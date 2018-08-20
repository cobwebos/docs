---
title: Azure 合作伙伴和客户使用情况归因
description: 有关如何跟踪 Azure 市场解决方案客户使用情况的概述
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/26/2018
ms.author: ellacroi
ms.openlocfilehash: 46cd5dbc044cbd0b7e38e5f0d0c8aa1916387a2d
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40037933"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure 合作伙伴和客户使用情况归因

作为 Azure 软件合作伙伴，你的解决方案要么需要使用 Azure 组件，要么直接部署在 Azure 基础结构上。  目前，当客户部署合作伙伴解决方案和预配自己的 Azure 资源时，合作伙伴难以观察这些部署的状态，并且难以洞察其对 Azure 增长的影响。 添加更高级别的可见性可以帮助合作伙伴与 Microsoft 销售团队保持一致，并获得 Microsoft 合作伙伴计划的信用额度。   

Microsoft 正在创建一种新方法，帮助合作伙伴更好地跟踪与客户在 Azure 上部署软件相关的 Azure 使用情况。 此新方法以使用 Azure 资源管理器编排 Azure 服务部署为前提。

作为 Microsoft 合作伙伴，你可以将 Azure 使用情况与你代表客户预配的任何 Azure 资源关联。  可以通过 Azure 市场、快速入门存储库、专用 GitHub 存储库甚或一对一客户互动来实现这种关联。  为启用跟踪，可以使用两种方法：

- Azure 资源管理器模板：Azure 资源管理器模板或解决方案模板，用于部署 Azure 服务以运行合作伙伴软件。 合作伙伴可以创建 Azure 资源管理器模板，其中定义 Azure 解决方案的基础结构和配置。 通过创建 Azure 资源管理器模板，你和你的客户可以在解决方案的整个生命周期内部署该解决方案，确保以一致的状态部署资源。 

- Azure 资源管理器 API：合作伙伴可以直接调用 Azure 资源管理器 API 来部署 Azure 资源管理器模板，或生成 API 调用用于直接预配 Azure 服务。 

## <a name="method-1-azure-resource-manager-templates"></a>方法 1：Azure 资源管理器模板 

现在，许多合作伙伴解决方案都是使用 Azure 资源管理器模板部署在客户订阅中的。  如果在 Azure 市场、GitHub 或快速入门存储库中已经拥有 Azure 资源管理器模板，则应该可以直接修改模板以启用这一新跟踪方法。  如果当前没有使用 Azure 资源管理器模板，以下链接可帮助更好地了解 Azure 资源管理器模板及创建方式： 

*   [创建和部署第一个 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [为 Azure 市场创建解决方案模板指南](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="instructions-add-a-guid-to-your-existing-azure-resource-manager-template"></a>说明：向现有 Azure 资源管理器模板添加 GUID

添加 GUID 是对主模板文件的单一修改：
 1. 创建一个 GUID，假设生成的值为 eb7927c8-dd66-43e1-b0cf-c346a422063
 2. 打开 Azure 资源管理器模板
 3. 在主模板文件中添加新资源。 资源只需位于 mainTemplate.json 或 azuredeploy.json 中，而不需要位于任何嵌套的或链接的模板中。
 4. 在“pid-”之后输入如上所示的 GUID。

   应该会看到类似示例的屏幕：`pid-eb7927c8-dd66-43e1-b0cf-c346a422063`

 5. 检查模板是否存在任何错误
 6. 在相应存储库中重新发布模板

## <a name="sample-template-code"></a>示例模板代码

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-code-for-lu-1.PNG?token=Ak8ZDB0JzsBdUGlKEIeHNJRS7b0BWn4Gks5bbMwwwA%3D%3D)


## <a name="method-2-azure-resource-manager-apis"></a>方法 2：Azure 资源管理器 API

在某些情况下，你可能希望直接调用 Azure 资源管理器 REST API 来部署 Azure 服务。 [Azure 为此支持多个 SDK](https://docs.microsoft.com/azure/#pivot=sdkstools)。  你可以使用其中一个 SDK，也可以直接调用 REST API 来部署资源。

如果使用 Azure 资源管理器模板，则应按照上述说明标记解决方案。  如果未使用 Azure 资源管理器模板并直接发出 API 调用，则仍可标记部署以关联 Azure 资源的使用情况。 

**如何使用 Azure 资源管理器 API 标记部署：** 对于此方法，在设计 API 调用时，需要在请求的用户代理标头中包含 GUID。 应为每个产品/服务或 SKU 添加 GUID。  字符串需要使用前缀为“pid-”的格式，然后包含合作伙伴生成的 GUID。   

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG?token=Ak8ZDDiokRcj4PJj0aMkZmfF8BdOuOTzks5bbM35wA%3D%3D)

>[!Note] 
>用于插入用户代理的 GUID 格式：pid-eb7927c8-dd66-43e1-b0cf-c346a422063     //在“pid-”之后输入 GUID

字符串的格式很重要。 如果未包含前缀“pid-”，我们将无法查询数据。 不同的 SDK 有不同的方式。  若要实现此方法，需要查看适用于首选 Azure SDK 的支持和方法。 

**使用 Python SDK 的示例：** 对于 Python，需要使用“config”属性。 只能添加到 UserAgent。 下面是一个示例：

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/python-for-lu.PNG?token=Ak8ZDK5Um4J6oY-7x25tuBpa168BEiYMks5bbMuUwA%3D%3D)

