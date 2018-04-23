---
title: 使用模板创建 Hadoop 群集 - Azure HDInsight | Microsoft Docs
description: 了解如何使用 Resource Manager 模板创建 HDInsight 的群集
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00a80dea-011f-44f0-92a4-25d09db9d996
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 1c50c3fbef5495171693b64e410f858af6176147
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="create-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>使用 Resource Manager 模板在 HDInsight 中创建 Hadoop 群集
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

本文介绍几种使用 Azure 资源管理器模板创建 Azure HDInsight 群集的方式。 有关详细信息，请参阅[使用 Azure 资源管理器模板部署应用程序](../azure-resource-manager/resource-group-template-deploy.md)。 若要了解其他群集创建工具和功能，请在本页顶部单击选项卡选择器，或参阅[群集创建方法](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods)。

## <a name="prerequisites"></a>先决条件
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

要按照本文中的说明进行操作，需要：

* 一个 [Azure 订阅](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* Azure PowerShell 和/或 Azure CLI。

### <a name="resource-manager-templates"></a>资源管理器模板
通过资源管理器模板，可轻松地通过单个协调操作为应用程序创建以下资源：
* HDInsight 群集及其依赖资源（例如默认存储帐户）
* 其他资源（例如要使用 Apache Sqoop 的 Azure SQL 数据库）

在此模板中，定义应用程序所需的资源。 还可指定部署参数以输入不同环境的值。 模板中包含用于为部署构造值的 JSON 和表达式。

可在 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/?term=hdinsight)中找到 HDInsight 模板示例。 将跨平台 [Visual Studio 代码](https://code.visualstudio.com/#alt-downloads)与 [Resource Manager 扩展](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)或文本编辑器配合使用，将模板保存到工作站上的文件中。 

有关 Resource Manager 模板的详细信息，请参阅以下文章：

* [创作 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)
* [使用 Azure 资源管理器模板部署应用程序](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>生成模板

使用资源管理器可利用不同工具从订阅中的现有资源导出资源管理器模板。 可以使用该生成的模板了解模板语法，或根据需要自动重新部署解决方案。

- Azure 门户：请参阅[从现有资源导出 Azure 资源管理器模板](../azure-resource-manager/resource-manager-export-template.md)。
- Azure PowerShell：请参阅[使用 PowerShell 导出 Azure 资源管理器模板](../azure-resource-manager/resource-manager-export-template-powershell.md)。
- Azure CLI：请参阅[使用 Azure CLI 导出 Azure 资源管理器模板](../azure-resource-manager/resource-manager-export-template-cli.md)。


## <a name="deploy-using-the-portal"></a>使用门户进行部署

可以使用 Azure 门户部署资源管理器模板。 有关详细信息，请参阅[从自定义模板部署资源](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)。

## <a name="deploy-using-powershell"></a>使用 PowerShell 进行部署

可以使用 Azure PowerShell 部署资源管理器模板。 有关详细信息，请参阅[使用资源管理器模板和 Azure PowerShell 部署资源](../azure-resource-manager/resource-group-template-deploy.md)和[使用 SAS 令牌和 Azure PowerShell 部署专用资源管理器模板](../azure-resource-manager/resource-manager-powershell-sas-token.md)。

## <a name="deploy-using-cli"></a>使用 CLI 进行部署

可以使用 Azure CLI 部署资源管理器模板。 有关详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../azure-resource-manager/resource-group-template-deploy-cli.md)和[使用 SAS 令牌和 Azure CLI 部署专用资源管理器模板](../azure-resource-manager/resource-manager-cli-sas-token.md)。

## <a name="deploy-using-the-rest-api"></a>使用 REST API 进行部署
可以使用 REST API 部署资源管理器模板。 有关详细信息，请参阅[使用资源管理器模板和资源管理器 REST API 部署资源](../azure-resource-manager/resource-group-template-deploy-rest.md)。

## <a name="deploy-with-visual-studio"></a>使用 Visual Studio 进行部署
 使用 Visual Studio 创建一个资源组项目，并通过用户界面将其部署到 Azure。 选择要包含在项目中的资源类型。 这些资源会自动添加到 Resource Manager 模板。 该项目还提供了用于部署模板的 PowerShell 脚本。

有关将 Visual Studio 用于资源组的简介，请参阅[通过 Visual Studio 创建和部署 Azure 资源组](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](hdinsight-administer-use-portal-linux.md#create-clusters)。

## <a name="next-steps"></a>后续步骤
在本文中，已经学习了几种创建 HDInsight 群集的方法。 若要了解更多信息，请参阅下列文章：

* 有关更多与 HDInsight 相关的模板，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?term=hdinsight)。
* 有关通过 .NET 客户端库部署资源的示例，请参阅[使用 .NET 库和模板部署资源](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 有关部署应用程序的详细示例，请参阅[按可预见的方式在 Azure 中预配和部署微服务](../app-service/app-service-deploy-complex-application-predictably.md)。
* 有关将解决方案部署到不同环境的指南，请参阅 [Microsoft Azure 中的开发和测试环境](../solution-dev-test-environments.md)。
* 若要了解 Azure 资源管理器模板的节，请参阅[创作模板](../azure-resource-manager/resource-group-authoring-templates.md)。
* 有关可在 Azure 资源管理器模板中使用的函数列表，请参阅[模板函数](../azure-resource-manager/resource-group-template-functions.md)。
