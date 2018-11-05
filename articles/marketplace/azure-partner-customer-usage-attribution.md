---
title: Azure 合作伙伴和客户使用情况归因
description: 有关如何跟踪 Azure 市场解决方案客户使用情况的概述
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: yijenj
ms.openlocfilehash: 604eb528ef33a95993aa5b6d3ff6eebb77936aa2
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157932"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure 合作伙伴和客户使用情况归因

作为 Azure 软件合作伙伴，你的解决方案要么需要使用 Azure 组件，要么直接部署在 Azure 基础结构上。 部署合作伙伴解决方案和预配自己 Azure 资源的客户可能难以观察这些部署的状态，并且难以洞察其对 Azure 增长的影响。 添加更高级别的可见性可与 Microsoft 销售团队保持一致，并获得 Microsoft 合作伙伴计划的信用额度。   

Microsoft 现已提供一种新方法，帮助合作伙伴更好地跟踪与客户在 Azure 上部署软件相关的 Azure 使用情况。 此新方法使用 Azure 资源管理器来协调 Azure 服务的部署。

Microsoft 合作伙伴可将 Azure 使用情况与其代表客户预配的任何 Azure 资源相关联。 可以通过 Azure 市场、快速入门存储库、专用 GitHub 存储库甚至一对一的客户互动来实现这种关联。 为启用跟踪，可以使用两种方法：

- Azure 资源管理器模板：用于部署 Azure 服务以运行合作伙伴软件的资源管理器模板或解决方案模板。 合作伙伴可以创建资源管理器模板，其中定义 Azure 解决方案的基础结构和配置。 通过资源管理器模板，你和你的客户可以在解决方案的整个生命周期内部署该解决方案。 可以放心地以一致状态部署资源。 

- Azure 资源管理器 API：合作伙伴可以直接调用资源管理器 API 来部署资源管理器模板，或生成 API 调用用于直接预配 Azure 服务。 

## <a name="use-resource-manager-templates"></a>使用 Resource Manager 模板

许多合作伙伴解决方案都是使用资源管理器模板部署在客户订阅中的。 如果在 Azure 市场、GitHub 或 QuickStart 中拥有 Azure 资源管理器模板，则应该可以直接修改模板以启用这一新跟踪方法。 如果未使用 Azure 资源管理器模板，可以借助以下链接更好地了解资源管理器模板及创建方式： 