>必须为每个客户端执行此操作，没有全局静态配置（可以选择通过客户端工厂来确保每个客户端都执行此操作）。 
>[其他参考信息](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

如何使用 Azure PowerShell 或 Azure CLI 标记部署：如果通过 Azure PowerShell 部署资源，则可以使用以下方法附加 GUID：

```

[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

若要在使用 Azure CLI 时附加 GUID，请设置 AZURE_HTTP_USER_AGENT 环境变量。  可在脚本范围内设置此变量或进行全局设置，以便在 shell 范围内使用：

```

export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'


```

## <a name="registering-guidsoffers"></a>注册 GUID/产品/服务

必须注册 GUID 才能将其纳入跟踪范围。  

模板 GUID 的所有注册都将通过 Azure 市场云合作伙伴门户 (CPP) 完成。 

1. 立即向 [Azure 市场](http://aka.ms/listonazuremarketplace)提出申请，获取云合作伙伴门户的访问权限。

 *  要求合作伙伴[在 CPP 中拥有个人资料](https://docs.microsoft.com/azure/marketplace/become-publisher)，并鼓励他们在 Azure 市场或 AppSource 中列出产品/服务 
 *  合作伙伴将能够注册多个 GUID 
 *  合作伙伴还能够为非市场解决方案模板/产品/服务注册 GUID
 
2. 登录到[云合作伙伴门户](https://cloudpartner.azure.com/)
3. 在门户的右上角，单击你的帐户图标，然后单击“发布者个人资料”

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-image-for-lu.png)

4. 在“个人资料”页面上，单击“添加跟踪 GUID”。

![](https://github.com/ellacroi/azure-docs-pr/blob/last-lu-images-i-hope/articles/marketplace/media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

5. 在展开的字段中，在“跟踪 GUID”字段中输入你的跟踪 GUID（仅限 GUID，不包括“pid-”前缀），并在“自定义说明”字段中输入你的套餐名称或说明。

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-login.png)

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example.png)

6. 若要注册多个 GUID，请再次单击“添加跟踪 GUID”。 这将打开另一个展开的字段。 

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example-add.png)

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-example-description.png)

7. 完成编辑后，单击“保存”以保存所做的更改。 

![](https://github.com/ellacroi/azure-docs-pr/blob/more-lu-images/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-save.png)



## <a name="verification-of-guid-deployment"></a>验证 GUID 部署 

修改模板并执行测试部署后，可使用以下 PowerShell 脚本检索已部署和标记的资源。 

可使用它来验证 GUID 是否已成功添加到 Azure 资源管理器模板。 它不适用于 Azure 资源管理器 API 部署。

登录 Azure 并选择包含想要在运行脚本前验证的部署的订阅。 必须在部署的订阅上下文中运行。

部署的 GUID 和 resourceGroup 名称是必需的参数。

可在[此处](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1)找到原始脚本。

```

Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName'
)

#get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

#find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

#find all deploymentOperations in a deployment by name (since PowerShell does not surface outputResources on the deployment or correlationId on the deploymentOperation)

