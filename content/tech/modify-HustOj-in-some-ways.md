+++
Description = "对HustOJ系统做的一些修改"
Tags = ["HustOJ","修改"]
date = "2013-05-11T16:43:03+08:00"
title = "对HustOj方面做的修改"

+++
1、替换了logo.png图片
2、修改了reinfo.php(显示编译错误文件)文件，用户在答案错误后不能看到错误答案，只有administrator用户才能看到
3、修改了/boot/css/bootstrap.css 文件，使管理用户的input框加上虚线，看着更清晰
4、修改了/include/profile.php文件，关闭了用户注册页面。
5、修改了一部分的faqs.cn.php文件，增加了程序结束时的说明
6、增加班级管理功能：
a.在增加一个ClassRelation的表，共有3个字段，ID，Grade（年级），classname(班级名称)。
b.在users后增加了一个字段class_id和上述表中的ID对应。
c.在/admin文件夹后增加了class_add.php,class_delete.php,修改了contest_add.php，使之可以按照班级管理竞赛。
7、修改了contest_add.php,将学号粘贴进去后，可以自动在user表中创建账户。
8.本地化了jquery.min.js，把这个文件放在了highlight/script/文件夹下，方便学校内网访问。
9.增加了phpMyAdmin数据库。用于数据库的管理和调试工作。
10.增加contest的删除功能，删除contest,同时删除和这个contest相关的数据，用户权限等等，页面是/admin/contest_delete.php
11.修改了/admin/menu.php 增加了首页 避免框架重复嵌套的bug、班级管理，增加班级 功能。
12.修改了页脚
14.修正了watch.php的功能。使之正常运行

TODO in Freatured：

1、班级管理目前一个同学只能对应一个班级，要是用户插入重复，则该同学班级不变.
2.目前的验证码还存在一些问题，有时会验证失败
3.竞赛的xls导出不能集成导出用户的班级
4.竞赛的源码导出不能打包导出
