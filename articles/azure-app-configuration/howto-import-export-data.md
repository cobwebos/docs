---
title: 通过 Azure 应用程序配置导入或导出数据
description: 了解如何在 Azure 应用配置中导入或导出配置数据。 在应用配置存储和代码项目之间交换数据。
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: lcozzens
ms.openlocfilehash: 21eba653bcd853db9550d0d3781aacd281884605
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88588069"
---
# <a name="import-or-export-configuration-data"></a>导入或导出配置数据

Azure 应用程序配置支持数据导入和导出操作。 使用这些操作可以批量处理配置数据，并在应用程序配置存储和代码项目之间交换数据。 例如，可以设置一个“应用程序配置”存储用于测试，另一个用于生产。 可以在它们之间复制应用程序设置，这样就不必两次输入数据。

本文提供了使用应用程序配置导入和导出数据的相关指导信息。 如果要设置与 GitHub 存储库的持续同步，请参阅我们的 [GitHub 操作](https://aka.ms/azconfig-gha1)。

## <a name="import-data"></a>导入数据

“导入”会将配置数据引入到现有源的应用程序配置存储中。 使用导入功能将数据迁移到应用程序配置存储中，或聚合来自多个源的数据。 应用程序配置支持从 JSON、YAML 或属性文件导入。

使用 [Azure 门户](https://portal.azure.com)或 [Azure CLI](./scripts/cli-import.md) 导入数据。 在 Azure 门户中执行以下步骤：

1. 浏览到应用程序配置存储，从“操作”菜单中选择“导入/导出” 。

1. 在“导入”选项卡上，选择“源服务” > “配置文件”  。

1. 选择“For 语言”，并选择所需的输入类型。

1. 选择“文件夹”图标，然后浏览到要导入的文件。

    ![导入文件](./media/import-file.png)

1. 选择“分隔符”，并选择性地输入“前缀”以用于导入的键名 。

1. 还可以选择“标签”。

1. 选择“应用”以完成导入。

    ![文件导入已完成](./media/import-file-complete.png)

## <a name="export-data"></a>导出数据

“导出”会将存储在应用程序配置中的配置数据写入另一个目标。 例如，使用导出功能将应用程序配置存储中的数据保存到在部署期间使用应用程序代码嵌入的文件中。

使用 [Azure 门户](https://portal.azure.com)或 [Azure CLI](./scripts/cli-export.md) 导出数据。 在 Azure 门户中执行以下步骤：

1. 浏览到应用程序配置存储，选择“导入/导出”。

1. 在“导出”选项卡上，选择“目标服务” > “配置文件”  。

1. 选择性地输入“前缀”，并为要导出的键选择“标签”和时间点 。

1. 选择“文件类型” > “分隔符” 。

1. 选择“应用”以完成导出。

    ![导出文件已完成](./media/export-file-complete.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建一个 ASP.NET Core Web 应用](./quickstart-aspnet-core-app.md)  
