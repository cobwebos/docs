---
title: Azure SQL 数据仓库 Striim 快速入门 | Microsoft Docs
description: 快速开始使用 Striim 和 Azure SQL 数据仓库。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 8ed9936884a648d736942caecade2ac3c2980e67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873399"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Striim Azure SQL DW 市场产品/服务安装指南

本快速入门教程假定已有 SQL 数据仓库的实例。

在 Azure 市场中搜索 Striim，并选择“用于将数据集成到 SQL 数据仓库（已暂存）的 Striim”选项 

![安装 Striim][install]

使用指定的属性配置 Striim VM，记下 Striim 群集名称、密码和管理员密码

![配置 Striim][configure]

部署完成后，单击\<VM 名称 >-masternode 在 Azure 门户中，单击连接，并复制 VM 本地帐户的登录名 

![将 Striim 连接到 SQL 数据仓库][connect]

将 sqljdbc42.jar 从 <https://www.microsoft.com/en-us/download/details.aspx?id=54671> 下载到本地计算机。 

打开命令行窗口，并将目录更改为下载 JDBC jar 的位置。 将 jar 文件 SCP 到 Striim VM，从 Azure 门户中获取地址和密码

![将 jar 文件复制到 VM 中][copy-jar]

打开另一个命令行窗口，或使用 ssh 实用工具 ssh 到 Striim 群集

![通过 SSH 连接到群集][ssh]

执行以下命令，将 JDBC jar 文件移动到 Striim 的 lib 目录，然后启动和停止服务器。

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-node
   5. systemctl stop striim-dbms
   6. systemctl start striim-dbms
   7. systemctl start striim-node

![Start the Striim cluster][start-striim]

现在，打开偏好的浏览器并导航到\<DNS 名称 >: 9080

![导航到登录屏幕][navigate]

使用 Azure 门户中设置的用户名和密码，然后选择偏好的向导开始，或转到“应用”页，开始使用拖放 UI

![使用服务器凭据登录][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
