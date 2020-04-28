---
title: 商业应用商店合作伙伴和客户使用情况归属
description: 大致了解如何跟踪 Azure Marketplace 解决方案的客户使用情况。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 4dd42c6eabf02bc7a4eb956b9cd5ab2aec28176b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186197"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>商业应用商店合作伙伴和客户使用情况归属

客户使用归属是一种方法，可将客户订阅中运行的 Azure 资源关联起来，以运行解决方案，并将其作为合作伙伴。 在内部 Microsoft 系统中形成这些关联可以更好地了解运行软件的 Azure 占用空间。 采用此跟踪功能时，请与 Microsoft 销售团队保持一致，并获得 Microsoft 合作伙伴计划的信用额度。

可以通过 Azure Marketplace、快速入门存储库、专用 GitHub 存储库和1:1 客户约定建立关联，以创建持久 IP （如应用的开发）。

客户使用情况归属支持三个部署选项：

- Azure 资源管理器模板：合作伙伴可以使用资源管理器模板部署 Azure 服务以运行合作伙伴的软件。 合作伙伴可以创建资源管理器模板，其中定义 Azure 解决方案的基础结构和配置。 通过资源管理器模板，你和你的客户可以在解决方案的整个生命周期内部署该解决方案。 可以放心地以一致状态部署资源。
- Azure 资源管理器 API：合作伙伴可以直接调用资源管理器 API 来部署资源管理器模板，或生成 API 调用用于直接预配 Azure 服务。
- Terraform：合作伙伴可以使用 Terraform 来部署资源管理器模板或直接部署 Azure 服务。

>[!IMPORTANT]
>- 客户使用归属并不旨在跟踪系统集成商、托管服务提供商或用于部署和管理在 Azure 上运行的软件的工具。
>
>- 客户使用归属适用于新部署，不支持标记已部署的现有资源。
>
>- 对于发布到 Azure Marketplace 的[Azure 应用程序](./partner-center-portal/create-new-azure-apps-offer.md)产品/服务，需要客户使用归属。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>创建 GUID

GUID 是具有32十六进制数字的唯一引用标识符。 若要创建用于跟踪的 GUID，应使用 GUID 生成器。 Azure 存储团队已创建 [GUID 生成器窗体](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR3i8TQB_XnRAsV3-7XmQFpFUMVRVVFFLTDFLS0E2QzNYSkFZR1U3WVJCTSQlQCN0PWcu)，它将通过电子邮件向你发送格式正确的 GUID，并可在不同的跟踪系统中重复使用。

> [!NOTE]
> 强烈建议使用[Azure 存储的 guid 生成器窗体](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR3i8TQB_XnRAsV3-7XmQFpFUMVRVVFFLTDFLS0E2QzNYSkFZR1U3WVJCTSQlQCN0PWcu)创建 guid。 有关详细信息，请参阅[常见问题解答](#faq)。

建议为每个产品/服务和每个产品的分销渠道创建唯一的 GUID。 如果不希望拆分报告，则可以选择对产品的多个分销渠道使用单个 GUID。

如果使用模板部署产品，并且它在 Azure Marketplace 和 GitHub 上都可用，则可以创建并注册两个不同的 GUID：

- Azure 市场中的产品 A
- GitHub 中的产品 A

报表是通过 Microsoft 合作伙伴网络 ID 和 GUID 来完成的。

你还可以通过在计划（其中计划是产品/服务的变体）之间注册附加 Guid 和更改 Guid，在更精细的级别上跟踪使用情况。

## <a name="register-guids"></a>注册 Guid

必须在合作伙伴中心注册 Guid，才能启用客户使用归属。

将 GUID 添加到模板或用户代理中并在合作伙伴中心注册 GUID 后，会跟踪未来的部署。

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard)。

