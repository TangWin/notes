[TOC]

# profile 概念

开发过程中有环境之分，如测试、预发、正式等，针对每个环境一般都会有不同的配置，profile用来解决多环境配置的难点，只需要指定激活条件，就能激活指定的配置，实现多环境不同配置。



###  Spring boot中的profile

在Spring boot中，配置文件为：**application.properties**，若为多环境配置，则其他配置名称格式为：application-{profile}.properties（**必须**），在 application.properties 中通过 **spring.profiles.active={profile}** 指定当前加载的配置。