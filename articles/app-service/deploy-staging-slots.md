---
title: 为 Azure 应用服务中的 Web 应用设置过渡环境 | Microsoft Docs
description: 了解如何对 Azure 应用服务中的 Web 应用使用分阶段发布。
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: fd488d475e24bc1aeebfa49b9d81b04ebae449ff
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445601"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>设置 Azure 应用服务中的过渡环境
<a name="Overview"></a>

当你部署 web 应用程序，在 Linux、 移动后端或 API 应用的 web 应用[Azure 应用服务](https://go.microsoft.com/fwlink/?LinkId=529714)，可以使用单独的部署槽而不是默认生产槽运行时**标准**， **premium**，或**独立**应用服务计划层。 部署槽是具有自身主机名的实时应用。 两个部署槽（包括生产槽）之间的应用内容与配置元素可以交换。 

将应用程序部署到非生产槽具有以下优点：

* 可以在分阶段部署槽中验证应用更改，然后将其与生产槽交换。
* 首先将应用部署到槽，然后将其交换到生产，这确保槽的所有实例都已准备好，然后交换到生产。 部署应用时，这样可避免停机。 流量重定向是无缝的，且不会因交换操作而删除任何请求。 您可以通过配置来自动化这整个工作流[自动交换](#Auto-Swap)不需要预交换验证时。
* 交换后，具有以前分阶段应用的槽现在具有以前的生产应用。 如果交换到生产槽的更改与预期不同，可以立即执行同一交换来收回“上一已知的良好站点”。

每种应用服务计划层支持不同数量的部署槽。 没有使用部署槽无任何额外费用。 若要了解的数槽应用层支持的信息，请参阅[应用服务限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits)。 

若要缩放你的应用到不同的层，请确保目标层支持的应用已使用的槽数。 例如，如果您的应用程序具有五个槽，则不能缩放其向下**标准**层，因为**标准**层支持只有五个部署槽。 

<a name="Add"></a>

## <a name="add-a-slot"></a>添加槽
应用必须在“标准”、“高级”或“独立”层中运行，才能启用多个部署槽位    。

1. 在 [Azure 门户](https://portal.azure.com/)中，打开应用的[资源页](../azure-resource-manager/manage-resources-portal.md#manage-resources)。

2. 在左窗格中，选择**部署槽位** > **添加槽**。
   
    ![添加新部署槽](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > 如果应用尚未处于**标准**， **Premium**，或**独立**层，你会收到一条消息，指示启用过渡的发布支持的层。 此时，您可以选择**升级**并转到**规模**继续操作之前应用的选项卡。
   > 

3. 在中**添加槽**对话框中，为槽提供一个名称，并选择是否要克隆的应用程序配置从另一个部署槽。 选择**添加**以继续。
   
    ![配置源](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    可以克隆从任何现有槽的配置。 可以克隆的设置包括应用设置、连接字符串、语言框架版本、Web 套接字、HTTP 版本和平台位数。

4. 添加槽后，选择**关闭**以关闭对话框。 新的槽现在显示在**部署槽位**页。 默认情况下**流量 %** 设置为 0 表示新的槽中，与所有客户流量路由到生产槽。

5. 选择新的部署槽，以打开该槽的资源页。
   
    ![部署槽标题](./media/web-sites-staged-publishing/StagingTitle.png)

    过渡槽具有管理页面，就像任何其他应用服务应用一样。 可以更改此槽的配置。 槽的名称将出现在页面顶部，提醒你正在查看部署槽位。

6. 选择此槽的资源页上的应用程序 URL。 部署槽具有其自己的主机名，同时也是实时应用。 若要限制对部署槽的公共访问权限，请参阅[Azure 应用服务 IP 限制](app-service-ip-restrictions.md)。

即使从其他槽克隆设置，新部署槽位也无内容。 例如，可以[使用发布到此槽 Git](app-service-deploy-local-git.md)。 可以从其他存储库分支或不同的存储库部署到槽。 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>交换过程中发生

### <a name="swap-operation-steps"></a>交换操作步骤

交换时 （通常从生产槽到过渡槽） 的两个槽，应用服务将执行以下操作以确保目标槽不会出现停机时间：

1. 从目标槽 （例如，生产槽） 将以下设置应用到源槽的所有实例： 
    - [特定于槽的](#which-settings-are-swapped)应用设置和连接字符串，如果适用。
    - [连续部署](deploy-continuous-deployment.md)设置，如果已启用。
    - [应用服务身份验证](overview-authentication-authorization.md)设置，如果已启用。
    
    这些情况下触发要重新启动的源槽中的所有实例。 期间[带预览的交换](#Multi-Phase)，这表示第一阶段的结尾。 交换操作将暂停，并且可以验证源槽与目标槽的设置一起正常运行。

1. 等待要完成其重启的源槽中的每个实例。 如果任何实例无法重新启动，交换操作将恢复到源槽的所有更改，并停止操作。

1. 如果[本地缓存](overview-local-cache.md)是启用了，通过发出 HTTP 请求，应用程序根目录 （"/"） 源槽的每个实例上触发本地缓存初始化。 等待，直到每个实例返回的任何 HTTP 响应。 本地缓存初始化每个实例上将导致另一个重新启动。

1. 如果[自动交换](#Auto-Swap)启用了[自定义准备工作](#Warm-up)，触发器[应用程序启动](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)通过发出 HTTP 请求，应用程序根目录 （"/"） 上的源的每个实例槽。

    如果`applicationInitialization`未指定，触发源槽，每个实例上的应用程序根目录的 HTTP 请求。 
    
    如果实例返回任何 HTTP 响应，它被视为已准备好。

1. 如果源槽上的所有实例都已都准备好成功，请通过交换在两个槽位的路由规则交换两个槽。 此步骤后，目标槽 （例如，生产槽） 具有之前已预热源槽中的应用。

1. 现在，源槽已预交换应用程序以前在目标槽中，通过将应用所有设置并重新启动实例执行相同的操作。

交换操作任何时候，所有工作的初始化已交换的应用的源槽上都发生。 源槽时，目标槽保持联机状态准备好并已准备好，无论其中交换成功或失败。 若要交换过渡槽与生产槽，请确保生产槽始终是目标槽。 这样一来，交换操作不会影响生产应用。

### <a name="which-settings-are-swapped"></a>交换哪些设置？
从另一个部署槽克隆配置时，可以编辑克隆的配置。 而其他配置元素后仍保持在同一个槽交换 （特定于位置），某些配置元素在交换 （不特定于位置），遵循内容。 以下列表显示交换槽时会更改的设置。

**已交换的设置**：

* 常规设置，例如 framework 版本、 32/64 位、 web 套接字
* 应用设置（可以配置为停在槽中）
* 连接字符串（可以配置为停在槽中）
* 处理程序映射
* 监视和诊断设置
* 公用证书
* WebJobs 内容
* 混合连接 *
* 虚拟网络集成 *
* 服务终结点 *
* Azure 内容交付网络 *

标有星号 （*） 的功能计划进行粘到槽。 

**不交换的设置**：

* 发布终结点
* 自定义域名
* 私有证书和 SSL 绑定
* 缩放设置
* Web 作业计划程序
* IP 限制
* AlwaysOn
* 协议设置 (HTTPS，TLS 版本，客户端证书)
* 诊断日志设置
* 跨域资源共享 (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->

若要配置要坚持使用某个特定槽中 （不交换） 的应用程序设置或连接字符串，请转到**配置**该槽的页。 添加或编辑设置，并选择**部署槽设置**。 选中此复选框告知应用服务设置不是可交换。 

![槽设置](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>交换两个槽 
可以在应用上交换部署槽**部署槽位**页并**概述**页。 槽交换的技术详细信息，请参阅[交换过程中发生](#AboutConfiguration)。

> [!IMPORTANT]
> 应用程序从部署槽交换到生产环境之前，请确保生产目标槽且完全按照你想要让他们在生产环境中配置源槽中的所有设置。
> 
> 

若要交换部署槽：

1. 转到你的应用**部署槽位**页，然后选择**交换**。
   
    ![交换按钮](./media/web-sites-staged-publishing/SwapButtonBar.png)

    **交换**对话框显示将更改的所选源和目标槽中的设置。

2. 选择所需的“源”和“目标”槽   。 目标通常是生产槽。 此外，请选择**源更改**并**目标更改**选项卡，然后验证是否符合预期的配置更改。 完成后，您可以通过选择立即交换槽**交换**。

    ![完成交换](./media/web-sites-staged-publishing/SwapImmediately.png)

    若要查看如何目标槽交换实际发生之前将运行使用新的设置，请不要选择**交换**，按照中的说明，但[带预览的交换](#Multi-Phase)。

3. 完成后，通过选择关闭该对话框**关闭**。

如果有任何疑问，请参阅[进行故障排除交换](#troubleshoot-swaps)。

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>带预览的交换（多阶段交换）

> [!NOTE]
> Linux 上的 Web 应用不支持带预览的交换。

交换到生产，因为目标槽之前，验证应用程序运行的已交换的设置。 源槽交换完成后，它非常关键的应用程序之前还预热。

当执行带预览的交换时，应用服务会执行相同[交换操作](#AboutConfiguration)但暂停后的第一步。 然后，您就可以完成交换之前验证过渡槽上的结果。 

如果取消该交换，应用服务将重新配置元素应用到源槽。

若要带预览的交换：

1. 按照中的步骤[交换部署槽](#Swap)但选择**执行带预览的交换**。

    ![带预览的交换](./media/web-sites-staged-publishing/SwapWithPreview.png)

    该对话框显示了如何将源槽中的配置更改在阶段 1，以及如何在阶段 2 中更改的源和目标槽。

2. 如果你已准备好启动交换，则选择**启动交换**。

    第 1 阶段完成后，你收到通知对话框中。 通过转到预览源槽交换`https://<app_name>-<source-slot-name>.azurewebsites.net`。 

3. 如果你已准备好完成挂起的交换，则选择**完成交换**中**交换操作**，然后选择**完成交换**。

    若要取消挂起的交换，请选择**取消交换**相反。

4. 完成后，通过选择关闭该对话框**关闭**。

如果有任何疑问，请参阅[进行故障排除交换](#troubleshoot-swaps)。

若要自动执行多阶段交换，请参阅[使用 PowerShell 进行自动化操作](#automate-with-powershell)。

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>回滚交换
如果在槽交换后目标槽（例如，生产槽）中发生任何错误，请通过立即交换相同的两个槽以将槽恢复到其交换前状态。

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>配置自动交换

> [!NOTE]
> 在 Linux 上的 web 应用中不支持自动交换。

自动交换简化了你想要为应用的客户部署持续使用零个冷启动和零停机时间对应用程序的 Azure DevOps 方案。 自动交换启用时从一个槽到生产环境，每次将代码更改推送到该槽，应用服务自动[交换到生产环境的应用程序](#swap-operation-steps)它在源槽预热后。

   > [!NOTE]
   > 配置自动交换为生产槽之前，请考虑测试对非生产目标槽的自动交换。
   > 

若要配置自动交换：

1. 转到应用的资源页。 选择**部署槽位** >  *\<所需的源槽 >*  > **配置** >  **常规设置**。
   
2. 有关**启用自动交换**，选择**上**。 然后选择所需的目标槽**自动交换部署槽**，然后选择**保存**命令栏上。 
   
    ![选择用于配置自动交换](./media/web-sites-staged-publishing/AutoSwap02.png)

3. 执行代码推送到源槽。 一段时间之后, 会发生自动交换，更新会反映在目标槽的 URL。

如果有任何疑问，请参阅[进行故障排除交换](#troubleshoot-swaps)。

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>指定自定义准备工作
当使用[自动交换](#Auto-Swap)，某些应用可能需要交换前的自定义的准备操作。 `applicationInitialization`在 web.config 中的配置元素允许您指定自定义初始化操作。 [交换操作](#AboutConfiguration)等待此自定义的准备工作，然后将与目标槽交换完成。 此处是 web.config 片段的示例。

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

有关自定义 `applicationInitialization` 元素的详细信息，请参阅[最常见的部署槽位交换故障以及如何修复它们](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)。

此外可以自定义一个或两个以下的预热行为[应用设置](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`：要成功进行 ping 操作尝试您的网站的路径。 通过指定以斜杠开头的自定义路径作为值来添加此应用设置。 例如 `/statuscheck`。 默认值为 `/`。 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`：预热操作的有效 HTTP 响应代码。 使用以逗号分隔的 HTTP 代码列表添加此应用设置。 例如， `200,202` 。 如果返回的状态代码不在列表中，则在预热和交换操作会停止。 默认情况下，所有响应代码都是有效的。

如果有任何疑问，请参阅[进行故障排除交换](#troubleshoot-swaps)。

## <a name="monitor-a-swap"></a>监视交换

如果[交换操作](#AboutConfiguration)需要很长时间才能完成，可以获取有关在交换操作的信息[活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)。

在应用程序的资源页上，在门户中，在左窗格中，选择**活动日志**。

交换操作在日志查询中显示为 `Swap Web App Slots`。 可以将其展开，然后选择一个子操作或错误来查看详细信息。

## <a name="route-traffic"></a>路由流量

默认情况下，应用生产 URL (`http://<app_name>.azurewebsites.net`) 的所有客户端请求都将路由到生产槽。 可以将部分流量路由到另一个槽。 如果需要用户对新更新的反馈，但是还没有准备好将其发布到生产环境中，那么这个功能非常有用。

### <a name="route-production-traffic-automatically"></a>自动路由生产流量

若要自动将生产流量路由：

1. 转到应用的资源页并选择**部署槽位**。

2. 在要路由到的槽的“流量 %”列中，指定一个百分比（介于 0 到 100 之间）以表示要路由的总流量  。 选择“保存”。 

    ![设置流量百分比](./media/web-sites-staged-publishing/RouteTraffic.png)

保存设置后，指定的百分比的客户端将随机路由到非生产槽。 

客户端自动路由到特定槽之后, 它的"固定"到该槽的生存期内的客户端会话。 在客户端浏览器上，可以通过查看 HTTP 标头中的 `x-ms-routing-name` cookie 来查看会话固定到哪个槽。 路由到“暂存”槽的请求具有 cookie `x-ms-routing-name=staging`。 路由到生产槽的请求具有 cookie `x-ms-routing-name=self`。

### <a name="route-production-traffic-manually"></a>手动路由生产流量

除了自动流量路由以外，应用服务也可以将请求路由到特定槽。 当你希望用户能够选择加入或退出 beta 应用时，这很有用。 若要手动路由生产流量，请使用 `x-ms-routing-name` 查询参数。

若要让用户选择退出 beta 应用，例如，您可以将此链接在您的网页：

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

字符串 `x-ms-routing-name=self` 指定生产槽。 客户端浏览器访问该链接后，它将重定向到生产槽。 每个后续请求具有`x-ms-routing-name=self`cookie 的会话固定到生产槽。

为了让用户选择加入 beta 应用，请将相同的查询参数设置为非生产槽的名称。 下面是一个示例：

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

默认情况下，新的槽提供的路由规则`0%`，它们以灰色显示。 当您将此值显式设置为`0%`（以黑色文本显示），用户可通过使用手动访问过渡槽`x-ms-routing-name`查询参数。 但是，它们将不会路由到的槽自动因为路由百分比设置为 0。 这是一种高级的方案，其中您可以"隐藏"来自公共过渡槽，同时允许内部团队要测试的槽的更改。

<a name="Delete"></a>

## <a name="delete-a-slot"></a>删除槽

转到应用的资源页。 选择**部署槽位** >  *\<插槽中，以删除 >*  > **概述**。 选择**删除**命令栏上。  

![删除部署槽](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>使用 PowerShell 进行自动化操作

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell 是一个模块，可提供通过 Windows PowerShell 管理 Azure 的 cmdlet，包括对管理 Azure 应用服务的部署槽的支持。

有关安装和配置 Azure PowerShell 的信息以及使用 Azure 订阅对 Azure PowerShell 进行身份验证的信息，请参阅[如何安装和配置 Microsoft Azure PowerShell](/powershell/azure/overview)。  

---
### <a name="create-a-web-app"></a>创建 Web 应用
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>创建槽
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>启动带预览 （多阶段交换） 的交换并将目标槽配置应用到源槽
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>取消挂起的交换 （带预览的交换） 并还原源槽配置
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>交换部署槽
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>监视活动日志中的交换事件
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>删除槽
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>使用 CLI 自动执行

有关用于部署槽的 [Azure CLI](https://github.com/Azure/azure-cli) 命令，请参阅 [az webapp deployment slot](/cli/azure/webapp/deployment/slot)。

## <a name="troubleshoot-swaps"></a>对交换进行故障排除

如果过程中出现任何错误[槽交换](#AboutConfiguration)，在中记录*D:\home\LogFiles\eventlog.xml*。 它也是特定于应用程序的错误日志中记录的。

下面是一些常见的交换错误：

- 对应用程序根目录的 HTTP 请求已超时。 交换操作会等待 90 秒内为每个 HTTP 请求和重试次数最多 5 次。 如果所有重试将超时，交换操作已停止。

- 当应用程序内容超出了指定的本地缓存的本地磁盘配额时，本地缓存初始化可能会失败。 有关详细信息，请参阅[本地缓存概述](overview-local-cache.md)。

- 期间[自定义准备工作](#Warm-up)，HTTP 请求都在内部 （无需通过外部 URL）。 它们会失败，且在特定 URL 重写规则*Web.config*。例如，用于将域名重定向或强制实施 HTTPS 规则可以阻止预热请求访问应用程序代码。 若要解决此问题，请通过添加以下两个条件来修改重写规则：

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 而无需自定义准备工作，URL 重写规则可能仍会阻止 HTTP 请求。 若要解决此问题，请通过添加以下条件来修改重写规则：

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 某些[IP 限制规则](app-service-ip-restrictions.md)可能会阻止将 HTTP 请求发送到您的应用程序交换操作。 IPv4 地址范围以开头`10.`和`100.`内部部署的。 应允许其连接到您的应用程序。

## <a name="next-steps"></a>后续步骤
[阻止对非生产槽进行访问](app-service-ip-restrictions.md)