1. 注册为[商业 marketplace 发布者](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv)。

   * 合作伙伴必须[在合作伙伴中心提供一个配置文件](https://docs.microsoft.com/azure/marketplace/become-publisher)。 我们建议在 Azure 市场或 AppSource 中列出产品/服务。
   * 合作伙伴可以注册多个 GUID。
   * 合作伙伴可以注册非 marketplace 解决方案模板和产品/服务的 Guid。

1. 在右上角，选择 "设置" 齿轮图标，然后选择 "**开发人员设置**"。

1. 在 "**帐户设置" 页**上，选择 "**添加跟踪 GUID"。**

1. 在 " **GUID** " 框中，输入你的跟踪 GUID。 只输入 GUID，不要包括 **pid-** 前缀。 在 "**说明**" 框中，输入产品/服务名称或说明。

1. 若要注册多个 GUID，请再次选择“添加跟踪 GUID”。**** 页面上会显示其他框。

1. 选择“保存”。 

## <a name="use-resource-manager-templates"></a>使用 Resource Manager 模板
许多合作伙伴解决方案都是使用 Azure 资源管理器模板部署的。 如果你有一个在 Azure Marketplace 中提供的资源管理器模板，在 GitHub 上或作为快速入门，修改模板以启用客户使用归属的过程是直接的。

> [!NOTE]
> 有关创建和发布解决方案模板的详细信息，请参阅
> * [创建和部署你的第一个资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。
>* [Azure 应用程序产品](./partner-center-portal/create-new-azure-apps-offer.md)。
>* 视频：[为 Azure Marketplace 构建解决方案模板和托管应用程序](https://channel9.msdn.com/Events/Build/2018/BRK3603)。


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

若要启用客户使用归属，请在设计 API 调用时，在请求的用户代理标头中包含 GUID。 为每个产品/服务或 SKU 添加 GUID。 使用 **pid-** 前缀设置字符串的格式，并包含合作伙伴生成的 GUID。 下面是要插入到用户代理的 GUID 格式的示例：

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
有关详细信息，请参阅[Azure SDK for Go](https://docs.microsoft.com/azure/go/)。

## <a name="use-terraform"></a>使用 Terraform

通过 Azure 提供商的1.21.0 版本提供对 Terraform 的支持： [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019)。  此支持适用于通过 Terraform 部署解决方案的所有合作伙伴以及 Azure 提供商（版本1.21.0 或更高版本）部署和计量的所有资源。

用于 Terraform 的 Azure 提供程序添加了一个名为[*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id)的新可选字段，你可以在其中指定用于解决方案的跟踪 GUID。 此字段的值还可源自*ARM_PARTNER_ID*环境变量。

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
如果合作伙伴要通过客户使用情况所跟踪的 Terraform 获取其部署，需要执行以下操作：

* 创建 GUID （应为每个产品/服务或 SKU 添加 GUID）
* 更新其 Azure 提供程序，将*partner_id*的值设置为 GUID （不要将 guid 预修复为 "pid-"，只需将其设置为实际 GUID）


## <a name="verify-the-guid-deployment"></a>验证 GUID 部署

修改模板并运行测试部署后，使用以下 PowerShell 脚本检索已部署和标记的资源。

可以使用该脚本来验证 GUID 是否已成功添加到资源管理器模板。 此脚本不适用于资源管理器 API 或 Terraform 部署。

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

可以在合作伙伴中心仪表板（[https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)）中找到客户使用情况归属的报表。 若要查看报表，必须使用合作伙伴中心凭据进行登录。 如果在报告或登录时遇到任何问题，请按照获取支持部分中的说明创建支持请求。

选择 "合作伙伴关联类型" 下拉列表中的 "跟踪的模板" 以查看报表。

![客户使用归属的报告](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>通知客户

合作伙伴应向客户通知使用客户使用情况归属的部署。 Microsoft 将向合作伙伴报告与这些部署关联的 Azure 使用情况。 以下示例包含可用于向客户告知这些部署的内容。 请将示例中的 \<PARTNER> 替换为自己的公司名称。 合作伙伴应确保通知与其数据隐私和收集政策保持一致，包括供客户选择将自己从跟踪中排除的选项。

### <a name="notification-for-resource-manager-template-deployments"></a>有关资源管理器模板部署的通知

部署此模板时，Microsoft 将能够根据部署的 Azure 资源识别 \<PARTNER> 软件的安装。 Microsoft 能够关联用于支持该软件的 Azure 资源。 Microsoft 收集此信息的目的在于提供其产品的最佳体验及运营其业务。 此数据将根据 Microsoft 的隐私政策进行收集和管理，隐私政策可在 https://www.microsoft.com/trustcenter 中找到。

### <a name="notification-for-sdk-or-api-deployments"></a>有关 SDK 或 API 部署的通知

部署 \<PARTNER> 软件时，Microsoft 能够根据部署的 Azure 资源识别 \<PARTNER> 软件的安装。 Microsoft 能够关联用于支持该软件的 Azure 资源。 Microsoft 收集此信息的目的在于提供其产品的最佳体验及运营其业务。 此数据将根据 Microsoft 的隐私政策进行收集和管理，隐私政策可在 https://www.microsoft.com/trustcenter 中找到。

## <a name="get-support"></a>获取支持

有两个支持通道取决于你面临的问题。

如果在合作伙伴中心遇到任何问题，如查看客户使用情况报表或登录，请在此处创建支持请求，其中包含合作伙伴中心支持团队：[https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

如果你在一般情况下需要有关 Marketplace 载入和/或客户使用情况的帮助，例如，如何设置客户使用归属，请遵循以下步骤：

1. 转到[支持页](https://go.microsoft.com/fwlink/?linkid=844975)。

1. 在“问题类型”下，选择“市场加入”。********

1. 选择问题的“类别”：****

   - 对于使用情况关联的问题，请选择“其他”。****
   - 有关 Azure Marketplace 的访问问题，请选择 "**访问问题**"。

     ![选择问题类别](media/marketplace-publishers-guide/lu-article-incident.png)

1. 选择“开始请求”****。

1. 在下一页上输入所需的值。 选择**继续**。

1. 在下一页上输入所需的值。

   > [!IMPORTANT]
   > 在“事件标题”框中，输入“ISV 使用情况跟踪”。******** 请详细描述问题。

   ![在事件标题中输入“ISV 使用情况跟踪”](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. 填写表单，然后选择“提交”。****

你还可以从 Microsoft 合作伙伴技术顾问那里获得有关技术售销售、部署和应用开发方案的技术指导，以了解并纳入客户使用归属。

### <a name="how-to-submit-a-technical-consultation-request"></a>如何提交技术咨询请求

1. 访问[合作伙伴技术服务](https://partner.microsoft.com/training/partner-technical-services)。
1. 选择 "云基础结构和管理"，将打开一个新页面供你查看技术旅程。
1. 在 "部署服务" 下，单击 "提交请求" 按钮
1. 使用 MSA （MPN 帐户）或 AAD （合作伙伴仪表板帐户）登录;根据你的登录凭据，将打开一个在线请求窗体：
    * 完成/查看联系信息。
    * 可以预先填充咨询详细信息，也可以从下拉项中进行选择。
    * 输入问题的标题和描述（提供尽可能多的详细信息）。
1. 单击“提交”

[使用技术售销售和部署服务](https://support.microsoft.com/help/3121537/using-technical-presales-and-deployment-services#request%20a%20technical%20consultation)在屏幕截图中查看分步说明。

### <a name="whats-next"></a>后续步骤

Microsoft 合作伙伴技术顾问将联系你，以设置对你的需求范围的调用。

## <a name="faq"></a>常见问题解答

**向模板添加 GUID 有何好处？**

Microsoft 为合作伙伴提供其解决方案的客户部署视图，并深入了解其影响使用情况。 Microsoft 与合作伙伴可以使用此信息来促进销售团队之间的更密切合作。 Microsoft 与合作伙伴可以使用此数据来获取单个合作伙伴对 Azure 增长的影响的更加一致视图。

**添加 GUID 后是否可对其进行更改？**

是，客户或实现合作伙伴可以自定义模板，并且可以更改或删除 GUID。 建议合作伙伴主动描述资源的角色，并向客户和合作伙伴提供 GUID，以防止对 GUID 进行删除或编辑。 更改 GUID 只会影响新的部署和资源，而不会影响现有部署和资源。

**我是否可以跟踪通过非 Microsoft 存储库（如 GitHub）部署的模板？**

是，只要部署模板时存在 GUID，就会跟踪使用情况。 合作伙伴仍必须注册其 Guid。

**客户是否也会收到报告？**

客户可在 Azure 门户中跟踪各个资源或客户定义资源组的使用情况。 客户看不到 GUID 的使用情况。

**此方法是否类似于记录的数字合作伙伴（DPOR）？**

这种将部署和使用情况与合作伙伴解决方案关联的新方法提供将合作伙伴解决方案与 Azure 使用情况连接的机制。 DPOR 旨在将咨询（系统集成商）或管理（托管服务提供商）合作伙伴与客户的 Azure 订阅关联。

**使用 Azure 存储的 GUID 生成器窗体有何益处？**

Azure 存储的 GUID 生成器窗体可确保生成所需格式的 GUID。 此外，如果使用任何 Azure 存储的数据平面跟踪方法，则可以利用相同 GUID 进行市场控制平面跟踪。 这样，你将可以利用 Partner 属性的单个统一 GUID，而无需维护单独的多个 GUID。

**能否在 Azure Marketplace 中使用解决方案模板产品的专用自定义 VHD？**

不，不可以。 虚拟机映像必须来自 Azure Marketplace，请参阅： [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)。

你可以使用自定义 VHD 在 marketplace 中创建 VM 产品/服务，并将其标记为 "专用"，使任何人都无法看到它。 然后在解决方案模板中引用此 VM。

**未能更新主模板的*contentVersion*属性？**

在某些情况下，可能会出现 bug，原因是使用来自另一个模板的 TemplateLink 部署，该模板需要较早的 contentVersion。 解决方法是使用元数据属性：

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
