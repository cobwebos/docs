---
title: "了解如何使用 Hadoop 沙盒 - 仿真器 - Azure HDInsight | Microsoft Docs"
description: "若要了解如何使用 Hadoop 生态系统，可以在 Azure 虚拟机上设置 Hortonworks 提供的 Hadoop 沙盒。 "
keywords: "hadoop emulator,hadoop 沙盒"
editor: cgronlun
manager: jhubbard
services: hdinsight
author: nitinme
documentationcenter: 
tags: azure-portal
ms.assetid: 6ad5bb58-8215-4e3d-a07f-07fcd8839cc6
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 40706ca30795d72120ff3edd728087d025a8b6c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-a-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>在虚拟机上开始使用 Hadoop 沙盒和 Hadoop 仿真器

了解如何在虚拟机上安装 Hortonworks 提供的 Hadoop 沙盒，了解 Hadoop 生态系统。 该沙盒提供一个本地开发环境，让用户了解 Hadoop、Hadoop 分布式文件系统 (HDFS) 和作业提交内容。 熟悉 Hadoop 之后，便可以开始在 Azure 中使用 Hadoop 创建 HDInsight 群集。 有关如何入门的详细信息，请参阅[在 HDInsight 中开始使用 Hadoop](hdinsight-hadoop-linux-tutorial-get-started.md)。

## <a name="prerequisites"></a>先决条件
* [Oracle VirtualBox](https://www.virtualbox.org/)。 从[此处](https://www.virtualbox.org/wiki/Downloads)下载并安装它。



## <a name="download-and-install-the-virtual-machine"></a>下载并安装虚拟机
1. 浏览到 [Hortonworks 下载](http://hortonworks.com/downloads/#sandbox)。

2. 单击“下载 VirtualBox”，将最新的 Hortonworks 沙盒下载到 VM 上。 开始下载之前，系统会提示注册 Hortonworks。 下载需要一到两个小时，具体取决于网络速度。
   
    ![用于下载 Hortonworks Sandbox for VirtualBox 的链接图像](./media/hdinsight-hadoop-emulator-get-started/download-sandbox.png)
3. 在同一网页上，单击“在 Virtual Box 上导入”链接，下载包含虚拟机安装说明的 PDF。

若要下载较旧的 HDP 版本沙盒，请展开存档：

![Hortonworks 沙盒存档](./media/hdinsight-hadoop-emulator-get-started/hortonworks-sandbox-archive.png)


## <a name="start-the-virtual-machine"></a>启动虚拟机

1. 打开 Oracle VM VirtualBox。
2. 在“文件”菜单上，单击“导入设备”，并指定 Hortonworks 沙盒映像。
1. 选择 Hortonworks 沙盒，依次单击“启动”、“正常启动”。 虚拟机完成启动过程后，显示登录说明。
   
    ![正常启动](./media/hdinsight-hadoop-emulator-get-started/normal-start.png)
2. 打开 Web 浏览器并导航到显示的 URL（通常是 http://127.0.0.1:8888 ）。

## <a name="set-sandbox-passwords"></a>设置沙盒密码

1. 在“Hortonworks 沙盒”页的“开始”步骤中，选择“查看高级选项”。 使用此页上的信息通过 SSH 登录到沙盒。 使用提供的名称和密码。
   
   > [!NOTE]
   > 如果未安装 SSH 客户端，可以使用虚拟机在 **http://localhost:4200/** 上提供的基于 Web 的 SSH。
   > 
   
    首次使用 SSH 建立连接时，系统会提示更改根帐户的密码。 输入用 SSH 登录时使用的新密码。

2. 登录后，请输入以下命令：
   
        ambari-admin-password-reset
   
    出现提示时，请提供 Ambari 管理员帐户的密码。 访问 Ambari Web UI 时使用此密码。

## <a name="use-hive-commands"></a>使用 Hive 命令

1. 与沙盒建立 SSH 连接后，使用以下命令启动 Hive shell：
   
        hive
2. 启动 shell 后，使用以下命令查看随沙盒一起提供的表：
   
        show tables;
3. 使用以下命令检索 `sample_07` 表中的 10 行数据：
   
        select * from sample_07 limit 10;

## <a name="next-steps"></a>后续步骤
* [了解如何将 Visual Studio 与 Hortonworks 沙盒配合使用](hdinsight-hadoop-emulator-visual-studio.md)
* [学习 Hortonworks 沙盒的重要知识](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop 教程 - HDP 入门](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

