+++
Description = "使用Gitlab构建CI"
Tags = ["gitlab", "ci", "golang"]
date = "2015-12-21T21:58:31+08:00"
title = "使用Gitlab CI构建持续集成"

+++

本文主要讲述使用Gitlab配合Gitlab CI进行自动化部署的过程。本方案是[2015北京GDG DevFest](https://github.com/gopher-beijing/talks/tree/master/20151108-GDGBeijingDevFest)中水羽哲演讲的内容的一个简单实现，适合的是我们自己随手搭建主机的实现方案。
使用gitlab 8.2.3 构建，这个版本的Gitlab已经将CI直接融入到了UI中，不再是以前的两个版本的方案了，所以使用的时候请看清楚软件版本。

## 概念
首先有几个概念，持续集成(Continous Integration)，持续交付(Continous Delivery)，持续部署(Continous Deployment)，我这里参考的是[阮一峰的网络日志](http://www.ruanyifeng.com/blog/2015/09/continuous-integration.html)来进行学习的。

在持续集成之前，还有相关的工作流，如git workflow/github workflow， 这些都是需要了解的，我也是参考[阮一峰的网络日志](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html), 不同的工作流之间有一定的区别，但是都是运用Git强大的分支能力，进行工作流的处理。再次吐槽一下把Git当SVN用的。

在工作流中引入持续集成，并且使用Docker来进行快速的部署，能够快速提升部署能力，稳定、快速的进行测试。目前国内很多云计算公司都提供这个服务，我所知道的如阿里云、Daocloud都有这个服务。

* Gitlab CI

[Gitlab CI](https://about.gitlab.com/gitlab-ci/) 是Gitlab提供的一套自动化持续集成的框架，架构如下图所示:

  ![Gitlab CI](https://about.gitlab.com/images/ci/arch-1.jpg)

它由一个Server端和多个Runner端构成，Server端就是安装Gitlab服务的机器，主要负责组织Runner运行，下发任务和收集结果。Runner端就是运行任务，只要安装了[Gitlan CI multi runner](https://gitlab.com/gitlab-org/gitlab-ci-multi-runner#installation)，并进行相关的配置，把Gitlab Server和Runner关联起来。配置的时候可以选择运行任务的方式，可以在本地直接运行，也可以ssh到别的机器上运行，还可以在Docker中运行，现在推荐使用Docker运行，能够进行任务以及环境的隔离。
有意思的是你还可以选择把Runner跑在Docker里面，这样的话每一个Docker负责一个任务，这也是可行的。只是相关环境需要自己集成一下，目前没有Golang + Gitlab CI的Dockerfile，只需要把Golang的Dockerfile和Gitlab CI Runner的Dockerfile集成一下，就能够实现这个功能，非常简单。

Runner有多种形式，可以由Gitlab管理员在后台设置的Runner，这种Runner是共享的，任何一个项目都可以使用这种Runner。还有一种Runner是在每一个项目里面自己设置的，进入项目 Setting->Runner，这里面就可以设置相关选项，是使用共享runner，还是自己搭建的Runner。


## 配置文件编写
要启动CI，只需要你在项目里面提交一个`.gitlab-ci.yml`的一个`YAML`文件，系统就会按照这个文件指定的方式去运行脚本。任务脚本的写法可以参见[Configuration of your builds with .gitlab-ci.yml](http://docs.gitlab.com/ce/ci/yaml/README.html)。默认的，CI把工作分成了3个阶段，build/test/deploy，build主要进行一些准备工作，环境变量的设置，依赖的更新等工作；test主要跑测试脚本，这点上就可以配合Golang的`go test`，或者Golang别的测试库进行。完成之后就可以进行deploy，deploy工作就能够进行具体的部署，部署到别的环境中。
这几个步骤中，只要有一个步骤失败了，就不会执行之后的步骤。每一次代码的提交都会触发CI的执行。GitLab CI 一旦配置完成之后，剩下的事情就比较的简单了，就直接看输出就好了。


## 总结
这篇文章之前酝酿了好久，都不知道怎么写，结果憋了半年憋出了这点。好多东西都忘了，果然知识库还是很重要的。主要是把这篇文章记下来，以后做再次使用Gitlab
CI的时候对这个有一个概念。
目前Github上很多项目都有CI，也有很多开源的CI框架，推荐大家在项目中引入CI，这样就可以一条龙服务了。

更新一下Hugo版本，换换CSS什么的很重要。
