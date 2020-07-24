---
title: 商业市场合作伙伴和客户使用情况归因
description: 获取有关跟踪 Azure 市场解决方案客户使用情况的概述。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vikrambmsft
ms.author: vikramb
ms.date: 04/14/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: ab729d34219c05ee76a2a14832f41342d29eab21
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065806"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>商业市场合作伙伴和客户使用情况归因

客户使用情况归因是一种方法，可将 Azure 资源作为合作伙伴与你关联起来，这些资源在客户订阅中运行且为了运行你的解决方案已部署。 在内部 Microsoft 系统中形成这些关联可以更好地了解运行软件的 Azure 内存占用情况。 采用此跟踪功能时，可与 Microsoft 销售团队保持一致，并获得 Microsoft 合作伙伴计划的信用额度。

可以通过 Azure 市场、快速入门存储库、专用 GitHub 存储库甚至创建持久 IP 的一对一客户互动（例如开发应用）来实现这种关联。

客户使用情况归因支持三个部署选项：

- Azure 资源管理器模板：合作伙伴可以使用资源管理器模板部署 Azure 服务以运行合作伙伴软件。 合作伙伴可以创建资源管理器模板，其中定义 Azure 解决方案的基础结构和配置。 通过资源管理器模板，你和你的客户可以在解决方案的整个生命周期内部署该解决方案。 可以放心地以一致状态部署资源。
- Azure 资源管理器 API：合作伙伴可以直接调用资源管理器 API 来部署资源管理器模板，或生成 API 调用用于直接预配 Azure 服务。
- Terraform：合作伙伴可以使用 Terraform 来部署资源管理器模板或直接部署 Azure 服务。

>[!IMPORTANT]
>- 客户使用情况归因不是为了跟踪系统集成商、托管服务提供商或用于部署和管理 Azure 上运行软件的工具的工作。
>
>- 客户使用情况归因适用于新部署，不支持标记已部署的现有资源。
>
>- 发布到 Azure 市场的 [Azure 应用程序](./partner-center-portal/create-new-azure-apps-offer.md)产品/服务必须有客户使用情况归因。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>创建 GUID

