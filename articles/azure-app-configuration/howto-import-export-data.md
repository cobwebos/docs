---
title: 使用 Azure 应用配置导入或导出数据
description: 了解如何将数据导入或导出到 Azure 应用配置或从 Azure 应用配置中
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 5b3d6d0561d7d6d6b23cb4f579b0988850da9771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056868"
---
# <a name="import-or-export-configuration-data"></a>导入或导出配置数据

Azure 应用配置支持数据导入和导出操作。 使用这些操作可批量处理配置数据，并在应用配置存储和代码项目之间交换数据。 例如，您可以设置一个应用配置存储用于测试，另一个设置为生产。 您可以在它们之间复制应用程序设置，这样您就不必输入数据两次。

本文提供了使用应用配置导入和导出数据的指南。 如果您想设置与 GitHub 存储库的持续同步，请查看我们的[GitHub 操作](https://aka.ms/azconfig-gha1)。

## <a name="import-data"></a>导入数据

导入会将配置数据从现有源引入应用配置存储。 使用导入函数将数据迁移到应用配置存储或聚合来自多个源的数据。 应用配置支持从 JSON、YAML 或属性文件导入。

使用[Azure 门户](https://portal.azure.com)或[Azure CLI](./scripts/cli-import.md)导入数据。 在 Azure 门户中执行以下步骤：

1. 浏览到应用配置商店，然后从 **"操作"** 菜单中选择 **"导入/导出**"。

1. 在 **"导入**"选项卡上，选择 **"源服务** > **配置文件**"。

1. 选择 **"为语言**"并选择所需的输入类型。

1. 选择 **"文件夹**"图标，然后浏览到要导入的文件。

    ![导入文件](./media/import-file.png)

1. 选择**分隔符**，并可以选择输入**前缀**以用于导入的键名称。

1. 或者，选择**标签**。

1. 选择 **"应用"** 以完成导入。

    ![导入文件已完成](./media/import-file-complete.png)

## <a name="export-data"></a>导出数据

将存储在应用配置中的配置数据导出到另一个目标。 例如，使用导出功能将数据保存在应用配置存储区中嵌入在部署期间嵌入的应用程序代码的文件。

使用[Azure 门户](https://portal.azure.com)或[Azure CLI](./scripts/cli-export.md)导出数据。 在 Azure 门户中执行以下步骤：

1. 浏览到应用配置商店，然后选择 **"导入/导出**"。

1. 在 **"导出**"选项卡上，选择 **"目标服务** > **配置文件**"。

1. 可选地输入**前缀**，并为要导出的键选择**标签**和时间点。

1. 选择**文件类型** > **分隔符**。

1. 选择 **"应用"** 以完成导出。

    ![导出文件已完成](./media/export-file-complete.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建一个 ASP.NET Core Web 应用](./quickstart-aspnet-core-app.md)  
