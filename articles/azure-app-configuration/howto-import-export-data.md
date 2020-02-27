---
title: 通过 Azure 应用配置导入或导出数据
description: 了解如何在 Azure 应用配置中导入或导出数据
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 2c074cbd99620a482b18cbe2dfcce8f987d78bd5
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619203"
---
# <a name="import-or-export-configuration-data"></a>导入或导出配置数据

Azure 应用配置支持数据导入和导出操作。 使用这些操作可以在应用配置存储和代码项目之间批量处理配置数据和交换数据。 例如，可以设置一个应用配置存储用于测试，另一个用于生产。 可以在它们之间复制应用程序设置，这样就无需两次输入数据。

本文提供了有关使用应用配置导入和导出数据的指南。

## <a name="import-data"></a>导入数据

"导入" 会将配置数据引入现有源的应用配置存储中。 使用 import 函数将数据迁移到应用配置存储，或聚合多个源中的数据。 应用配置支持从 JSON、YAML 或属性文件导入。

使用[Azure 门户](https://portal.azure.com)或[Azure CLI](./scripts/cli-import.md)导入数据。 在 Azure 门户中执行以下步骤：

1. 浏览到应用配置存储，并从 "**操作**" 菜单中选择 "**导入/导出**"。

1. 在 "**导入**" 选项卡上，选择 "**源服务** > **配置文件**"。

1. 选择 "语言" 并选择所需**的**输入类型。

1. 选择**文件夹**图标，并浏览到要导入的文件。

    ![导入文件](./media/import-file.png)

1. 选择一个**分隔符**，并根据需要输入用于导入密钥名称的**前缀**。

1. 还可以选择一个**标签**。

1. 选择 "**应用**" 以完成导入。

    ![导入文件已完成](./media/import-file-complete.png)

## <a name="export-data"></a>导出数据

导出将存储在应用配置中的配置数据写入另一个目标。 例如，使用导出功能将应用配置存储中的数据保存到在部署期间嵌入到应用程序代码中的文件。

使用[Azure 门户](https://portal.azure.com)或[Azure CLI](./scripts/cli-export.md)导出数据。 在 Azure 门户中执行以下步骤：

1. 浏览到应用配置存储，并选择 "**导入/导出**"。

1. 在 "**导出**" 选项卡上，选择 "**目标服务** > **配置文件**"。

1. 根据需要输入**前缀**，并选择要导出的密钥的**标签**和时间点。

1. 选择 > **分隔符**的**文件类型**。

1. 选择 "**应用**" 以完成导出。

    ![导出文件已完成](./media/export-file-complete.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建一个 ASP.NET Core Web 应用](./quickstart-aspnet-core-app.md)  