GUID 是由 32 位十六进制数字组成的唯一参考标识符。 若要创建用于跟踪的 GUID，应使用 GUID 生成器。 Azure 存储团队已创建 [GUID 生成器窗体](https://aka.ms/StoragePartners)，它将通过电子邮件向你发送格式正确的 GUID，并可在不同的跟踪系统中重复使用。

> [!NOTE]
> 强烈建议使用 [Azure 存储的 GUID 生成器窗体](https://aka.ms/StoragePartners)创建 GUID。 有关详细信息，请参阅[常见问题解答](#faq)。

建议为每个产品/服务和每个产品的分销渠道创建唯一的 GUID。 如果不希望拆分报告，则可以选择对产品的多个分销渠道使用单个 GUID。

如果使用模板部署产品并且产品在 Azure 市场和 GitHub 中都提供，则可以创建并注册两个不同的 GUID：

- Azure 市场中的产品 A
- GitHub 中的产品 A

报告通过 Microsoft 合作伙伴网络 ID 和 GUID 完成。

你还可以通过注册额外 GUID 以及在计划之间更改 GUID（其中计划是产品/服务的变体），在更精细的级别上跟踪使用情况。

## <a name="register-guids"></a>注册 GUID

必须在合作伙伴中心注册 GUID，才能启用客户使用情况归因。

将 GUID 添加到模板或用户代理并在合作伙伴中心注册 GUID 后，系统将跟踪未来的部署。

> [!NOTE]
> 如果你正在通过合作伙伴中心向 Azure Marketplace 发布你的[Azure 应用程序](./partner-center-portal/create-new-azure-apps-offer.md)产品/服务，则在上载模板时，模板内使用的任何新 GUID 都将自动注册到你的合作伙伴中心配置文件。  

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard)。

1. 注册为[商业市场发布者](https://aka.ms/JoinMarketplace)。

   * 要求合作伙伴[在合作伙伴中心中拥有个人资料](become-publisher.md)。 我们建议在 Azure 市场或 AppSource 中列出产品/服务。
   * 合作伙伴可以注册多个 GUID。
   * 合作伙伴可为非市场解决方案模板和产品/服务注册 GUID。

1. 在右上角，选择设置齿轮图标，然后选择“开发人员设置”。

1. 在“帐户设置”页上，选择“添加跟踪 GUID”。 

1. 在“GUID”框中，输入跟踪 GUID。 仅输入不带前缀的 GUID `pid-` 。 在“描述”框中，输入产品/服务名称或描述。

1. 若要注册多个 GUID，请再次选择“添加跟踪 GUID”。 页面上会显示其他框。

1. 选择“保存”。

## <a name="use-resource-manager-templates"></a>使用 Resource Manager 模板
许多合作伙伴解决方案都是使用 Azure 资源管理器模板部署的。 如果你使用的资源管理器模板在 Azure 市场、GitHub 上或作为快速入门提供，则修改此模板以启用客户使用情况归因的过程很简单。

> [!NOTE]
> 有关创建和发布解决方案模板的详细信息，请参阅
> * [创建和部署第一个资源管理器模板](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。
>* [Azure 应用程序产品/服务](./partner-center-portal/create-new-azure-apps-offer.md)。
>* 视频：[为 Azure 市场构建解决方案模板和托管应用程序](https://channel9.msdn.com/Events/Build/2018/BRK3603)。


若要添加全局唯一标识符 (GUID)，可对主模板文件进行一次性的修改：

1. 使用建议的方法[创建 GUID](#create-guids)，并[注册 GUID](#register-guids)。

1. 打开资源管理器模板。

1. 在主模板文件中添加新资源。 资源只需位于 **mainTemplate.json** 或 **azuredeploy.json** 文件中，而不需要位于任何嵌套的或链接的模板中。

1. 在前缀后面输入 GUID 值 `pid-` （例如，pid-eb7927c8-dd66-43e1-b0cf-c346a422063）。

1. 检查模板是否存在任何错误。

1. 在相应存储库中重新发布模板。

1. [在模板部署中验证 GUID 是否成功](#verify-the-guid-deployment)。

### <a name="sample-resource-manager-template-code"></a>示例资源管理器模板代码

若要为模板启用跟踪资源，需要在资源部分下添加以下附加资源。 将下面的示例代码添加到主模板文件时，请确保使用自己的输入修改该代码。
资源只需添加到 **mainTemplate.json** 或 **azuredeploy.json** 文件中，而不需要位于任何嵌套的或链接的模板中。

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
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

在某些情况下，你可能希望直接调用资源管理器 REST API 来部署 Azure 服务。 [Azure 支持使用多个 SDK](https://docs.microsoft.com/azure/?pivot=sdkstools) 来启用这些调用。 你可以使用其中一个 SDK，也可以直接调用 REST API 来部署资源。

如果使用资源管理器模板，应按照上述说明标记解决方案。 如果不使用资源管理器模板，而是直接发出 API 调用，则仍可标记部署以关联 Azure 资源的使用情况。

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>使用资源管理器 API 标记部署

要启用客户使用情况归因，在设计 API 调用时，请在请求的用户代理标头中包含 GUID。 为每个产品/服务或 SKU 添加 GUID。 格式化带有前缀的字符串 `pid-` ，并包括合作伙伴生成的 GUID。 下面是要插入到用户代理的 GUID 格式的示例：

![示例 GUID 格式](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> 字符串的格式很重要。 如果不 `pid-` 包含前缀，则不可能查询数据。 以不同的方式跟踪不同的 SDK。 若要实现此方法，请查看适用于首选 Azure SDK 的支持和跟踪方法。

#### <a name="example-the-python-sdk"></a>示例：Python SDK

对于 Python，请使用 **config** 属性。 只能将该属性添加到 UserAgent。 下面是一个示例：

![将属性添加到用户代理](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> 将属性添加每个客户端。 没有全局的静态配置。 可以标记一个客户端工厂来确保每个客户端都在跟踪。 有关详细信息，请参阅 [GitHub 上的此客户端工厂示例](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)。

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>使用 Azure PowerShell 标记部署

如果通过 Azure PowerShell 部署资源，请使用以下方法追加 GUID：

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>使用 Azure CLI 标记部署

使用 Azure CLI 附加 GUID 时，请设置 **AZURE_HTTP_USER_AGENT** 环境变量。 可在脚本范围设置此变量。 还可以针对 shell 全局范围设置变量：

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
有关详细信息，请参阅 [Azure SDK for Go](https://docs.microsoft.com/azure/developer/go/)。

## <a name="use-terraform"></a>使用 Terraform

对 Terraform 的支持通过 Azure 提供程序 1.21.0 版本提供：[https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019)。  此支持适用于通过 Terraform 部署解决方案的所有合作伙伴，以及由 Azure 提供程序（版本 1.21.0 或更高版本）部署和按流量计费的所有资源。

适用于 Terraform 的 Azure 提供程序添加了一个新的可选字段 [partner_id](https://www.terraform.io/docs/providers/azurerm/#partner_id)，你可以在其中指定用于解决方案的跟踪 GUID。 此字段的值还可来源于 *ARM_PARTNER_ID* 环境变量。

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
如果合作伙伴要通过客户使用情况归因所跟踪的 Terraform 获取其部署，需要执行以下操作：

* 创建 GUID（应为每个产品/服务或 SKU 添加 GUID）
* 更新其 Azure 提供程序，将 partner_id 的值设置为 GUID（不要给 GUID 加前缀“pid-”，只需将其设置为实际 GUID）


## <a name="verify-the-guid-deployment"></a>验证 GUID 部署

修改模板并运行测试部署后，使用以下 PowerShell 脚本检索已部署和标记的资源。

可以使用该脚本来验证 GUID 是否已成功添加到资源管理器模板。 该脚本不适用于资源管理器 API 或 Terraform 部署。

登录 Azure。 选择包含要在运行脚本之前验证的部署的订阅。 在部署的订阅上下文中运行该脚本。

部署的 **GUID** 和 **resourceGroup** 名称是必需的参数。

可在 GitHub 上获取[原始脚本](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1)。

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>报表

你可以在合作伙伴中心仪表板 ([https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)) 中找到客户使用情况归因报告。 若要查看报告，必须使用合作伙伴中心凭据登录。 如果在报告或登录时遇到任何问题，请按照“获取支持”部分中的说明创建支持请求。

在“合作伙伴关联类型”下拉列表中选择“跟踪的模板”以查看报告。

![客户使用情况归因报告](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>通知客户

合作伙伴应告知客户有关使用客户使用情况归因的部署情况。 Microsoft 将向合作伙伴报告与这些部署关联的 Azure 使用情况。 以下示例包含可用于向客户告知这些部署的内容。 在示例中，将替换 \<PARTNER> 为公司名称。 合作伙伴应确保通知与其数据隐私和收集政策保持一致，包括供客户选择将自己从跟踪中排除的选项。

### <a name="notification-for-resource-manager-template-deployments"></a>有关资源管理器模板部署的通知

部署此模板时，Microsoft 能够识别软件的安装 \<PARTNER> 和部署的 Azure 资源。 Microsoft 能够关联用于支持该软件的 Azure 资源。 Microsoft 收集此信息的目的在于提供其产品的最佳体验及运营其业务。 此数据将根据 Microsoft 的隐私政策进行收集和管理，隐私政策可在 https://www.microsoft.com/trustcenter 中找到。

### <a name="notification-for-sdk-or-api-deployments"></a>有关 SDK 或 API 部署的通知

部署软件时 \<PARTNER> ，Microsoft 能够识别软件的安装 \<PARTNER> 和部署的 Azure 资源。 Microsoft 能够关联用于支持该软件的 Azure 资源。 Microsoft 收集此信息的目的在于提供其产品的最佳体验及运营其业务。 此数据将根据 Microsoft 的隐私政策进行收集和管理，隐私政策可在 https://www.microsoft.com/trustcenter 中找到。

## <a name="get-support"></a>获取支持

根据你面临的问题，我们提供了两个支持通道。

如果在合作伙伴中心遇到任何问题，如查看客户使用情况归因报告或登录问题，请在此处创建面向合作伙伴中心支持团队的支持请求：[https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![获取支持页的屏幕截图](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

如果你需要有关市场加入和/或客户使用情况归因的常规帮助（例如，如何设置客户使用情况归因），请遵循以下步骤：

1. 转到[支持页](https://go.microsoft.com/fwlink/?linkid=844975)。

1. 在“问题类型”下，选择“市场加入”。 

1. 选择问题的“类别”：

   - 对于使用情况关联的问题，请选择“其他”。
   - 对于访问 Azure 市场时出现的问题，请选择“访问问题”。

     ![选择问题类别](media/marketplace-publishers-guide/lu-article-incident.png)

1. 选择“开始请求”。

1. 在下一页上输入所需的值。 选择“继续”。

1. 在下一页上输入所需的值。

   > [!IMPORTANT]
   > 在“事件标题”框中，输入“ISV 使用情况跟踪”。  请详细描述问题。

   ![在事件标题中输入“ISV 使用情况跟踪”](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. 填写表单，然后选择“提交”。

你还可以从 Microsoft 合作伙伴技术顾问那里获得有关技术预销售、部署和应用开发方案的技术指导，以了解并纳入客户使用情况归因。

### <a name="how-to-submit-a-technical-consultation-request"></a>如何提交技术咨询请求

1. 访问[合作伙伴技术服务](https://aka.ms/TechnicalJourney)。
1. 选择“云基础设施和管理”，将打开一个新页面，让你一览技术之旅。
1. 在“部署服务”下，单击“提交请求”按钮
1. 使用 MSA（MPN 帐户）或 AAD（合作伙伴仪表板帐户）登录；根据你的登录凭据，将打开一个在线请求窗体：
    * 完成/查看联系信息。
    * 可以预先填充咨询详细信息，也可以从下拉列表中进行选择。
    * 输入问题的标题和描述（提供尽可能多的详细信息）。
1. 单击“提交”

通过[使用技术预售和部署服务](https://aka.ms/TechConsultInstructions)上的屏幕截图了解分步说明。

### <a name="whats-next"></a>后续步骤

Microsoft 合作伙伴技术顾问将电话联系你，确定你的需求。

## <a name="faq"></a>常见问题解答

**向模板添加 GUID 有何好处？**

Microsoft 为合作伙伴提供对其解决方案的客户部署情况的观点，以及有关受影响的使用情况的见解。 Microsoft 与合作伙伴可以使用此信息来促进销售团队之间的更密切合作。 Microsoft 与合作伙伴可以使用此数据来获取单个合作伙伴对 Azure 增长的影响的更加一致视图。

**添加 GUID 后是否可对其进行更改？**

是，客户或实现合作伙伴可以自定义模板，并且可以更改或删除 GUID。 我们建议合作伙伴主动向其客户和合作伙伴介绍资源和 GUID 的角色，以防止后者删除或编辑 GUID。 更改 GUID 只会影响新的部署和资源，而不会影响现有部署和资源。

**我是否可以跟踪通过非 Microsoft 存储库（如 GitHub）部署的模板？**

是，只要部署模板时存在 GUID，就会跟踪使用情况。 合作伙伴仍必须注册其 GUID。

**客户是否也会收到报告？**

客户可在 Azure 门户中跟踪各个资源或客户定义资源组的使用情况。 客户看不到按 GUID 细分的使用情况。

**此方法是否类似于记录数字合作伙伴 (DPOR)？**

这种将部署和使用情况与合作伙伴解决方案关联的新方法提供将合作伙伴解决方案与 Azure 使用情况连接的机制。 DPOR 旨在将咨询（系统集成商）或管理（托管服务提供商）合作伙伴与客户的 Azure 订阅关联。

**使用 Azure 存储的 GUID 生成器窗体有何益处？**

Azure 存储的 GUID 生成器窗体可确保生成所需格式的 GUID。 此外，如果使用任何 Azure 存储的数据平面跟踪方法，则可以利用相同 GUID 进行市场控制平面跟踪。 这样，你将可以利用 Partner 属性的单个统一 GUID，而无需维护单独的多个 GUID。

**能否对 Azure 市场中的解决方案模板产品/服务使用专用自定义 VHD？**

否，不能。 虚拟机映像必须来自 Azure Marketplace，请参阅： [Azure marketplace 上虚拟机产品/服务的发布指南](marketplace-virtual-machines.md)。

你可以使用自定义 VHD 在市场中创建 VM 产品/服务，并将其标记为“专用”，使任何人都看不到它， 然后在解决方案模板中引用此 VM。

**未能更新主模板的 contentVersion 属性？**

在某些情况下，当使用来自另一个模板（因某些原因应使用较低版本 contentVersion ）的 TemplateLink 部署该模板时，可能会出现这个 bug。 解决方法是使用元数据属性：

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