foreach ($deployment in $deployments){

#get deploymentOperations by deploymentName and then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}


```

## <a name="guidance-on-creating-guids"></a>有关创建 GUID 的指导

GUID（全局唯一标识符）是由 32 位十六进制数字组成的唯一参考编号。 若要创建 GUID，应使用 GUID 生成器来创建用于跟踪的 GUID。  可使用多个[在线 GUID 生成器](https://www.bing.com/search?q=guid%20generator&qs=n&form=QBRE&sp=-1&ghc=2&pq=guid%20g&sc=8-6&sk=&cvid=0BAFAFCD70B34E4296BB97FBFA3E1B4E)。

建议为每个产品/服务和分销渠道创建唯一的 GUID。  例如，如果你有两个解决方案且两者都通过模板部署并在 Azure 市场和 GitHub 中提供。  创建四个 GUID：

*   Azure 市场中的产品/服务 A 
*   GitHub 中的产品/服务 A
*   Azure 市场中的产品/服务 B 
*   GitHub 中的产品/服务 B

报告将由合作伙伴（Microsoft 合作伙伴 ID）和 GUID 完成。 

还可以选择在更加精细的级别跟踪 GUID，即 SKU（其中 SKU 是产品/服务的变体）。

## <a name="guidance-on-privacy-and-data-collection"></a>隐私和数据收集指南

合作伙伴应提供消息，告知其客户包含 Azure 资源管理器 GUID 跟踪功能的部署将允许 Microsoft 向合作伙伴报告与这些部署关联的 Azure 使用情况。  下面是一些示例语言。 出现“PARTNER”的指示时，应填写自己的公司名称。 此外，合作伙伴应确保语言与其自己的数据隐私和收集政策保持一致，包括供客户选择将自己从跟踪中排除的选项： 

**对于 Azure 资源管理器模板部署**

部署此模板时，Microsoft 将能够根据部署的 Azure 资源识别 PARTNER 软件的安装。  Microsoft 将能够关联用于支持该软件的 Azure 资源。  Microsoft 收集此信息的目的在于提供其产品的最佳体验及运营其业务。 此数据将根据 Microsoft 的隐私政策进行收集和管理，隐私政策可在 https://www.microsoft.com/trustcenter 找到。 

**对于 SDK 或 API 部署**

部署 PARTNER 软件时，Microsoft 将能够根据部署的 Azure 资源识别 PARTNER 软件的安装。  Microsoft 将能够关联用于支持该软件的 Azure 资源。  Microsoft 收集此信息的目的在于提供其产品的最佳体验及运营其业务。 此数据将根据 Microsoft 的隐私政策进行收集和管理，隐私政策可在 https://www.microsoft.com/trustcenter 找到。

## <a name="support"></a>支持

如需帮助，请执行以下步骤：
 1. 访问位于 [go.microsoft.com/fwlink/?linkid=844975](https://go.microsoft.com/fwlink/?linkid=844975) 的支持页面
 2. 对于有关使用情况关联的问题 — 选择问题类型“市场入门”和类别“其他”，然后单击“启动请求”。 

对于有关访问 Azure 市场云合作伙伴门户的问题 — 选择问题类型“市场入门”和类别“访问问题”，然后单击“启动请求”。

 ![](https://github.com/ellacroi/azure-docs-pr/blob/last-lu-images-i-hope/articles/marketplace/media/marketplace-publishers-guide/lu-article-incident.png)
 

 3. 填写下一页中的必填字段，然后单击“继续”。
 4. 填写下一页中的自定义文本字段。 **重要提示**：在事件标题中填写“ISV 使用情况跟踪”，然后在大型自定义文本字段中详细说明你的问题。  填写表格的其余部分，然后单击“提交”。 
 
  ![](https://github.com/qianw211/azure-docs-pr/blob/MyImgAdded-2/articles/marketplace/media/marketplace-publishers-guide/guid-dev-center-help.png)

 

## <a name="faqs"></a>常见问题

**向模板添加 GUID 有何好处？**

Microsoft 将为合作伙伴提供其模板的客户部署情况以及有关受其影响的使用情况的见解。  Microsoft 与合作伙伴还可以使用此信息来促进销售团队之间的更密切合作。 Microsoft 与合作伙伴还可以使用此信息来获取单个合作伙伴对 Azure 增长的影响的更加一致视图。 

**谁可以向模板添加 GUID？**

跟踪资源旨在将合作伙伴的解决方案与客户的 Azure 使用情况关联。  使用情况数据与合作伙伴的 Microsoft 合作伙伴网络身份 (MPN ID) 关联，并且报告通过云合作伙伴门户 (CPP) 向合作伙伴提供。  

**添加 GUID 后是否可对其进行更改？**
 
是，客户或实现合作伙伴可以自定义模板，并且可以更改或删除 GUID。 我们建议合作伙伴主动向其客户和合作伙伴介绍资源和 GUID 的角色，以防止删除或编辑跟踪 GUID。  更改 GUID 只会影响新的部署和资源，而不会影响现有部署和资源。

**报告何时可用？**

报告的 beta 版本应该很快就会上线。  报告将集成到云合作伙伴门户 (CPP) 中。

**我是否可以跟踪通过非 Microsoft 存储库（如 GitHub）部署的模板？**

是，只要部署模板时存在 GUID，就会跟踪使用情况。  
合作伙伴需要在云合作伙伴门户中拥有配置文件才能注册在 Azure 市场外部发布的相关模板。 

**通过 Azure 市场部署模板与通过 GitHub 等其他存储库部署模板是否存在差异？**

是，在 Azure 市场中发布产品/服务的合作伙伴可能会从 Azure 市场收到有关部署的更加详细的数据。  合作伙伴将从在 Azure 市场门户和 Azure 管理门户中向客户公开其产品/服务获益。 Azure 市场中的产品/服务也会为合作伙伴带来潜在顾客。

**如果我为单独的客户参与创建自定义模板，会怎样？**

你仍然可以向模板添加 GUID。  如果使用已注册的现有 GUID，它将包含在报告中。  如果创建新的 GUID，则需要注册新的 GUID 才能将其包含在跟踪中。

**客户是否也会收到报告？**

客户目前能够在 Azure 管理门户内跟踪各个资源或客户定义资源组的使用情况。   

**此跟踪方法是否类似于数字记录合作伙伴 (DPOR)？**

这种将部署和使用情况与合作伙伴解决方案关联的新方法旨在提供将合作伙伴解决方案与 Azure 使用情况连接的机制。  DPOR 旨在将咨询（系统集成商）或管理（托管服务提供商）合作伙伴与客户的 Azure 订阅关联。   
