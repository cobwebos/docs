---
title: 商业市场合作伙伴和客户使用归因
description: 获取 Azure 应用商店解决方案跟踪客户使用情况的概述。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: dsindona
ms.openlocfilehash: 5fec72ce5f86c1bee9ec0e978e458f5be454c8e3
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256563"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>商业市场合作伙伴和客户使用归因

客户使用情况归因是一种将客户订阅中运行的 Azure 资源（部署以运行解决方案）与您作为合作伙伴相关联的方法。 在 Microsoft 内部系统中形成这些关联可提高运行软件的 Azure 占用空间的可见性。 当您采用此跟踪功能时，您可以与 Microsoft 销售团队保持一致，并为 Microsoft 合作伙伴计划赢得赞誉。

您可以通过 Azure 应用商店、快速入门存储库、专用 GitHub 存储库和创建持久 IP（如应用开发）的 1：1 客户参与来形成关联。

客户使用情况归因支持三个部署选项：

- Azure 资源管理器模板：合作伙伴可以使用资源管理器模板部署 Azure 服务来运行合作伙伴的软件。 合作伙伴可以创建资源管理器模板，其中定义 Azure 解决方案的基础结构和配置。 通过资源管理器模板，你和你的客户可以在解决方案的整个生命周期内部署该解决方案。 可以放心地以一致状态部署资源。
- Azure 资源管理器 API：合作伙伴可以直接调用资源管理器 API 来部署资源管理器模板，或生成 API 调用用于直接预配 Azure 服务。
- Terraform：合作伙伴可以使用 Terraform 部署资源管理器模板或直接部署 Azure 服务。

>[!IMPORTANT]
>- 客户使用情况归因不是用于跟踪系统集成商、托管服务提供商或旨在部署和管理在 Azure 上运行的软件的工具的工作。
>
>- 客户使用情况归因用于新部署，不支持标记已部署的现有资源。
>
>- 发布到 Azure 应用商店的[Azure 应用程序](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)产品/服务需要客户使用情况归因。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>创建 GUID

