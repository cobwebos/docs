---
title: 排查 Azure 数据工厂问题
description: 了解如何排查 Azure 数据工厂使用问题。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 38fd14c1-5bb7-4eef-a9f5-b289ff9a6942
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: a9a4f7b19d6ef08562d17474e252ce72fd0977dc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="troubleshoot-data-factory-issues"></a>排查数据工厂问题
> [!NOTE]
> 本文适用于 Azure 数据工厂版本 1（即正式版 (GA)）。 

本文提供有关 Azure 数据工厂使用问题的故障排除提示。 本文未列出使用该服务时可能出现的所有问题，但涵盖了部分问题和常见故障排除的提示。   

## <a name="troubleshooting-tips"></a>故障排除提示
### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>错误: 该订阅未注册为使用命名空间 'Microsoft.DataFactory'
出现此错误表示未在计算机上注册 Azure 数据工厂资源提供程序。 请执行以下操作：

1. 启动 Azure PowerShell。
2. 使用以下命令登录到 Azure 帐户。

    ```powershell
    Connect-AzureRmAccount
    ```
3. 运行以下命令来注册 Azure 数据工厂提供程序。

    ```powershell        
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>问题：运行数据工厂 cmdlet 时出现未授权错误
可能未在 Azure PowerShell 中使用正确的 Azure 帐户或订阅。 使用以下 cmdlet 选择要在 Azure PowerShell 中使用的正确 Azure 帐户和订阅。

1. Connect-AzureRmAccount - 使用正确的用户 ID 和密码
2. Get-AzureRmSubscription - 查看帐户的所有订阅。
3. Select-AzureRmSubscription &lt;订阅名称&gt; - 选择正确的订阅。 使用在 Azure 门户中创建数据工厂时所用的同一个订阅。

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>问题：无法从 Azure 门户启动数据管理网关快速安装
快速安装数据管理网关需要使用 Internet Explorer 或者与 Microsoft ClickOnce 兼容的 Web 浏览器。 如果快速安装无法启动，请执行以下操作之一：

* 请使用 Internet Explorer 或与 Microsoft ClickOnce 兼容的 Web 浏览器。

    如果使用 Chrome，请转到 [Chrome 网上应用店](https://chrome.google.com/webstore/)，使用“ClickOnce”关键字进行搜索，选择 ClickOnce 扩展之一并安装它。

    对于 Firefox，请执行相同的操作（安装外接程序）。 在工具栏上单击“打开菜单”按钮（右上角的三条横线），单击“扩展程序”，使用“ClickOnce”关键字进行搜索，选择一个 ClickOnce 扩展并安装它。
* 使用门户中同一边栏选项卡上所示的“手动安装”链接。 使用此方法手动下载和运行安装文件。 安装成功后，会看到“数据管理网关配置”对话框。 从门户屏幕中复制**密钥**，并在配置管理器中使用该密钥将网关手动注册到服务。  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>问题：无法连接到本地 SQL Server
在网关计算机上启动“数据管理网关”，并使用“疑难解答”选项卡测试从网关计算机到 SQL Server 的链接。 请参阅[网关问题故障排除](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)，了解连接/网关相关问题的故障排除提示。   

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>问题：输入切片始终处于等待状态
切片处于“等待”状态的原因有多种。 其中一个常见原因是 **external** 属性未设置为 **true**。 在 Azure 数据工厂范围外部生成的任何数据集都应标记为 **external** 属性。 此属性表示该数据是外部的，且不由数据工厂内的任何管道提供支持。 当数据出现在相应的存储中后，数据切片将标记为 **Ready**。

有关 **external** 属性的用法，请参阅以下示例。 将 external 设置为 true 时，可以选择指定 **externalData** \*。

有关此属性的更多详细信息，请参阅[数据集](data-factory-create-datasets.md)一文。

```json
{
  "name": "CustomerTable",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "MyLinkedService",
    "typeProperties": {
      "folderPath": "MyContainer/MySubFolder/",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      }
    }
  }
}
```

要解决该错误，请将 **external** 属性和可选的 **externalData** 节添加到输入表中的 JSON 定义中，然后重新创建表。

### <a name="problem-hybrid-copy-operation-fails"></a>问题：混合复制操作失败
有关使用数据管理网关向/从本地数据存储复制数据时的问题进行故障排除的步骤，请参阅[排查网关问题](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)。

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>问题：按需 HDInsight 预配失败
使用 HDInsightOnDemand 类型的链接服务时，需指定指向 Azure Blob 存储的 linkedServiceName。 数据工厂服务使用此存储来存储按需 HDInsight 群集的日志和支持文件。  有时，预配按需 HDInsight 群集会失败并出现以下错误：

```
Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.
```

此错误通常表示 linkedServiceName 中指定的存储帐户的位置，不是预配 HDInsight 时所在的同一个数据中心位置。 示例：如果数据工厂在美国西部，而 Azure 存储在美国东部，则美国西部的按需预配会失败。

此外，在另一个 JSON 属性 additionalLinkedServiceNames 中，可能指定了按需 HDInsight 中的其他存储帐户。 其他这些链接存储帐户应与 HDInsight 群集位于同一位置，否则操作会失败并出现相同错误。

### <a name="problem-custom-net-activity-fails"></a>问题：自定义 .NET 活动失败
有关详细步骤，请参阅[使用自定义活动调试管道](data-factory-use-custom-activities.md#troubleshoot-failures)。

## <a name="use-azure-portal-to-troubleshoot"></a>使用 Azure 门户进行故障排除
### <a name="using-portal-blades"></a>使用门户边栏选项卡
有关步骤，请参阅[监视管道](data-factory-build-your-first-pipeline-using-editor.md#monitor-a-pipeline)。

### <a name="using-monitor-and-manage-app"></a>使用监视和管理应用
有关详细信息，请参阅[使用监视和管理应用监视和管理数据工厂管道](data-factory-monitor-manage-app.md)。

## <a name="use-azure-powershell-to-troubleshoot"></a>使用 Azure PowerShell 进行故障排除
### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>使用 Azure PowerShell 排查错误
有关详细信息，请参阅[使用 Azure PowerShell 监视数据工厂管道](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline)。

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
