+++
Description = "使用gitlab构建ci golang"
Tags = ["gitlab", "ci", "golang"]
date = "2015-12-21T21:58:31+08:00"
title = "使用gitlab构建golang CI持续集成(1)"

+++

本文主要讲述使用gitlab配合gitlab-ci进行自动化部署的过程。本方案是[2015北京GDG DevFest](https://github.com/gopher-beijing/talks/tree/master/20151108-GDGBeijingDevFest)中水羽哲演讲的内容的一个简单实现，适合的是我们自己随手搭建主机的实现方案。
使用gitlab 8.2.3 构建，这个版本的gitlab已经将CI直接融入到了UI中，不再是以前的两个版本的方案了，所以使用的时候请看清楚软件版本。

## 概念
* CI(Continuous Integration)

