---
title: include 文件
description: include 文件
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f634ebb4d8c3f443b5d843b0093c955b384a9f5f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285607"
---
| 域名                  | 出站端口 | 描述                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | 自承载集成运行时连接到数据工厂中的数据移动服务时需要此端口。 |
| `*.frontend.clouddatahub.net` | 443            | 自承载集成运行时连接到数据工厂服务时需要此端口。 |
| `download.microsoft.com`    | 443            | 自承载集成运行时下载更新时需要此端口。 如果已禁用自动更新，则可以跳过此设置。 |
| `*.core.windows.net`          | 443            | 使用[分阶段复制](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)功能时，由自承载集成运行时用来连接到 Azure 存储帐户。 |
| `*.database.windows.net`      | 1433           | （可选）从/向 Azure SQL 数据库或 Azure SQL 数据仓库复制时需要。 在不打开端口 1433 的情况下，使用暂存复制功能将数据复制到 Azure SQL 数据库或 Azure SQL 数据仓库。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | （可选）从/向 Azure Data Lake Store 复制时需要。 |
