---
title: include 文件
description: include 文件
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 0b2831321c9f4d855a19605c1ce5ace9fa11c88b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89596102"
---
| 域名                  | 出站端口 | 说明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | 自承载集成运行时连接到 Azure 数据工厂中的数据移动服务时需要此端口。 |
| `{datafactory}.{region}.datafactory.azure.net`<br> 或 `*.frontend.clouddatahub.net` | 443            | 自承载集成运行时连接到数据工厂服务时需要此端口。 <br>对于新创建的数据工厂，请在自承载集成运行时密钥中查找 FQDN，其格式为 {datafactory}.{region}.datafactory.azure.net。 对于旧数据工厂，如果在自承载集成密钥中找不到 FQDN，请使用 *.frontend.clouddatahub.net。 |
| `download.microsoft.com`    | 443            | 自承载集成运行时下载更新时需要此端口。 如果已禁用自动更新，则可以跳过对此域的配置。 |
| `*.core.windows.net`          | 443            | 使用[分阶段复制](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)功能时，由自承载集成运行时用来连接到 Azure 存储帐户。 |
| `*.database.windows.net`      | 1433           | 仅当从或向 Azure SQL 数据库或 Azure Synapse Analytics 复制时才是必需的，否则为可选。 在不打开端口 1433 的情况下，使用暂存复制功能将数据复制到 SQL 数据库或 Synapse Analytics。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | 仅当从/向 Azure Data Lake Store 复制时才是必需的，否则为可选。 |