GUID 是具有 32 个十六进制数字的唯一引用标识符。 若要创建用于跟踪的 GUID，应使用 GUID 生成器。 Azure 存储团队已创建 [GUID 生成器窗体](https://aka.ms/StoragePartners)，它将通过电子邮件向你发送格式正确的 GUID，并可在不同的跟踪系统中重复使用。

> [!NOTE]
> 强烈建议您使用[Azure 存储的 GUID 生成器窗体](https://aka.ms/StoragePartners)来创建 GUID。 有关详细信息，请参阅[常见问题解答](#faq)。

建议为每个产品/服务和每个产品的分销渠道创建唯一的 GUID。 如果不希望拆分报告，则可以选择对产品的多个分销渠道使用单个 GUID。

如果使用模板部署产品，并且该产品在 Azure 应用商店和 GitHub 上都可用，则可以创建和注册两个不同的 GUIDS：

- Azure 市场中的产品 A
- GitHub 中的产品 A

报告由 Microsoft 合作伙伴网络 ID 和 GUID 完成。

您还可以通过注册其他 GUID 并在计划之间更改 GUID（其中计划是产品/服务变体）来跟踪更精细级别的使用情况。

## <a name="register-guids"></a>注册 GUID

GUID 必须在合作伙伴中心注册，以启用客户使用情况归因。

将 GUID 添加到模板或用户代理中，并在合作伙伴中心注册 GUID 后，将跟踪将来的部署。

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard)。

1. 注册为[商业市场出版商](https://aka.ms/JoinMarketplace)。

   * 合作伙伴必须在[合作伙伴中心中拥有配置文件](https://docs.microsoft.com/azure/marketplace/become-publisher)。 我们建议在 Azure 市场或 AppSource 中列出产品/服务。
   * 合作伙伴可以注册多个 GUID。
   * 合作伙伴可以注册非市场解决方案模板和优惠的 GUID。

1. 在右上角，选择设置齿轮图标，然后选择 **"开发人员"设置**。

1. 在 **"帐户设置"页上**，选择 **"添加跟踪 GUID"。**

1. 在**GUID**框中，输入您的跟踪 GUID。 只输入 GUID，不要包括 **pid-** 前缀。 在 **"描述"** 框中，输入您的产品/服务名称或说明。

1. 若要注册多个 GUID，请再次选择“添加跟踪 GUID”。**** 页面上会显示其他框。

1. 选择“保存”。 

## <a name="use-resource-manager-templates"></a>使用 Resource Manager 模板
使用 Azure 资源管理器模板部署许多合作伙伴解决方案。 如果 Azure 应用商店、GitHub 或作为快速入门有可用的资源管理器模板，则修改模板以启用客户使用情况归因的过程是直接的。

> [!NOTE]
> 有关创建和发布解决方案模板的详细信息，请参阅
> * [创建并部署第一个资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。
>* [Azure 应用程序产品 /服务](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)。
>* 视频：[为 Azure 应用商店构建解决方案模板和托管应用程序](https://channel9.msdn.com/Events/Build/2018/BRK3603)。


若要添加全局唯一标识符 (GUID)，可对主模板文件进行一次性的修改：

1. 使用建议的方法[创建 GUID](#create-guids)，并[注册 GUID](#register-guids)。

1. 打开资源管理器模板。

1. 在主模板文件中添加新资源。 资源只需位于 **mainTemplate.json** 或 **azuredeploy.json** 文件中，而不需要位于任何嵌套的或链接的模板中。

1. 在 pid- 前缀的后面输入 GUID 值（例如，pid-eb7927c8-dd66-43e1-b0cf-c346a422063）****。

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

要启用客户使用情况归因，在设计 API 调用时，请在请求中的用户代理标头中包括 GUID。 为每个产品/服务或 SKU 添加 GUID。 使用 **pid-** 前缀设置字符串的格式，并包含合作伙伴生成的 GUID。 下面是要插入到用户代理的 GUID 格式的示例：

![示例 GUID 格式](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> 字符串的格式很重要。 如果未包含 **pid-** 前缀，我们将无法查询数据。 以不同的方式跟踪不同的 SDK。 若要实现此方法，请查看适用于首选 Azure SDK 的支持和跟踪方法。

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
有关详细信息，请参阅[Azure SDK。](https://docs.microsoft.com/azure/go/)

## <a name="use-terraform"></a>使用地形

对 Terraform 的支持可通过 Azure 提供程序的 1.21.0[https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019)版本获得： 。  此支持适用于通过 Terraform 部署其解决方案的所有合作伙伴，以及 Azure 提供程序部署和计量的所有资源（版本 1.21.0 或更高版本）。

Terraform 的 Azure 提供程序添加了一个名为[*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id)的新可选字段，该字段是指定用于解决方案的跟踪 GUID 的位置。 此字段的值也可以从*ARM_PARTNER_ID*环境变量中获取。

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
希望通过 Terraform 通过客户使用情况归因跟踪其部署的合作伙伴需要执行以下操作：

* 创建 GUID（应为每个优惠或 SKU 添加 GUID）
* 更新其 Azure 提供程序以将*partner_id*的值设置为 GUID（不要用"pid-"预先修复 GUID，只需将其设置为实际 GUID）


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

您可以在合作伙伴中心仪表板 （）[https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)中找到客户使用情况归因的报告。 要查看报表，您必须使用合作伙伴中心凭据才能登录。 如果遇到报告或登录的任何问题，请按照"获取支持"部分中的说明创建支持请求。

在合作伙伴关联类型下拉列表中选择"跟踪模板"以查看报表。

![客户使用情况归因报告](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>通知客户

合作伙伴应向客户通报使用客户使用情况归因的部署。 Microsoft 将向合作伙伴报告与这些部署关联的 Azure 使用情况。 以下示例包含可用于向客户告知这些部署的内容。 请将示例中的 \<PARTNER> 替换为自己的公司名称。 合作伙伴应确保通知与其数据隐私和收集政策保持一致，包括供客户选择将自己从跟踪中排除的选项。

### <a name="notification-for-resource-manager-template-deployments"></a>有关资源管理器模板部署的通知

部署此模板时，Microsoft 将能够根据部署的 Azure 资源识别 \<PARTNER> 软件的安装。 Microsoft 能够关联用于支持该软件的 Azure 资源。 Microsoft 收集此信息的目的在于提供其产品的最佳体验及运营其业务。 此数据将根据 Microsoft 的隐私政策进行收集和管理，隐私政策可在 https://www.microsoft.com/trustcenter 中找到。

### <a name="notification-for-sdk-or-api-deployments"></a>有关 SDK 或 API 部署的通知

部署 \<PARTNER> 软件时，Microsoft 能够根据部署的 Azure 资源识别 \<PARTNER> 软件的安装。 Microsoft 能够关联用于支持该软件的 Azure 资源。 Microsoft 收集此信息的目的在于提供其产品的最佳体验及运营其业务。 此数据将根据 Microsoft 的隐私政策进行收集和管理，隐私政策可在 https://www.microsoft.com/trustcenter 中找到。

## <a name="get-support"></a>获取支持

有两个支持渠道，具体取决于您面临的问题。

如果您在合作伙伴中心遇到任何问题（如查看客户使用情况归因报告或登录），请在此处与合作伙伴中心支持团队创建支持请求：[https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

如果您需要有关应用商店入职和/或客户使用一般归因的帮助，例如如何设置客户使用情况归因，请按照以下步骤操作：

1. 转到[支持页](https://go.microsoft.com/fwlink/?linkid=844975)。

1. 在“问题类型”下，选择“市场加入”。********

1. 选择问题的“类别”：****

   - 对于使用情况关联的问题，请选择“其他”。****
   - 对于 Azure 应用商店的访问问题，请选择 **"访问问题**"。

     ![选择问题类别](media/marketplace-publishers-guide/lu-article-incident.png)

1. 选择“开始请求”****。

1. 在下一页上输入所需的值。 选择“继续”。 

1. 在下一页上输入所需的值。

   > [!IMPORTANT]
   > 在“事件标题”框中，输入“ISV 使用情况跟踪”。******** 请详细描述问题。

   ![在事件标题中输入“ISV 使用情况跟踪”](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. 填写表单，然后选择“提交”。****

您还可以从 Microsoft 合作伙伴技术顾问那里获得技术销售前、部署和应用开发方案的技术指导，以了解并纳入客户使用情况归因。

### <a name="how-to-submit-a-technical-consultation-request"></a>如何提交技术咨询请求

1. 请访问 [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney)。
1. 选择云基础架构和管理，将打开一个新页面，供您查看技术之旅。
1. 在"部署服务"下，单击"提交请求"按钮
1. 使用您的 MSA （MPN 帐户） 或 AAD（合作伙伴仪表板帐户）登录;根据您的登录凭据，将打开联机请求表单：
    * 填写/查看联系信息。
    * 咨询详细信息可以预先填充或从下拉列表中选择。
    * 输入标题和问题描述（提供尽可能多的详细信息）。
1. 单击“提交”

查看分步说明，并在 上[https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions)使用屏幕截图。

### <a name="whats-next"></a>后续步骤

Microsoft 合作伙伴技术顾问将与您联系，以设置呼叫范围，以满足您的需求范围。

## <a name="faq"></a>常见问题解答

**向模板添加 GUID 有何好处？**

Microsoft 为合作伙伴提供客户部署其解决方案的视图，以及对其影响使用情况的见解。 Microsoft 与合作伙伴可以使用此信息来促进销售团队之间的更密切合作。 Microsoft 与合作伙伴可以使用此数据来获取单个合作伙伴对 Azure 增长的影响的更加一致视图。

**添加 GUID 后是否可对其进行更改？**

是，客户或实现合作伙伴可以自定义模板，并且可以更改或删除 GUID。 我们建议合作伙伴主动向客户和合作伙伴描述资源和 GUID 的作用，以防止删除或编辑 GUID。 更改 GUID 只会影响新的部署和资源，而不会影响现有部署和资源。

**我是否可以跟踪通过非 Microsoft 存储库（如 GitHub）部署的模板？**

是，只要部署模板时存在 GUID，就会跟踪使用情况。 合作伙伴仍必须注册其 GUID。

**客户是否也会收到报告？**

客户可在 Azure 门户中跟踪各个资源或客户定义资源组的使用情况。 客户看不到 GUID 中断的使用。

**此方法是否类似于记录的数字合作伙伴 （DPOR）？**

这种将部署和使用情况与合作伙伴解决方案关联的新方法提供将合作伙伴解决方案与 Azure 使用情况连接的机制。 DPOR 旨在将咨询（系统集成商）或管理（托管服务提供商）合作伙伴与客户的 Azure 订阅关联。

**使用 Azure 存储的 GUID 生成器窗体有何益处？**

Azure 存储的 GUID 生成器窗体可确保生成所需格式的 GUID。 此外，如果使用任何 Azure 存储的数据平面跟踪方法，则可以利用相同 GUID 进行市场控制平面跟踪。 这样，你将可以利用 Partner 属性的单个统一 GUID，而无需维护单独的多个 GUID。

**是否可以在 Azure 应用商店中使用私有自定义 VHD 作为解决方案模板产品？**

不，你不能。 虚拟机映像必须来自 Azure 应用商店，请参阅： [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)。

您可以使用自定义 VHD 在市场中创建 VM 产品/服务，并将其标记为私有，以便任何人都看不到它。 然后在解决方案模板中引用此 VM。

**无法更新主模板*的内容版本*属性？**

在某些情况下，使用来自另一个模板的 TemplateLink 部署模板时，可能是一个 Bug，该模板由于某种原因预期旧内容版本。 解决方法是使用元数据属性：

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
