---
title: 通过 Azure 应用配置导入或导出数据 |Microsoft Docs
description: 了解如何在 Azure 应用配置中导入或导出数据
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 9780dc34aa6b146fe62b11586cbab46825e60535
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185155"
---
# <a name="import-or-export-configuration-data"></a>导入或导出配置数据

Azure 应用配置支持数据导入和导出操作。 使用这些操作可以在应用配置存储和代码项目之间批量处理配置数据和交换数据。 例如，可以设置一个应用配置存储用于测试，另一个用于生产。 然后，您可以通过文件在它们之间复制应用程序设置，这样就无需两次输入数据。

本文提供了有关使用应用配置导入和导出数据的指南。

## <a name="import-data"></a>导入数据

"导入" 会将配置数据从现有源引入应用配置存储，而不是手动输入。 使用 import 函数将数据迁移到应用配置存储，或聚合多个源中的数据。 应用配置支持从 JSON、YAML 或属性文件导入。

使用[Azure 门户](https://portal.azure.com)或[Azure CLI](./scripts/cli-import.md)导入数据。 在 Azure 门户中执行以下步骤：

1. 浏览到应用配置存储，并选择 "**导入/导出**"。

2. 在 "**导入**" 选项卡上，选择 "**源服务** > **配置文件**"。

3. 选择 "**对于语言** > **文件类型**"。

4. 选择**文件夹**图标，并浏览到要导入的文件。

    ![导入文件](./media/import-file.png)

5. 选择一个**分隔符**，并根据需要输入用于导入密钥名称的**前缀**。

6. 还可以选择一个**标签**。

7. 选择 "**应用**" 以完成导入。

    ![导入文件已完成](./media/import-file-complete.png)

## <a name="export-data"></a>导出数据

导出将存储在应用配置中的配置数据写入另一个目标。 例如，使用导出功能将应用配置存储中的数据保存到在部署期间嵌入到应用程序代码中的文件。

使用[Azure 门户](https://portal.azure.com)或[Azure CLI](./scripts/cli-export.md)导出数据。 在 Azure 门户中执行以下步骤：

1. 浏览到应用配置存储，并选择 "**导入/导出**"。

2. 在 "**导出**" 选项卡上，选择 "**目标服务** > **配置文件**"。

3. 根据需要输入**前缀**，并选择要导出的密钥的**标签**和时间点。

4. 选择 > **分隔符**的**文件类型**。

5. 选择 "**应用**" 以完成导出。

    ![导出文件已完成](./media/export-file-complete.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建一个 ASP.NET Core Web 应用](./quickstart-aspnet-core-app.md)  
