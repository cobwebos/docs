---
title: "为 Azure Stack 开发模板 | Microsoft 文档"
description: "了解 Azure Stack 模板的最佳做法"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: f85875b5b128f53d45fe9af97c026fc6e34b2d27
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="azure-resource-manager-template-considerations"></a>Azure 资源管理器模板注意事项

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

开发应用程序时，请务必确保模板可在 Azure 和 Azure Stack 之间移植。 这篇文章提供有关开发 Azure 资源管理器注意事项[模板](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)，因此你可以原型你应用程序和测试 Azure 中部署而无需对 Azure 堆栈环境的访问。

## <a name="resource-provider-availability"></a>资源提供程序的可用性
你打算部署的模板必须仅使用 Microsoft Azure 服务中已可用或在 Azure 堆栈中的预览。

## <a name="public-namespaces"></a>公共命名空间
由于 Azure Stack 托管在数据中心中，它的服务终结点命名空间与 Azure 公有云不同。 因此，Azure 资源管理器模板中的硬编码公共终结点失败时尝试将它们部署到 Azure 堆栈。 但是，可以使用 *reference* 和 *concatenate* 函数基于在部署过程中从资源提供程序检索到的值动态构建服务终结点。 例如，无需在模板中指定 *blob.core.windows.net*，检索 [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) 可动态设置 *osDisk.URI* 终结点：

     "osDisk": {"name": "osdisk","vhd": {"uri": 
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>API 版本控制
Azure 服务版本在 Azure 和 Azure Stack 之间可能有所不同。 每个资源都需要有 apiVersion 属性，用于定义所提供的功能。 若要确保 Azure 堆栈中的 API 版本兼容性，以下是有效的每个资源提供程序的 API 版本：

| 资源提供程序 | apiVersion |
| --- | --- |
| 计算 |`'2015-06-15'` |
| 网络 |`'2015-06-15'`、`'2015-05-01-preview'` |
| 存储 |`'2016-01-01'`、`'2015-06-15'`、`'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| 应用服务 |`'2015-08-01'` |

## <a name="template-functions"></a>模板函数
Azure 资源管理器[函数](../../azure-resource-manager/resource-group-template-functions.md)提供生成为动态模板所需的功能。 例如，可以对如下任务使用函数：

* 串联或者修整字符串 
* 引用其他资源的值
* 对资源进行迭代以部署多个实例 

这些函数中均不提供 Azure 堆栈：

* 跳过
* Take

## <a name="resource-location"></a>资源位置
Azure 资源管理器模板使用位置属性，以便在部署过程中放置的资源。 在 Azure 中，位置是指美国西部或南美洲等区域。 在 Azure Stack 中，位置有所不同，因为 Azure Stack 在数据中心内。 若要确保模板可在 Azure 和 Azure Stack 之间转移，在部署单个资源时应引用资源组位置。 你可以执行此使用`[resourceGroup().Location]`以确保所有资源都继承资源组位置。 以下摘录内容是在部署存储帐户的过程中使用此函数的示例：

    "resources": [
    {
      "name": "[variables('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "[variables('apiVersionStorage')]",
      "location": "[resourceGroup().location]",
      "comments": "This storage account is used to store the VM disks",
      "properties": {
      "accountType": "Standard_GRS"
      }
    }
    ]

## <a name="next-steps"></a>后续步骤
* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
* [使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)
* [通过 Visual Studio 部署模板](azure-stack-deploy-template-visual-studio.md)

