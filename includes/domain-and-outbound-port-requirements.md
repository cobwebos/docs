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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "74559301"
---
| 域名                  | 出站端口 | 描述                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | 自托管的集成运行时需要连接到 Azure 数据工厂中的数据移动服务。 |
| `*.frontend.clouddatahub.net` | 443            | 自承载集成运行时连接到数据工厂服务时需要此端口。 |
| `download.microsoft.com`    | 443            | 自承载集成运行时下载更新时需要此端口。 如果禁用了自动更新，可以跳过配置此域。 |
| `*.core.windows.net`          | 443            | 使用[分阶段复制](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)功能时，由自承载集成运行时用来连接到 Azure 存储帐户。 |
| `*.database.windows.net`      | 1433           | 仅当从 Azure SQL 数据库或 Azure SQL 数据仓库复制时才需要，否则即可进行可选操作。 使用暂存复制功能将数据复制到 SQL 数据库或 SQL 数据仓库，而无需打开端口 1433。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | 仅在从 Azure 数据湖存储复制时才需要，否则即可进行可选。 |
