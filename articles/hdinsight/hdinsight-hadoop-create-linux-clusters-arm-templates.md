---
title: 使用模板创建 Apache Hadoop 群集-Azure HDInsight
description: 了解如何使用 Resource Manager 模板创建 HDInsight 的群集
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 533fe0f391779a5bbe53bf7f2a136c3e9fb19017
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86080612"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>使用资源管理器模板在 HDInsight 中创建 Apache Hadoop 群集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

本文介绍几种使用 [azure 资源管理器模板](../azure-resource-manager/templates/deploy-powershell.md)创建 azure HDInsight 群集的方法。 若要了解其他群集创建工具和功能，请单击此页顶部的选项卡选择器。 另请参阅 [群集创建方法](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods)。

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="resource-manager-templates"></a>Resource Manager 模板

通过资源管理器模板，可轻松地通过单个协调操作为应用程序创建以下资源：

* HDInsight 群集及其依赖资源（例如默认存储帐户）。
* 其他资源（例如要使用 [Apache Sqoop](https://sqoop.apache.org/) 的 Azure SQL 数据库）。

在此模板中，定义应用程序所需的资源。 还可指定部署参数以输入不同环境的值。 模板中包含用于为部署构造值的 JSON 和表达式。

可以在 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?term=hdinsight)中找到 HDInsight 模板示例。 将跨平台 [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) 与[资源管理器扩展](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)或文本编辑器配合使用，将模板保存到工作站上的文件中。

有关资源管理器模板的详细信息，请参阅以下文章和示例：

* [创作 Azure Resource Manager 模板](../azure-resource-manager/templates/template-syntax.md)
* [使用 Azure Resource Manager 模板部署应用程序](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions) 模板引用
* [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>生成模板

使用资源管理器可利用不同工具从订阅中的现有资源导出资源管理器模板。 可以使用该生成的模板了解模板语法，或根据需要自动重新部署解决方案。 有关详细信息，请参阅[导出模板](../azure-resource-manager/templates/export-template-portal.md)。

## <a name="deploy-using-the-portal"></a>使用门户进行部署

可以使用 Azure 门户部署资源管理器模板。 有关详细信息，请参阅[从自定义模板部署资源](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)。

## <a name="deploy-using-powershell"></a>使用 PowerShell 进行部署

可以使用 Azure PowerShell 部署资源管理器模板。 有关详细信息，请参阅[使用资源管理器模板和 Azure PowerShell 部署资源](../azure-resource-manager/templates/deploy-powershell.md)和[使用 SAS 令牌和 Azure PowerShell 部署专用资源管理器模板](../azure-resource-manager/resource-manager-powershell-sas-token.md)。

## <a name="deploy-using-azure-cli"></a>使用 Azure CLI 进行部署

可以使用 Azure CLI 部署资源管理器模板。 有关详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../azure-resource-manager/templates/deploy-cli.md)和[使用 SAS 令牌和 Azure CLI 部署专用资源管理器模板](../azure-resource-manager/resource-manager-cli-sas-token.md)。

## <a name="deploy-using-the-rest-api"></a>使用 REST API 进行部署

可以使用 REST API 部署资源管理器模板。 有关详细信息，请参阅[使用资源管理器模板和资源管理器 REST API 部署资源](../azure-resource-manager/templates/deploy-rest.md)。

## <a name="deploy-with-visual-studio"></a>使用 Visual Studio 进行部署

 使用 Visual Studio 创建一个资源组项目，并通过用户界面将其部署到 Azure。 选择要包含在项目中的资源类型。 这些资源会自动添加到 Resource Manager 模板。 该项目还提供了用于部署模板的 PowerShell 脚本。

有关将 Visual Studio 用于资源组的简介，请参阅[通过 Visual Studio 创建和部署 Azure 资源组](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。

## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](hdinsight-hadoop-customize-cluster-linux.md#access-control)。

## <a name="next-steps"></a>后续步骤

在本文中，已经学习了几种创建 HDInsight 群集的方法。 要了解更多信息，请参阅下列文章：

* 有关 HDInsight 相关模板的详细信息，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?term=hdinsight)。
* 有关通过 .NET 客户端库部署资源的示例，请参阅[使用 .NET 库和模板部署资源](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 有关部署应用程序的详细示例，请参阅[按可预见的方式在 Azure 中预配和部署微服务](../app-service/deploy-complex-application-predictably.md)。
* 有关将解决方案部署到不同环境的指南，请参阅 [Microsoft Azure 中的开发和测试环境](../solution-dev-test-environments.md)。
* 若要了解 Azure Resource Manager 模板的节，请参阅[创作模板](../azure-resource-manager/templates/template-syntax.md)。
* 有关可在 Azure Resource Manager 模板中使用的函数列表，请参阅[模板函数](../azure-resource-manager/templates/template-functions.md)。
