---
title: 为 Azure 应用服务中的 Web 应用设置过渡环境 | Microsoft Docs
description: 了解如何对 Azure 应用服务中的 Web 应用使用分阶段发布。
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
ms.openlocfilehash: 2fabf0d61ffd2f526fab49816eab36a86497a358
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764700"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>设置 Azure 应用服务中的过渡环境
<a name="Overview"></a>

将 Web 应用、Linux 上的 Web 应用、移动后端和 API 应用部署到 [应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)时，如果应用在“标准”或“高级”应用服务计划层下运行，则可以部署到单独的部署槽而不是默认的生产槽。 部署槽实际上是具有自身主机名的实时应用。 两个部署槽（包括生产槽）之间的应用内容与配置元素可以交换。 将应用程序部署到部署槽具有以下优点：

* 可以在分阶段部署槽中验证应用更改，然后将其与生产槽交换。
* 首先将应用部署到槽，然后将其交换到生产，这确保槽的所有实例都已准备好，然后交换到生产。 部署应用时，这样可避免停机。 流量重定向是无缝的，且不会因交换操作而删除任何请求。 当不需要预交换验证时，可以通过配置[自动交换](#Auto-Swap)来自动化这整个工作流。
* 交换后，具有以前分阶段应用的槽现在具有以前的生产应用。 如果交换到生产槽的更改与预期不同，可以立即执行同一交换来收回“上一已知的良好站点”。

每种应用服务计划层支持不同数量的部署槽。 若要了解应用层支持的槽数，请参阅[应用服务限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits)。 若要将应用缩放到其他层，目标层必须支持应用业已使用的槽数。 例如，如果应用有 5 个以上的槽，则不能向下缩放到“标准”层，因为“标准”层只支持 5 个部署槽。

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>添加部署槽
必须在“标准”或“高级”层下运行应用，才能启用多个部署槽。

1. 在 [Azure 门户](https://portal.azure.com/)中，打开应用的[资源边栏选项卡](../azure-resource-manager/resource-group-portal.md#manage-resources)。
2. 选择“部署槽”选项，并单击“添加槽”。
   
    ![添加新部署槽][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > 如果应用尚未处于“标准”或“高级”层，则会收到消息，指示启用过渡支持的层。 此时，可选择“升级”，并导航到应用的“缩放”选项卡，并继续。
   > 
   > 
3. 在“添加槽”边栏选项卡中，为槽提供一个名称，并选择是否要从其他现有部署槽中克隆应用配置。 单击复选标记以继续。
   
    ![配置源][ConfigurationSource1]
   
    第一次添加槽时，只有两种选择：从生产中的默认槽克隆配置或者完全不进行克隆。
    创建多个插槽后，可以从生产槽以外的槽克隆配置：
   
    ![配置源][MultipleConfigurationSources]
4. 在应用的资源边栏选项卡中，单击“部署槽”，并单击部署槽打开该槽的资源边栏选项卡，它包含一组度量值和配置（类似任何其他应用）。 槽的名称将出现在边栏选项卡顶部，提醒你正在查看部署槽。
   
    ![部署槽标题][StagingTitle]
5. 单击此槽边栏选项卡中的应用 URL。 请注意，部署槽有其自己的主机名，同时它也是动态应用。 若要限制对部署槽的公共访问权限，请参阅[应用服务 Web 应用 – 阻止对非生产部署槽的 Web 访问](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)。

创建部署槽后没有任何内容。 可以从其他存储库分支或完全不同的存储库部署到槽。 还可以更改此槽的配置。 使用与内容更新部署槽关联的发布配置文件或部署凭证。  例如，可以[使用 git 发布到此槽](app-service-deploy-local-git.md)。

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>交换哪些设置？
从另一个部署槽克隆配置时，可以编辑克隆的配置。 此外，某些配置元素在交换时遵循内容（不特定于位置），而其他配置元素会在交换之后保留在同一个位置（特定于位置）。 以下列表显示交换槽时会更改的设置。

**已交换的设置**：

* 常规设置 - 例如 Framework 版本、32/64 位、Web 套接字
* 应用设置（可以配置为停在槽中）
* 连接字符串（可以配置为停在槽中）
* 处理程序映射
* 监视和诊断设置
* WebJobs 内容

**不交换的设置**：

* 发布终结点
* 自定义域名
* SSL 证书和绑定
* 缩放设置
* Web 作业计划程序

要将应用设置或连接字符串配置为停在某个槽中（不交换），请访问特定槽的“应用程序设置”边栏选项卡，然后针对应该位于该槽中的配置元素选中“槽设置”框。 如果将配置元素标记为特定于槽，则无法在所有与该应用关联的部署槽之间交换该元素。

![槽设置][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>交换部署槽 
在应用的资源边栏选项卡的“概述”或“部署槽”中，可交换部署槽。

> [!IMPORTANT]
> 将应用从部署槽交换到生产之前，请确保所有非槽特定的设置已完全根据希望它在交换目标中的位置明确地进行配置。
> 
> 

1. 若要交换部署槽，请在应用命令栏或部署槽命令栏中，单击“交换”按钮。
   
    ![“交换”按钮][SwapButtonBar]

2. 确保正确设置交换源和交换目标。 交换目标通常是生产槽。 单击“确定”完成操作。 操作完成后，即已交换部署槽。

    ![完成交换](./media/web-sites-staged-publishing/SwapImmediately.png)

    有关“带预览的交换”交换类型，请参阅[带预览的交换（多阶段交换）](#Multi-Phase)。  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>带预览的交换（多阶段交换）

带预览的交换（或多阶段交换）可简化特定于槽的配置元素（如连接字符串）的验证。
对于任务关键型工作负荷，在应用生产槽的配置时，请验证应用的行为是否符合预期，并且必须在将应用交换到生产之前执行此验证。 带预览的交换正是需要的。

> [!NOTE]
> Linux 上的 Web 应用不支持带预览的交换。

如果使用“带预览的交换”选项（请参阅[交换部署槽](#Swap)），应用服务将执行以下操作：

- 目标槽保持不变，该槽上的现有工作负荷（如生产）不会受影响。
- 将目标槽的配置元素应用到源槽，包括特定于槽的连接字符串和应用设置。
- 使用前面提到的这些配置元素，重启源槽上的工作进程。
- 完成交换后：将准备好的源槽移到目标槽。 目标槽会按手动交换的方式移动到源槽。
- 取消交换时：重新将源槽的配置元素应用到源槽。

可预览应用具体如何使用目标槽配置。 完成验证后，可通过单独的步骤完成交换。 此步骤具有额外优势，源槽已通过所需的配置提前准备好，因此客户端不会遇到停机的情况。  

“适用于部署槽的 Azure PowerShell cmdlet”部分中提供了可用于多阶段交换的 Azure PowerShell cmdlet 示例。

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>配置自动交换
自动交换简化了 DevOps 方案，在此方案中，可连续部署应用，无需冷启动且不会给应用的最终客户造成停机。 将部署槽配置为自动交换到生产槽后，每次将代码更新推送到该槽时，应用服务会在其已在该槽上做好准备之后，自动将该应用交换到生产槽。

> [!IMPORTANT]
> 为某个槽启用自动交换时，请确保槽配置与针对目标槽（通常是生产槽）的配置完全相同。
> 
> 

> [!NOTE]
> Linux 上的 Web 应用中不支持自动交换。

为槽配置自动交换很容易。 执行以下步骤:

1. 在“部署槽”中，选择非生产槽，并在该槽的资源边栏选项卡中选择“应用程序设置”。  
   
    ![][Autoswap1]
2. 针对“自动交换”选择“打开”，在“自动交换槽”中选择所需的目标槽，并在命令栏中单击“保存”。 确保槽的配置与针对目标槽的配置完全相同。
   
    操作完成后，“通知”选项卡会闪烁并显示绿色的“成功”字样。
   
    ![][Autoswap2]
   
   > [!NOTE]
   > 若要测试应用的自动交换，可在“自动交换槽”中选择非生产目标槽，以便先熟悉这个功能。  
   > 
   > 
3. 向该部署槽执行代码推送。 不久之后，自动交换就会发生，而更新将反映在目标槽的 URL 上。

<a name="Rollback"></a>

## <a name="roll-back-a-production-app-after-swap"></a>在交换后回退生产应用
如果在槽交换后在生产中发现任何错误，请立即通过交换相同的两个槽来将槽回滚到交换前状态。

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>交换前的自定义准备工作
某些应用可能需要自定义的准备操作。 web.config 中的 `applicationInitialization` 配置元素允许指定收到请求之前要执行的自定义初始化操作。 交换操作会等待此自定义准备操作完成。 以下是 web.config 片段的示例。

    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostname="[app hostname]" />
    </applicationInitialization>

## <a name="monitor-swap-progress"></a>监视交换进度

有时候（例如，进行交换的应用的预热时间长），交换操作需要一些时间才能完成。 有关交换操作的详细信息，可参阅 [Azure 门户](https://portal.azure.com)中的[活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)。

在门户的应用页的左侧导航中，选择“活动日志”。

交换操作在日志查询中显示为 `Slotsswap`。 可以将其展开，然后选择一个子操作或错误来查看详细信息。

![槽交换的活动日志](media/web-sites-staged-publishing/activity-log.png)

<a name="Delete"></a>

## <a name="delete-a-deployment-slot"></a>删除部署槽
在部署槽的边栏选项卡中，打开部署槽的边栏选项卡，单击“概述”（默认页），并在命令栏中单击“删除”。  

![删除部署槽][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-azure-powershell"></a>使用 Azure PowerShell 进行自动化操作

Azure PowerShell 是一个模块，可提供通过 Windows PowerShell 管理 Azure 的 cmdlet，包括对管理 Azure 应用服务的部署槽的支持。

* 有关安装和配置 Azure PowerShell 的信息以及使用 Azure 订阅对 Azure PowerShell 进行身份验证的信息，请参阅[如何安装和配置 Microsoft Azure PowerShell](/powershell/azure/overview)。  

- - -
### <a name="create-a-web-app"></a>创建 Web 应用
```PowerShell
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>创建部署槽
```PowerShell
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>启动带预览的交换（多阶段交换）并将目标槽配置应用到源槽
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>取消挂起的交换（带预览的交换）并还原源槽配置
```PowerShell
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>交换部署槽
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>在活动日志中监视交换事件
```PowerShell
Get-AzureRmLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-deployment-slot"></a>删除部署槽
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-azure-cli"></a>使用 Azure CLI 进行自动化操作

有关用于部署槽的 [Azure CLI](https://github.com/Azure/azure-cli) 命令，请参阅 [az webapp deployment slot](/cli/azure/webapp/deployment/slot)。

## <a name="next-steps"></a>后续步骤
[Azure 应用服务 Web 应用 – 阻止对非生产部署槽的 Web 访问](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)  
[Linux 应用服务简介](../app-service/containers/app-service-linux-intro.md)  
[Microsoft Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png

