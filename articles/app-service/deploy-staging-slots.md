---
title: 设置过渡环境
description: 了解如何将应用部署到非生产槽并自动交换到生产环境中。 提高可靠性并消除部署中的应用停机时间。
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 11e133a24ff728cc864e50e898e9db982b186337
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597887"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>设置 Azure 应用服务中的过渡环境
<a name="Overview"></a>

如果将 web 应用、Linux 上的 web 应用、移动后端或 API 应用部署到[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)，则在**标准**、**高级**或**独立**应用服务计划层中运行时，可以使用单独的部署槽而不是默认的生产槽。 部署槽是具有自身主机名的实时应用。 两个部署槽（包括生产槽）之间的应用内容与配置元素可以交换。 

将应用程序部署到非生产槽具有以下优点：

* 可以在分阶段部署槽中验证应用更改，并将其与生产槽交换。
* 首先将应用部署到槽，然后将其交换到生产，这确保槽的所有实例都已准备好，然后交换到生产。 部署应用时，这样可避免停机。 流量重定向是无缝的，且不会因交换操作而删除任何请求。 当不需要预交换验证时，可以通过配置[自动交换](#Auto-Swap)来自动化这整个工作流。
* 交换后，具有以前分阶段应用的槽现在具有以前的生产应用。 如果交换到生产槽的更改与预期不同，可以立即执行同一交换来收回“上一已知的良好站点”。

每种应用服务计划层支持不同数量的部署槽。 使用部署槽不会产生额外的费用。 若要了解应用层支持的槽数，请参阅[应用服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。 

若要将应用缩放到其他层，请确保目标层支持应用业已使用的槽数。 例如，如果应用有 5 个以上的槽，则不能向下缩放到“标准”层，因为“标准”层只支持 5 个部署槽   。 

<a name="Add"></a>

## <a name="add-a-slot"></a>添加槽
应用必须在“标准”、“高级”或“独立”层中运行，才能启用多个部署槽位    。


1. 在 [Azure 门户](https://portal.azure.com/)中搜索并选择“应用服务”  ，然后选择应用。 
   
    ![搜索应用服务](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. 在左窗格中，选择“部署槽” > “添加槽”   。
   
    ![添加新部署槽](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > 如果应用尚未处于“标准”、“高级”或“独立”层级，则你会收到一条消息，其中指示了支持启用过渡发布的层级    。 此时，可选择“升级”  ，转到应用的“缩放”  选项卡，然后继续。
   > 

3. 在“添加槽”对话框中，为槽提供一个名称，并选择是否要从其他部署槽中克隆应用配置  。 选择“添加”以继续  。
   
    ![配置源](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    可以从任何现有槽克隆配置。 可以克隆的设置包括应用设置、连接字符串、语言框架版本、Web 套接字、HTTP 版本和平台位数。

4. 添加槽后，选择“关闭”以关闭对话框  。 新槽现在显示在“部署槽”页面中  。 默认情况下，新槽的“流量 %”设置为 0，所有客户流量都路由到生产槽  。

5. 选择新部署槽打开该槽的资源页。
   
    ![部署槽标题](./media/web-sites-staged-publishing/StagingTitle.png)

    过渡槽具有管理页面，就像任何其他应用服务应用一样。 可以更改此槽的配置。 为了提醒你正在查看部署槽，应用名称显示为** \<应用名称>/\<槽名称>**，应用类型为**应用服务（槽）**。 你还可以在资源组中将槽显示为单独的应用，具有相同的标识。

6. 选择此槽资源页中的应用 URL。 部署槽有其自己的主机名，同时也是动态应用。 若要限制对部署槽的公共访问权限，请参阅[AZURE APP SERVICE IP 限制](app-service-ip-restrictions.md)。

即使从其他槽克隆设置，新部署槽位也无内容。 例如，可以[通过 Git 发布到此槽](app-service-deploy-local-git.md)。 可以从其他存储库分支或不同的存储库部署到槽。

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>交换期间发生的情况

### <a name="swap-operation-steps"></a>交换操作步骤

交换两个槽时（通常是从过渡槽交换到生产槽），应用服务将执行以下操作，以确保目标槽不会出现停机：

1. 将目标槽（例如生产槽）中的以下设置应用到源槽的所有实例： 
    - [槽特定的](#which-settings-are-swapped)应用设置和连接字符串（如果适用）。
    - [持续部署](deploy-continuous-deployment.md)设置（如果已启用）。
    - [应用服务身份验证](overview-authentication-authorization.md)设置（如果已启用）。
    
    出现其中的任何情况都会触发源槽中所有实例的重启。 在[交换并预览](#Multi-Phase)期间，这标志着第一阶段结束。 交换操作将会暂停，你可以使用目标槽的设置验证源槽是否正常工作。

1. 等待源槽中的每个实例完成重启。 如果有任何实例无法重启，交换操作将会还原对源槽所做的所有更改，并停止操作。

1. 如果启用了[本地缓存](overview-local-cache.md)，则会通过对每个源槽实例上的应用程序根 ("/") 发出 HTTP 请求，来触发本地缓存初始化。 等到每个实例返回了任何 HTTP 响应。 本地缓存初始化会导致每个实例再次重启。

1. 如果结合[自定义预热](#Warm-up)启用了[自动交换](#Auto-Swap)，则会通过对每个源槽实例上的应用程序根 ("/") 发出 HTTP 请求，来触发[应用程序初始化](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)。

    如果未指定 `applicationInitialization`，则向每个实例上的源槽的应用程序根触发 HTTP 请求。 
    
    如果某个实例返回了任何 HTTP 响应，则认为该实例已预热。

1. 如果源槽上的所有实例已成功预热，则通过交换两个槽的路由规则来交换这两个槽。 完成此步骤后，目标槽（例如生产槽）包含先前已在源槽中预热的应用。

1. 源槽包含先前位于目标槽中的预交换应用后，通过应用所有设置并重启实例来执行相同的操作。

在执行交换操作期间的任何时候，初始化已交换应用的所有工作都在源槽上发生。 准备和预热源槽时，目标槽将保持联机，而不管交换是成功还是失败。 若要将过渡槽与生产槽交换，请确保生产槽始终是目标槽。 这样，交换操作才不会影响生产应用。

### <a name="which-settings-are-swapped"></a>交换哪些设置？

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

若要将应用设置或连接字符串配置为粘滞到特定的槽（未交换），请转到该槽的“配置”页。**** 添加或编辑某个设置，然后选择“部署槽设置”。**** 选中此复选框可让应用服务知道该设置不可交换。 

![槽设置](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>交换两个槽 
可以在应用的“部署槽”页上以及“概述”页上交换部署槽********。 有关交换槽的技术详细信息，请参阅[交换期间发生的情况](#AboutConfiguration)。

> [!IMPORTANT]
> 将应用从部署槽交换到生产槽之前，请确保生产槽是目标槽，并且已完全根据生产槽中的情况配置了源槽中的所有设置。
> 
> 

若要交换部署槽：

1. 转到应用的“部署槽”页，然后选择“交换”。********
   
    ![“交换”按钮](./media/web-sites-staged-publishing/SwapButtonBar.png)

    “交换”对话框显示将要更改的所选源和目标槽中的设置****。

2. 选择所需的“源”和“目标”槽********。 目标通常是生产槽。 此外，选择“源更改”和“目标更改”选项卡，并验证配置更改是否符合预期********。 完成后，可以通过选择“交换”立即交换槽****。

    ![完成交换](./media/web-sites-staged-publishing/SwapImmediately.png)

    若要在交换实际发生之前查看目标槽如何使用新设置运行，请不要选择“交换”，而是按照[交换预览](#Multi-Phase)中的说明进行操作****。

3. 完成后，选择“关闭”以关闭对话框****。

如果遇到任何问题，请参阅[排查交换问题](#troubleshoot-swaps)。

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>带预览的交换（多阶段交换）

在交换到用作目标槽的生产槽之前，请使用交换的设置验证应用的运行。 源槽在交换完成之前也已预热，这对于任务关键型应用程序是可行的。

在执行交换并预览时，应用服务将执行相同的[交换操作](#AboutConfiguration)，但完成第一个步骤后会暂停。 然后，你可以在完成交换之前验证过渡槽中的结果。 

如果取消交换，应用服务会重新将配置元素应用到源槽。

若要交换并预览：

1. 按照[交换部署槽位](#Swap)中的步骤操作，并选择 "**执行带预览的交换**"。

    ![带预览的交换](./media/web-sites-staged-publishing/SwapWithPreview.png)

    该对话框显示源槽中的配置在阶段 1 中的更改方式，以及源槽和目标槽在阶段 2 中的更改方式。

2. 如果已准备好开始交换，请选择“开始交换”。****

    第 1 阶段完成后，对话框中会显示通知。 通过转到 `https://<app_name>-<source-slot-name>.azurewebsites.net` 来预览源槽中的交换。 

3. 准备完成挂起的交换后，在“交换操作”中选择“完成交换”，然后选择“完成交换”************。

    若要取消挂起的交换，请选择“取消交换”****。

4. 完成后，选择“关闭”以关闭对话框****。

如果遇到任何问题，请参阅[排查交换问题](#troubleshoot-swaps)。

若要自动执行多阶段交换，请参阅[使用 PowerShell 自动执行](#automate-with-powershell)。

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>回滚交换
如果在槽交换后目标槽（例如，生产槽）中发生任何错误，请通过立即交换相同的两个槽以将槽恢复到其交换前状态。

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>配置自动交换

> [!NOTE]
> Linux 上的 Web 应用中不支持自动交换。

自动交换简化了 Azure DevOps 方案，在此方案中，可连续部署应用，无需冷启动且不会给应用的客户造成停机。 启用从某个槽到生产槽的自动交换后，每次将代码更改推送到该槽时，应用服务会在源槽中预热后自动[将应用交换到生产槽](#swap-operation-steps)中。

   > [!NOTE]
   > 在为生产槽配置自动交换之前，请考虑在非生产目标槽上测试自动交换。
   > 

若要配置自动交换：

1. 转到应用的资源页。 选择**部署槽** > *\<所需的源槽>*  > **配置** > **常规设置**。
   
2. 对于“启用自动交换”，请选择“打开”。******** 为“自动交换部署槽”选择所需的目标槽，然后选择在命令栏上“保存”。******** 
   
    ![用于配置自动交换的选项](./media/web-sites-staged-publishing/AutoSwap02.png)

3. 执行代码推送到源槽。 片刻之后，自动交换就会发生，而更新将反映在目标槽的 URL 上。

如果遇到任何问题，请参阅[排查交换问题](#troubleshoot-swaps)。

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>指定自定义预热

某些应用在交换之前可能需要执行自定义的预热操作。 web.config 中的 `applicationInitialization` 配置元素可用于指定自定义初始化操作。 [交换操作](#AboutConfiguration)在与目标槽交换之前等待此自定义预热操作完成。 以下是 web.config 片段的示例。

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

有关自定义 `applicationInitialization` 元素的详细信息，请参阅[最常见的部署槽位交换故障以及如何修复它们](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)。

还可以使用以下一项或两项[应用设置](configure-common.md)来自定义预热行为：

- `WEBSITE_SWAP_WARMUP_PING_PATH`：用于预热站点的 ping 的路径。 通过指定以斜杠开头的自定义路径作为值来添加此应用设置。 例如 `/statuscheck`。 默认值为 `/`。 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`：用于预热操作的有效 HTTP 响应代码。 使用以逗号分隔的 HTTP 代码列表添加此应用设置。 例如 `200,202`。 如果返回的状态代码不在列表中，则预热和交换操作会停止。 默认情况下，所有响应代码都是有效的。

> [!NOTE]
> 每次启动应用都需要运行 `<applicationInitialization>` 配置元素，而两个预热行为应用设置仅适用于槽交换。

如果遇到任何问题，请参阅[排查交换问题](#troubleshoot-swaps)。

## <a name="monitor-a-swap"></a>监视交换

如果[交换操作](#AboutConfiguration)需要很长时间才能完成，可在[活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)中获取有关交换操作的信息。

在门户的应用资源页上的左窗格中，选择“活动日志”****。

交换操作在日志查询中显示为 `Swap Web App Slots`。 可以将其展开，然后选择一个子操作或错误来查看详细信息。

## <a name="route-traffic"></a>路由流量

默认情况下，应用生产 URL (`http://<app_name>.azurewebsites.net`) 的所有客户端请求都将路由到生产槽。 可以将部分流量路由到另一个槽。 如果需要用户对新更新的反馈，但是还没有准备好将其发布到生产环境中，那么这个功能非常有用。

### <a name="route-production-traffic-automatically"></a>自动路由生产流量

若要自动路由生产流量：

1. 转到应用的资源页，然后选择“部署槽”****。

2. 在要路由到的槽的“流量 %”列中，指定一个百分比（介于 0 到 100 之间）以表示要路由的总流量****。 选择“保存”  。

    ![设置流量百分比](./media/web-sites-staged-publishing/RouteTraffic.png)

保存设置后，指定百分比的客户端将随机路由到非生产槽。 

客户端自动路由到特定槽后，在该客户端会话生存期内都将“固定”到该槽。 在客户端浏览器上，可以通过查看 HTTP 标头中的 `x-ms-routing-name` cookie 来查看会话固定到哪个槽。 路由到“暂存”槽的请求具有 cookie `x-ms-routing-name=staging`。 路由到生产槽的请求具有 cookie `x-ms-routing-name=self`。

   > [!NOTE]
   > 在 Azure 门户旁边，还可以使用 Azure CLI 中的[`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set)命令，以设置 CI/CD 工具（如 DevOps 管道或其他自动化系统）中的路由百分比。
   > 

### <a name="route-production-traffic-manually"></a>手动路由生产流量

除了自动流量路由以外，应用服务也可以将请求路由到特定槽。 如果想要让用户能够选择加入或退出 beta 应用，这就非常有用。 若要手动路由生产流量，请使用 `x-ms-routing-name` 查询参数。

例如，若要让用户选择退出 beta 应用，可以在网页中插入以下链接：

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

字符串 `x-ms-routing-name=self` 指定生产槽。 在客户端浏览器访问该链接后，它将重定向到生产槽。 每个后续请求包含一个用于将会话固定到生产槽的 `x-ms-routing-name=self` Cookie。

若要让用户选择加入 beta 应用，请将相同的查询参数设置为非生产槽的名称。 下面是一个示例：

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

默认情况下，为新槽提供 `0%` 的路由规则（以灰色显示）。 将此值显式设置为 `0%` 时（以黑色文本显示），用户可以使用 `x-ms-routing-name` 查询参数手动访问过渡槽。 但是，他们不会自动路由到该槽，因为路由百分比设置为 0。 这是一种高级方案，在其中可以对公众“隐藏”过渡槽，同时允许内部团队测试槽中的更改。

<a name="Delete"></a>

## <a name="delete-a-slot"></a>删除槽

搜索并选择应用。 选择*\<要删除>*  > **概述**的**部署槽** > 槽。 应用程序类型显示为**应用服务（槽）** ，提醒你正在查看部署槽位。 在命令栏上选择“删除”。****  

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
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>启动带预览的交换（多阶段交换）并将目标槽配置应用到源槽
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>取消挂起的交换（带预览的交换）并还原源槽配置
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>交换部署槽
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>在活动日志中监视交换事件
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>删除槽
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>资源管理器模板自动执行

[Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview)是声明性的 JSON 文件，用于自动部署和配置 Azure 资源。 若要通过使用资源管理器模板来交换槽，你将在 " *microsoft/站点/槽*" 和 " *microsoft 网站/站点*" 资源上设置两个属性：

- `buildVersion`：这是一个字符串属性，表示槽中部署的应用的当前版本。 例如：“v1”、“1.0.0.1”或“2019-09-20T11:53:25.2887393-07:00”。
- `targetBuildVersion`：这是一个字符串属性，指定槽应使用哪个 `buildVersion`。 如果 targetBuildVersion 不等于当前的 `buildVersion`，则此属性会通过查找指定了 `buildVersion` 的槽来触发交换操作。

### <a name="example-resource-manager-template"></a>示例资源管理器模板

以下资源管理器模板将更新`buildVersion`过渡槽的，并`targetBuildVersion`在生产槽上设置。 这会交换两个槽。 该模板假设已使用名为“staging”的槽创建了一个 Web 应用。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

此资源管理器模板是幂等的，这意味着它可以重复执行，并生成相同的槽状态。 首次执行后，`targetBuildVersion` 将与当前的 `buildVersion` 匹配，因此不会触发交换。

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>使用 CLI 进行自动化

有关用于部署槽的 [Azure CLI](https://github.com/Azure/azure-cli) 命令，请参阅 [az webapp deployment slot](/cli/azure/webapp/deployment/slot)。

## <a name="troubleshoot-swaps"></a>排查交换问题

如果在[交换槽](#AboutConfiguration)的过程中出现任何错误，该错误会记录在 *D:\home\LogFiles\eventlog.xml* 中。 特定于应用程序的错误日志中也会记录该错误。

下面是一些常见的交换错误：

- 对应用程序根发出的 HTTP 请求已超时。 交换操作将为每个 HTTP 请求等待 90 秒，并最多重试 5 次。 如果所有重试均超时，则表示交换操作已停止。

- 如果应用内容超过了为本地缓存指定的本地磁盘配额，本地缓存初始化可能会失败。 有关详细信息，请参阅[本地缓存概述](overview-local-cache.md)。

- 在[自定义预热](#Warm-up)期间，会在内部发出 HTTP 请求（而不通过外部 URL）。 *在 web.config 中，* 它们可能会失败，并会出现某些 URL 重写规则。例如，重定向域名或强制 HTTPS 的规则可能会阻止预热请求到达应用代码。 若要解决此问题，请修改重写规则，在其中添加以下两个条件：

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 即使不执行自定义预热，URL 重写规则也可能仍会阻止 HTTP 请求。 若要解决此问题，请修改重写规则，在其中添加以下条件：

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- 某些 [IP 限制规则](app-service-ip-restrictions.md)可能会阻止交换操作将 HTTP 请求发送到应用。 以 `10.` 和 `100.` 开头的 IPv4 地址范围是部署内部的地址。 应允许这些地址连接到你的应用。

- 槽交换之后，应用可能会遇到意外的重启。 这是因为，在交换之后，主机名绑定配置会失去同步，这种情况本身不会导致重启。 但是，某些基础存储事件（例如存储卷故障转移）可能会检测到这些差异，因而强制所有工作进程重启。 若要最大程度地减少这种重新启动类型，请在*所有槽*上设置[ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1`应用设置](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig)。 但是，此应用设置不适用于 Windows Communication Foundation (WCF) 应用。**

## <a name="next-steps"></a>后续步骤
[阻止对非生产槽进行访问](app-service-ip-restrictions.md)