*   [创建和部署第一个资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [为 Azure 市场创建解决方案模板](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="add-a-guid-to-your-template"></a>将 GUID 添加到模板

若要添加全局唯一标识符 (GUID)，可对主模板文件进行一次性的修改：

1. 使用建议的方法[创建 GUID](#create-guids)，并[注册 GUID](#register-guids-and-offers)。

1. 打开资源管理器模板。

1. 在主模板文件中添加新资源。 资源只需位于 **mainTemplate.json** 或 **azuredeploy.json** 文件中，而不需要位于任何嵌套的或链接的模板中。

1. 在 pid- 前缀的后面输入 GUID 值（例如，pid-eb7927c8-dd66-43e1-b0cf-c346a422063）。

1. 检查模板是否存在任何错误。

1. 在相应存储库中重新发布模板。

1. [在模板部署中验证 GUID 是否成功](#verify-the-guid-deployment)。

### <a name="sample-resource-manager-template-code"></a>示例资源管理器模板代码
将下面的示例代码添加到主模板文件时，请确保使用自己的输入修改该代码。
资源只需添加到 **mainTemplate.json** 或 **azuredeploy.json** 文件中，而不需要位于任何嵌套的或链接的模板中。
```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```

## <a name="use-the-resource-manager-apis"></a>使用资源管理器 API

在某些情况下，你可能希望直接调用资源管理器 REST API 来部署 Azure 服务。 [Azure 支持使用多个 SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) 来启用这些调用。 你可以使用其中一个 SDK，也可以直接调用 REST API 来部署资源。

如果使用资源管理器模板，应按照上述说明标记解决方案。 如果不使用资源管理器模板，而是直接发出 API 调用，则仍可标记部署以关联 Azure 资源的使用情况。 

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>使用资源管理器 API 标记部署

对于此跟踪方法，在设计 API 调用时，请在请求的用户代理标头中包含 GUID。 为每个产品/服务或 SKU 添加 GUID。 使用 **pid-** 前缀设置字符串的格式，并包含合作伙伴生成的 GUID。 下面是要插入到用户代理的 GUID 格式的示例： 

![示例 GUID 格式](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> 字符串的格式很重要。 如果未包含 **pid-** 前缀，我们将无法查询数据。 以不同的方式跟踪不同的 SDK。 若要实现此方法，请查看适用于首选 Azure SDK 的支持和跟踪方法。 

#### <a name="example-the-python-sdk"></a>示例：Python SDK

对于 Python，请使用 **config** 属性。 只能将该属性添加到 UserAgent。 下面是一个示例：

![将属性添加到用户代理](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> 将属性添加每个客户端。 没有全局的静态配置。 可以标记一个客户端工厂来确保每个客户端都在跟踪。 有关详细信息，请参阅 [GitHub 上的此客户端工厂示例](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)。

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>使用 Azure PowerShell 标记部署

如果通过 Azure PowerShell 部署资源，请使用以下方法追加 GUID：

```
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>使用 Azure CLI 标记部署

使用 Azure CLI 附加 GUID 时，请设置 **AZURE_HTTP_USER_AGENT** 环境变量。 可在脚本范围设置此变量。 还可以针对 shell 全局范围设置变量：

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

## <a name="create-guids"></a>创建 GUID

GUID 是由 32 位十六进制数字组成的唯一参考编号。 若要创建用于跟踪的 GUID，应使用 GUID 生成器。 Azure 存储团队已创建 [GUID 生成器窗体](https://aka.ms/StoragePartners)，它将通过电子邮件向你发送格式正确的 GUID，并可在不同的跟踪系统中重复使用。 

> [!Note]
> 强烈建议使用 [Azure 存储的 GUID 生成器窗体](https://aka.ms/StoragePartners)创建 GUID。 有关详细信息，请参阅[常见问题解答](#faq)。

请为每个产品/服务和分销渠道创建唯一的 GUID。 如果使用模板部署两个解决方案，并且每个解决方案都在 Azure 市场和 GitHub 中提供，则需要创建四个 GUID：

*   Azure 市场中的产品/服务 A 
*   GitHub 中的产品/服务 A
*   Azure 市场中的产品/服务 B 
*   GitHub 中的产品/服务 B

报告根据合作伙伴值（Microsoft 合作伙伴 ID）和 GUID 来执行。 

也可以在更加精细的级别跟踪 GUID（例如 SKU，其中 SKU 是产品/服务的变体）。

## <a name="register-guids-and-offers"></a>注册 GUID 和产品/服务

必须注册 GUID 才能将其纳入跟踪范围。  

模板 GUID 的所有注册需通过 Azure 市场云合作伙伴门户 (CPP) 完成。 

将 GUID 添加到模板或用户代理并在 CPP 中注册 GUID 后，系统将跟踪所有部署。 

1. 向 [Azure 市场](http://aka.ms/listonazuremarketplace)提出申请，并获取 CPP 的访问权限。

   * 合作伙伴必须[在 CPP 中拥有个人资料](https://docs.microsoft.com/azure/marketplace/become-publisher)。 我们建议在 Azure 市场或 AppSource 中列出产品/服务。
   * 合作伙伴可以注册多个 GUID。
   * 合作伙伴可为非市场解决方案模板和产品/服务注册 GUID。
 
1. 登录[云合作伙伴平台](https://cloudpartner.azure.com/)。

1. 在右上角选择自己的帐户图标，然后选择“发布者个人资料”。

   ![选择“发布者个人资料”](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. 在“个人资料”页上，选择“添加跟踪 GUID”。

   ![选择“添加跟踪 GUID”](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. 在“跟踪 GUID”框中输入跟踪 GUID。 只输入 GUID，不要包括 **pid-** 前缀。 在“自定义说明”框中，输入产品/服务名称或说明。

   ![个人资料页](media/marketplace-publishers-guide/guid-dev-center-login.png)
   
   ![输入 GUID 和产品/服务的说明](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. 若要注册多个 GUID，请再次选择“添加跟踪 GUID”。 页面上会显示其他框。

   ![再次选择“添加跟踪 GUID”](media/marketplace-publishers-guide/guid-dev-center-example-add.png)
   
   ![输入另一个 GUID 和产品/服务的说明](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. 选择“保存”。

   ![选择“保存”](media/marketplace-publishers-guide/guid-dev-center-save.png)

将 GUID 添加到模板或用户代理并在 CPP 中注册 GUID 后，系统将跟踪所有部署。 

## <a name="verify-the-guid-deployment"></a>验证 GUID 部署 

修改模板并运行测试部署后，使用以下 PowerShell 脚本检索已部署和标记的资源。 

可以使用该脚本来验证 GUID 是否已成功添加到资源管理器模板。 该脚本不适用于资源管理器 API 部署。

登录 Azure。 选择包含要在运行脚本之前验证的部署的订阅。 在部署的订阅上下文中运行该脚本。

部署的 **GUID** 和 **resourceGroup** 名称是必需的参数。

可在 GitHub 上获取[原始脚本](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1)。

```
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="notify-your-customers"></a>通知客户

合作伙伴应让客户知道部署中使用了资源管理器 GUID 跟踪。 Microsoft 将向合作伙伴报告与这些部署关联的 Azure 使用情况。 以下示例包含可用于向客户告知这些部署的内容。 请将示例中的 \<PARTNER> 替换为自己的公司名称。 合作伙伴应确保通知与其数据隐私和收集政策保持一致，包括供客户选择将自己从跟踪中排除的选项。 

### <a name="notification-for-resource-manager-template-deployments"></a>有关资源管理器模板部署的通知

部署此模板时，Microsoft 将能够根据部署的 Azure 资源识别 \<PARTNER> 软件的安装。 Microsoft 能够关联用于支持该软件的 Azure 资源。 Microsoft 收集此信息的目的在于提供其产品的最佳体验及运营其业务。 此数据将根据 Microsoft 的隐私政策进行收集和管理，隐私政策可在 https://www.microsoft.com/trustcenter 中找到。 

### <a name="notification-for-sdk-or-api-deployments"></a>有关 SDK 或 API 部署的通知

部署 \<PARTNER> 软件时，Microsoft 能够根据部署的 Azure 资源识别 \<PARTNER> 软件的安装。 Microsoft 能够关联用于支持该软件的 Azure 资源。 Microsoft 收集此信息的目的在于提供其产品的最佳体验及运营其业务。 此数据将根据 Microsoft 的隐私政策进行收集和管理，隐私政策可在 https://www.microsoft.com/trustcenter 中找到。

## <a name="get-support"></a>获取支持

如需帮助，请执行以下步骤。

1. 转到[支持页](https://go.microsoft.com/fwlink/?linkid=844975)。 

1. 在“问题类型”下，选择“市场加入”。

1. 选择问题的“类别”：

   - 对于使用情况关联的问题，请选择“其他”。
   - 对于访问 Azure 市场 CPP 时出现的问题，请选择“访问问题”。
   
    ![选择问题类别](media/marketplace-publishers-guide/lu-article-incident.png)

1. 选择“开始请求”。

1. 在下一页上输入所需的值。 选择“继续”。

1. 在下一页上输入所需的值。

   > [!Important] 
   > 在“事件标题”框中，输入“ISV 使用情况跟踪”。 请详细描述问题。
   
   ![在事件标题中输入“ISV 使用情况跟踪”](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. 填写表单，然后选择“提交”。

## <a name="faq"></a>常见问题解答

**向模板添加 GUID 有何好处？**

Microsoft 为合作伙伴提供其模板的客户部署情况以及有关受其影响的使用情况的见解。 Microsoft 与合作伙伴可以使用此信息来促进销售团队之间的更密切合作。 Microsoft 与合作伙伴可以使用此数据来获取单个合作伙伴对 Azure 增长的影响的更加一致视图。 

**谁可以向模板添加 GUID？**

跟踪资源旨在将合作伙伴的解决方案与客户的 Azure 使用情况关联。 使用情况数据与合作伙伴的 Microsoft 合作伙伴网络标识 (MPN ID) 相关联。 报告通过 CPP 向合作伙伴提供。

**添加 GUID 后是否可对其进行更改？**
 
是，客户或实现合作伙伴可以自定义模板，并且可以更改或删除 GUID。 我们建议合作伙伴主动向其客户和合作伙伴介绍资源和 GUID 的角色，以防止删除或编辑跟踪 GUID。 更改 GUID 只会影响新的部署和资源，而不会影响现有部署和资源。

**报告何时可用？**

报告的 beta 版本应该很快就会上线。 报告将集成到 CPP 中。

**我是否可以跟踪通过非 Microsoft 存储库（如 GitHub）部署的模板？**

是，只要部署模板时存在 GUID，就会跟踪使用情况。 合作伙伴需要在 CPP 中拥有个人资料才能注册在 Azure 市场外部发布的相关模板。 

**通过 Azure 市场部署模板与通过 GitHub 等其他存储库部署模板是否存在差异？**

是，在 Azure 市场中发布产品/服务的合作伙伴可能会从 Azure 市场收到有关部署的更加详细的数据。 合作伙伴可从在 Azure 市场门户和 Azure 门户中向客户公开其产品/服务获益。 Azure 市场中的产品/服务也会为合作伙伴带来潜在顾客。

**如果我为单独的客户参与创建自定义模板，会怎样？**

仍可以向模板添加 GUID。 如果使用已注册的现有 GUID，它将包含在报告中。 如果创建新的 GUID，则需要注册新的 GUID 才能将其包含在跟踪中。

**客户是否也会收到报告？**

客户可在 Azure 门户中跟踪各个资源或客户定义资源组的使用情况。   

**此跟踪方法是否类似于数字记录合作伙伴 (DPOR)？**

这种将部署和使用情况与合作伙伴解决方案关联的新方法提供将合作伙伴解决方案与 Azure 使用情况连接的机制。 DPOR 旨在将咨询（系统集成商）或管理（托管服务提供商）合作伙伴与客户的 Azure 订阅关联。   

**使用 Azure 存储的 GUID 生成器窗体有何益处？**

Azure 存储的 GUID 生成器窗体可确保生成所需格式的 GUID。 此外，如果使用任何 Azure 存储的数据平面跟踪方法，则可以利用相同 GUID 进行市场控制平面跟踪。 这样，你将可以利用 Partner 属性的单个统一 GUID，而无需维护单独的多个 GUID。
