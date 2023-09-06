---
layout: page
title: apt
parent: 技术文档
---

## 1. package 定义

    可以自动化安装配置和卸载的软件或者库。

    A package is a collection of files that allow for applications or libraries to be distributed via the package management system. The aim of packaging is to allow the automation of installing, upgrading, configuring, and removing computer programs for Debian in a consistent manner.

## 2. package 分类

    - binary packages

      二进制库包含可执行文件，默认配置，和其他执行需要的资源

      Binary packages contain the executables, standard configuration files, and other resources required for the executable to run.

    - source packages

      源文件包包含源代码，构建配置，运行时依赖列表，冲突的包，版权协议，软件初始配置等

      Source packages contain the upstream source code, configuration for the package build system, list of runtime dependencies and conflicting packages, a machine-readable description of copyright and license information, initial configuration for the software, and more.
