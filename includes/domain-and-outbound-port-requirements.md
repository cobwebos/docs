---
title: include 文件
description: include 文件
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "74559301"
---
| 域名                  | 出站端口 | 说明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | 自承载集成运行时要求连接到 Azure 数据工厂中的数据移动服务。 |
| `*.frontend.clouddatahub.net` | 443            | 自承载集成运行时连接到数据工厂服务时需要此端口。 |
| `download.microsoft.com`    | 443            | 自承载集成运行时下载更新时需要此端口。 如果已禁用自动更新，则可以跳过对此域的配置。 |
| `*.core.windows.net`          | 443            | 使用[分阶段复制](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)功能时，由自承载集成运行时用来连接到 Azure 存储帐户。 |
| `*.database.windows.net`      | 1433           | 仅当从或复制到 Azure SQL 数据库或 Azure SQL 数据仓库时才需要，否则为可选。 使用暂存复制功能可以将数据复制到 SQL 数据库或 SQL 数据仓库，而无需打开端口1433。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | 仅当从或复制到 Azure Data Lake Store 时是必需的，否则为可选。 |
