---
title: 基于gitlab ci的前端持续集成/持续部署（CI/CD）
date: 2018-12-1 11:07:08
tags: 持续集成/持续部署
---

#### 什么是持续集成/持续部署（CI/CD）？
个人理解，说白了就是把代码测试、打包、发布等工作交给一些工具来自动完成。这样可以提高效率，减少失误，开发人员只需要关心开发和提交代码到`git`就可以了。

#### 怎么做？
* 方式一： 使用`web hooks`,这种方式的原理就是在`gitlab`项目的`Setting-Integrations`设置中增加一个请求`url`和一个`secret`，如下图
![](https://user-gold-cdn.xitu.io/2018/12/1/1676572b0b918f22?w=1782&h=954&f=png&s=122760)
当触发钩子条件时，（一般是`git push`之后），`gitlab`会主动把`secret`带在请求头中去请求前面的`url`，后端服务接受到这个请求后并验证`secret`后，就可以为所欲为啦。这种方式不需要配置持续集成工具，但是需要自己单独专门写一个后端服务。由于今天主要使用下面的方式二，所以方式一的具体做法就不展开了，可以参考[Gitlab Webhooks自动化部署实战](https://www.jianshu.com/p/d0d601af3797)
* 方式二： 使用工具如`gitlab-CI`,这种方式的原理就是在自己的服务器上注册`gitlab-runner`时，会有一个`token`，服务器上运行`gitlab-runner`后，`runner`会轮询的发送带`token`的`http`请求给`gitlab`,如果`gitlab`有任务了，（一般是`git push`），那么会把任务信息返回给`runner`，然后`runner`就开始调用注册时选的`Executor`（我是用的`shell`）来执行项目根目录下的配置文件`.gitlab-ci.yml`，执行后把结果反馈给`gitlab`。详细的工作原理请移步[当谈到 GitLab CI 的时候，我们该聊些什么](https://tech.upyun.com/article/245/%E5%BD%93%E8%B0%88%E5%88%B0%20GitLab%20CI%20%E7%9A%84%E6%97%B6%E5%80%99%EF%BC%8C%E6%88%91%E4%BB%AC%E8%AF%A5%E8%81%8A%E4%BA%9B%E4%BB%80%E4%B9%88%EF%BC%88%E4%B8%8A%E7%AF%87%EF%BC%89.html)。下面说说我个人的使用步骤：

    1. 在开发环境（如自己的windows上）开发前端项目（该项目我命名为`ci-test`），并推送到`gitlab`远程仓库中。这里是用`vue-cli 3.0`生成的`vue`项目，我个人习惯增加如下自定义配置
        ```
        //vue.config.js
        module.exports = {
            outputDir: 'app-page',          //自定义打包后的目录名，注意在.gitnore文件中也要忽略掉该目录
            baseUrl: './'                   //打包时使用相对路径
        }
        ```
    2. 服务器上配置`nginx`，并配置默认访问目录，例如我自己的配置是`/app`文件夹。具体做法请自行搜索。
    3. 安装`node`和`git`，并在`/app`目录下克隆`gitlab`仓库中的代码，这时候服务器上就存在`/app/ci-test`目录了
    4. 服务器上安装`gitlab-runner`，具体方法请参考[官方文档](https://docs.gitlab.com/runner/install/)
    5. 服务器上注册一个`Runner`,具体方法参考[官方文档](https://docs.gitlab.com/runner/register/index.html),注意这里要填的`url(一般是 https://gitlab.com)`和`token`，都在`gitlab`项目下的`setting-CD/CD-Runners-Specific Runners`里面找(如下图)，

        ![](https://user-gold-cdn.xitu.io/2018/12/1/1676560365798880?w=1739&h=936&f=png&s=141865)
        而且要填的`tags`也是有用的，后面的`.gitlab-ci.yml`里面的`tag`选项必须是这里的`tags`里面的子项，最后`executor`这里我是填的`shell`
    6. 注册后修改`gitlab-runner`的权限
        ```
        sudo chown -R gitlab-runner:gitlab-runner /home/gitlab-runner
        sudo chmod -R 777 /home/gitlab-runner
        ```
    7. 开启`gitlab-runner`服务
        ```
        gitlab-runner run
        ```
        成功的话，会有一个绿点，如下图
        ![](https://user-gold-cdn.xitu.io/2018/12/1/1676562384f17f9f?w=1817&h=939&f=png&s=142855)
    8. 在`vue`项目根目录下配置`.gitlab-ci.yml`文件，具体配置选项请看[文档](https://docs.gitlab.com/ce/ci/yaml/README.html)。我的配置如下：
        ```
            #当Runner通过轮询检测到gitlab上有任务时，就会执行这个文件
            #个人不是很熟yml的语法以及详细配置，都是找猫画虎的，求各位大神提出优化意见

            stages:
                - update
                - test
                - build

            #更新代码并且安装依赖
            update:     
                stage: update
                script:
                    - cd /app/ci-test       #先进入到项目目录下，下面同理
                    - git pull
                    - sudo npm install
                tags:                       #这里的tags一定要属于注册时填的tags中，下面同理
                    - update
            
            #执行单元测试
            test:
                stage: test
                script:
                    - cd /app/ci-test
                    - npm run test:unit
                tags:
                    - unitTest
            
            #打包
            build:
                stage: build
                script:
                    - cd /app/ci-test
                    - npm run build
                tags: 
                    - build

        ```
    9. 将`.gitlab-ci.yml`文件推送到`gitlab`仓库中，在`gitlab`项目页面中打开`CI/CD-Pipelines`，即可看到效果，如下图
    
        ![](https://user-gold-cdn.xitu.io/2018/12/1/16765648b16d549c?w=1826&h=896&f=png&s=145348)
        `status`那一列如果是绿色的`passed`则说明大功告成，以后每次只需要提交代码即可，再也不用手动测试部署了。这个时候去浏览器中打开相应的地址，就可以看到部署成功。我的是这样的：
        ![](https://user-gold-cdn.xitu.io/2018/12/1/1676569b2de26bc1?w=1919&h=1000&f=png&s=64030)
        如果是红色的`failed`则说明执行某一个命令的时候出错了，这时候就会收到一封邮件告知失败原因。
    



#### 参考
* [基于Gitlab CI搭建持续集成环境](https://www.jianshu.com/p/705428ca1410)
* [Gitlab Webhooks自动化部署实战](https://www.jianshu.com/p/d0d601af3797)
* [gitlab-ci实现前端自动化部署（步骤全面）](https://blog.csdn.net/qq_40699305/article/details/82753506)
* [当谈到 GitLab CI 的时候，我们该聊些什么](https://tech.upyun.com/article/245/%E5%BD%93%E8%B0%88%E5%88%B0%20GitLab%20CI%20%E7%9A%84%E6%97%B6%E5%80%99%EF%BC%8C%E6%88%91%E4%BB%AC%E8%AF%A5%E8%81%8A%E4%BA%9B%E4%BB%80%E4%B9%88%EF%BC%88%E4%B8%8A%E7%AF%87%EF%BC%89.html)



