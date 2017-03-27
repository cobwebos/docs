---
title: "面向 Azure 政府合作伙伴的 Azure 应用商店 | Microsoft 文档"
description: "本文提供针对 Azure 政府的应用程序开发使用到的功能和指南对比。"
services: azure-government
cloud: gov
documentationcenter: 
author: tsingh
manager: asimm
ms.assetid: 7790d3c5-d0fa-4662-b4f0-a174cb7d6c9f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: zakramer;tsingh;divacc
translationtype: Human Translation
ms.sourcegitcommit: 0839e8e57b2149e50d4512d28b1e57e6592be458
ms.openlocfilehash: 70821864520ff18ba8c64be0ea66376bccee7148


---
# <a name="azure-marketplace-for-azure-government"></a>面向 Azure 政府的 Azure 应用商店
如果你是有兴趣将产品发布到 Azure 应用商店的 Azure 政府合作伙伴，可在本文中找到详细信息。

目前 Azure 政府应用商店支持 BYOL VM 映像和解决方案模板。 最佳做法是，在发布到 Azure 政府之前检查解决方案模板，以确保它将同时在 Azure 公有云和 Azure 政府云上正常工作。 如果仅发布 VM 映像，则可以继续执行下面详述的发布步骤。

## <a name="pre-publishing-validation-for-solution-templates"></a>解决方案模板的预发布验证

将解决方案模板发布到 Azure 政府之前，建议检查几个常见的最佳做法领域，以确保该模板将同时在 Azure 公有云和 Azure 政府中正常工作。

1.  验证终结点是否未硬编码到 Azure 公有云的解决方案模板中。 这些并非对于任何其他 Azure 环境都有效。 请改为修改解决方案模板，以请求拉取有效终结点的 API 调用：  

  示例：

  不正确 VHD uri（已硬编码）"uri": "[concat('https://', variables('storageAccountName'), '.blob.core.windows.net/',  '/osdisk.vhd')]",

  正确的 VHD uri（已引用）

  "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'osdisk.vhd')]",

  已更正的语法可确保该模板将作用于任何云（政府、公共 Azure、AzureStack、中国，等等）

2.  验证解决方案模板中使用的资源是否在要发布到的 Sovereign 云中可用。
Azure 和 API 版本中的 RP

    确定使用资源浏览器的 Azure 政府在门户中的可用性：

  1.    登录到 Azure 政府门户
  2.    按照此处 (https://docs.microsoft.com/zh-cn/azure/azure-resource-manager/resource-manager-supported-services#supported-regions) 列出的步骤启动资源浏览器

  最常用的扩展; Fairfax 中的可用性  

  | 发布者/类型 | Fairfax 中的可用版本 |
  | --- | --- |
  | Microsoft.OSTCExtensions/CustomScriptForLinux | 1; 1.1; 1.2.2.0; 1.3.0.2; 1.4.1.0; 1.5.2.0 |
  | Microsoft.Compute/CustomScriptExtension | 1.2; 1.3; 1.4; 1.7; 1.8 |
  | Microsoft.Azure.Extensions/DockerExtension | 不可用 |
  | Microsoft.Azure.Extensions/CustomScript | 2.0.2 |
  | Microsoft.OSTCExtensions/LinuxDiagnostic | 2.0.9005; 2.1.9005; 2.2.9005; 2.3.9011 |
  | Microsoft.Powershell/DSC | 2.19.0.0 |

> [!NOTE]
> 如果放入位置以提供允许值的列表，则需要在添加新区域时定期进行更新。  


## <a name="publishing"></a>发布
> [!NOTE]
> 如果不是现有的 Azure 认证应用商店合作伙伴，请在继续进行操作前，先完成显示如何[发布和管理产品](../marketplace-publishing/marketplace-publishing-getting-started.md)的步骤。
>
>

### <a name="step-1"></a>步骤 1
登录到 [Azure 发布](https://publish.windowsazure.com)。

### <a name="step-2"></a>步骤 2
单击要发布的产品/服务。

### <a name="step-3"></a>步骤 3
单击“SKU”，然后选择“Azure 政府云”框。

> [!NOTE]
> 仅支持自带许可 (BYOL) SKU。  此选项不适用于即用即付 (PayG) SKU。
>
>

![“SKU”和“Azure 政府云”选项所在的产品/服务页](./media/government-manage-marketplace-partner-1.png)

### <a name="step-4"></a>步骤 4
单击“+ 添加证书”链接，添加产品/服务的任何证书的链接。

![包含“添加证书”链接的产品/服务页](./media/government-manage-marketplace-partner-2.png)

### <a name="step-5"></a>步骤 5
若要在发布门户中测试映像，请为 Microsoft Azure 政府请求[试用帐户](https://azuregov.microsoft.com/trial/azuregovtrial)。

你已通过了成为为美国联邦、州、地方或部落实体提供产品/服务的合作伙伴的资格验证，确认信息将通过电子邮件发送给你。  试用帐户在 3 到 5 个工作日内可用。

### <a name="step-6"></a>步骤 6
单击“发布”，然后单击“推送到暂存”。

![“发布”和“推送到暂存”选项](./media/government-manage-marketplace-partner-3.png)

系统将提示你输入有权访问暂存产品/服务的已加入允许列表的订阅。 输入从 Azure 政府试用版帐户中新获取的订阅 ID。

![你在其中指定可以访问产品/服务的订阅 ID 的提示](./media/government-manage-marketplace-partner-4.png)

### <a name="step-7"></a>步骤 7
成功暂存产品/服务后，可通过使用 Azure 政府试用帐户登录 [Azure 门户](https://portal.azure.us)来测试映像。

### <a name="step-8"></a>步骤 8
使用试用订阅验证映像后，可通过单击“发布”并请求审批以转到生产，使产品/服务上线。

![“请求审批以转到生产”命令](./media/government-manage-marketplace-partner-5.png)

## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 [Microsoft Azure 政府版博客](https://blogs.msdn.microsoft.com/azuregov/)。



<!--HONumber=Dec16_HO3-->


