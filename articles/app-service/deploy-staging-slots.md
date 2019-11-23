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
ms.topic: article
ms.date: 09/19/2019
ms.author: cephalin
ms.custom: fasttrack-edit
ms.openlocfilehash: 7f98ba9851216737712b6be1ec29156ba0b1a68b
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382272"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>设置 Azure 应用服务中的过渡环境
<a name="Overview"></a>

When you deploy your web app, web app on Linux, mobile back end, or API app to [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714), you can use a separate deployment slot instead of the default production slot when you're running in the **Standard**, **Premium**, or **Isolated** App Service plan tier. Deployment slots are live apps with their own host names. 两个部署槽（包括生产槽）之间的应用内容与配置元素可以交换。 

将应用程序部署到非生产槽具有以下优点：

* 可以在分阶段部署槽中验证应用更改，然后将其与生产槽交换。
* 首先将应用部署到槽，然后将其交换到生产，这确保槽的所有实例都已准备好，然后交换到生产。 部署应用时，这样可避免停机。 流量重定向是无缝的，且不会因交换操作而删除任何请求。 You can automate this entire workflow by configuring [auto swap](#Auto-Swap) when pre-swap validation isn't needed.
* 交换后，具有以前分阶段应用的槽现在具有以前的生产应用。 如果交换到生产槽的更改与预期不同，可以立即执行同一交换来收回“上一已知的良好站点”。

每种应用服务计划层支持不同数量的部署槽。 There's no additional charge for using deployment slots. To find out the number of slots your app's tier supports, see [App Service limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). 

To scale your app to a different tier, make sure that the target tier supports the number of slots your app already uses. For example, if your app has more than five slots, you can't scale it down to the **Standard** tier, because the **Standard** tier supports only five deployment slots. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Add a slot
应用必须在“标准”、“高级”或“独立”层中运行，才能启用多个部署槽位。

1. 在 [Azure 门户](https://portal.azure.com/)中，打开应用的[资源页](../azure-resource-manager/manage-resources-portal.md#manage-resources)。

2. In the left pane, select **Deployment slots** > **Add Slot**.
   
    ![添加新部署槽](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > If the app isn't already in the **Standard**, **Premium**, or **Isolated** tier, you receive a message that indicates the supported tiers for enabling staged publishing. At this point, you have the option to select **Upgrade** and go to the **Scale** tab of your app before continuing.
   > 

3. In the **Add a slot** dialog box, give the slot a name, and select whether to clone an app configuration from another deployment slot. Select **Add** to continue.
   
    ![Configuration source](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    You can clone a configuration from any existing slot. 可以克隆的设置包括应用设置、连接字符串、语言框架版本、Web 套接字、HTTP 版本和平台位数。

4. After the slot is added, select **Close** to close the dialog box. The new slot is now shown on the **Deployment slots** page. By default, **Traffic %** is set to 0 for the new slot, with all customer traffic routed to the production slot.

5. Select the new deployment slot to open that slot's resource page.
   
    ![Deployment slot title](./media/web-sites-staged-publishing/StagingTitle.png)

    过渡槽具有管理页面，就像任何其他应用服务应用一样。 可以更改此槽的配置。 槽的名称将出现在页面顶部，提醒你正在查看部署槽位。

6. Select the app URL on the slot's resource page. The deployment slot has its own host name and is also a live app. To limit public access to the deployment slot, see [Azure App Service IP restrictions](app-service-ip-restrictions.md).

即使从其他槽克隆设置，新部署槽位也无内容。 For example, you can [publish to this slot with Git](app-service-deploy-local-git.md). 可以从其他存储库分支或不同的存储库部署到槽。 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>What happens during a swap

### <a name="swap-operation-steps"></a>Swap operation steps

When you swap two slots (usually from a staging slot into the production slot), App Service does the following to ensure that the target slot doesn't experience downtime:

1. Apply the following settings from the target slot (for example, the production slot) to all instances of the source slot: 
    - [Slot-specific](#which-settings-are-swapped) app settings and connection strings, if applicable.
    - [Continuous deployment](deploy-continuous-deployment.md) settings, if enabled.
    - [App Service authentication](overview-authentication-authorization.md) settings, if enabled.
    
    Any of these cases trigger all instances in the source slot to restart. During [swap with preview](#Multi-Phase), this marks the end of the first phase. The swap operation is paused, and you can validate that the source slot works correctly with the target slot's settings.

1. Wait for every instance in the source slot to complete its restart. If any instance fails to restart, the swap operation reverts all changes to the source slot and stops the operation.

1. If [local cache](overview-local-cache.md) is enabled, trigger local cache initialization by making an HTTP request to the application root ("/") on each instance of the source slot. Wait until each instance returns any HTTP response. Local cache initialization causes another restart on each instance.

1. If [auto swap](#Auto-Swap) is enabled with [custom warm-up](#Warm-up), trigger [Application Initiation](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) by making an HTTP request to the application root ("/") on each instance of the source slot.

    If `applicationInitialization` isn't specified, trigger an HTTP request to the application root of the source slot on each instance. 
    
    If an instance returns any HTTP response, it's considered to be warmed up.

1. If all instances on the source slot are warmed up successfully, swap the two slots by switching the routing rules for the two slots. After this step, the target slot (for example, the production slot) has the app that's previously warmed up in the source slot.

1. Now that the source slot has the pre-swap app previously in the target slot, perform the same operation by applying all settings and restarting the instances.

At any point of the swap operation, all work of initializing the swapped apps happens on the source slot. The target slot remains online while the source slot is being prepared and warmed up, regardless of where the swap succeeds or fails. To swap a staging slot with the production slot, make sure that the production slot is always the target slot. This way, the swap operation doesn't affect your production app.

### <a name="which-settings-are-swapped"></a>交换哪些设置？

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

To configure an app setting or connection string to stick to a specific slot (not swapped), go to the **Configuration** page for that slot. Add or edit a setting, and then select **deployment slot setting**. Selecting this check box tells App Service that the setting is not swappable. 

![槽设置](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>交换两个槽 
You can swap deployment slots on your app's **Deployment slots** page and the **Overview** page. For technical details on the slot swap, see [What happens during swap](#AboutConfiguration).

> [!IMPORTANT]
> Before you swap an app from a deployment slot into production, make sure that production is your target slot and that all settings in the source slot are configured exactly as you want to have them in production.
> 
> 

To swap deployment slots:

1. Go to your app's **Deployment slots** page and select **Swap**.
   
    ![Swap button](./media/web-sites-staged-publishing/SwapButtonBar.png)

    The **Swap** dialog box shows settings in the selected source and target slots that will be changed.

2. 选择所需的“源”和“目标”槽。 目标通常是生产槽。 Also, select the **Source Changes** and **Target Changes** tabs and verify that the configuration changes are expected. When you're finished, you can swap the slots immediately by selecting **Swap**.

    ![完成交换](./media/web-sites-staged-publishing/SwapImmediately.png)

    To see how your target slot would run with the new settings before the swap actually happens, don't select **Swap**, but follow the instructions in [Swap with preview](#Multi-Phase).

3. When you're finished, close the dialog box by selecting **Close**.

If you have any problems, see [Troubleshoot swaps](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>带预览的交换（多阶段交换）

Before you swap into production as the target slot, validate that the app runs with the swapped settings. The source slot is also warmed up before the swap completion, which is desirable for mission-critical applications.

When you perform a swap with preview, App Service performs the same [swap operation](#AboutConfiguration) but pauses after the first step. You can then verify the result on the staging slot before completing the swap. 

If you cancel the swap, App Service reapplies configuration elements to the source slot.

To swap with preview:

1. Follow the steps in [Swap deployment slots](#Swap) but select **Perform swap with preview**.

    ![带预览的交换](./media/web-sites-staged-publishing/SwapWithPreview.png)

    The dialog box shows you how the configuration in the source slot changes in phase 1, and how the source and target slot change in phase 2.

2. When you're ready to start the swap, select **Start Swap**.

    When phase 1 finishes, you're notified in the dialog box. Preview the swap in the source slot by going to `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. When you're ready to complete the pending swap, select **Complete Swap** in **Swap action** and select **Complete Swap**.

    To cancel a pending swap, select **Cancel Swap** instead.

4. When you're finished, close the dialog box by selecting **Close**.

If you have any problems, see [Troubleshoot swaps](#troubleshoot-swaps).

若要自动执行多阶段交换，请参阅[使用 PowerShell 进行自动化操作](#automate-with-powershell)。

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Roll back a swap
如果在槽交换后目标槽（例如，生产槽）中发生任何错误，请通过立即交换相同的两个槽以将槽恢复到其交换前状态。

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Configure auto swap

> [!NOTE]
> Auto swap isn't supported in web apps on Linux.

Auto swap streamlines Azure DevOps scenarios where you want to deploy your app continuously with zero cold starts and zero downtime for customers of the app. When auto swap is enabled from a slot into production, every time you push your code changes to that slot, App Service automatically [swaps the app into production](#swap-operation-steps) after it's warmed up in the source slot.

   > [!NOTE]
   > Before you configure auto swap for the production slot, consider testing auto swap on a non-production target slot.
   > 

To configure auto swap:

1. Go to your app's resource page. Select **Deployment slots** >  *\<desired source slot>*  > **Configuration** > **General settings**.
   
2. For **Auto swap enabled**, select **On**. Then select the desired target slot for **Auto swap deployment slot**, and select **Save** on the command bar. 
   
    ![Selections for configuring auto swap](./media/web-sites-staged-publishing/AutoSwap02.png)

3. 执行代码推送到源槽。 Auto swap happens after a short time, and the update is reflected at your target slot's URL.

If you have any problems, see [Troubleshoot swaps](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Specify custom warm-up

Some apps might require custom warm-up actions before the swap. The `applicationInitialization` configuration element in web.config lets you specify custom initialization actions. The [swap operation](#AboutConfiguration) waits for this custom warm-up to finish before swapping with the target slot. Here's a sample web.config fragment.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

For more information on customizing the `applicationInitialization` element, see [Most common deployment slot swap failures and how to fix them](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

You can also customize the warm-up behavior with one or both of the following [app settings](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: The path to ping to warm up your site. 通过指定以斜杠开头的自定义路径作为值来添加此应用设置。 例如 `/statuscheck`。 默认值为 `/`。 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Valid HTTP response codes for the warm-up operation. 使用以逗号分隔的 HTTP 代码列表添加此应用设置。 An example is `200,202` . If the returned status code isn't in the list, the warmup and swap operations are stopped. 默认情况下，所有响应代码都是有效的。

> [!NOTE]
> The `<applicationInitialization>` configuration element is part of each app start-up, whereas the two warm-up behavior app settings apply only to slot swaps.

If you have any problems, see [Troubleshoot swaps](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Monitor a swap

If the [swap operation](#AboutConfiguration) takes a long time to complete, you can get information on the swap operation in the [activity log](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

On your app's resource page in the portal, in the left pane, select **Activity log**.

交换操作在日志查询中显示为 `Swap Web App Slots`。 可以将其展开，然后选择一个子操作或错误来查看详细信息。

## <a name="route-traffic"></a>路由流量

默认情况下，应用生产 URL (`http://<app_name>.azurewebsites.net`) 的所有客户端请求都将路由到生产槽。 可以将部分流量路由到另一个槽。 如果需要用户对新更新的反馈，但是还没有准备好将其发布到生产环境中，那么这个功能非常有用。

### <a name="route-production-traffic-automatically"></a>自动路由生产流量

To route production traffic automatically:

1. Go to your app's resource page and select **Deployment slots**.

2. 在要路由到的槽的“流量 %”列中，指定一个百分比（介于 0 到 100 之间）以表示要路由的总流量。 选择“保存”。

    ![Setting a traffic percentage](./media/web-sites-staged-publishing/RouteTraffic.png)

After the setting is saved, the specified percentage of clients is randomly routed to the non-production slot. 

After a client is automatically routed to a specific slot, it's "pinned" to that slot for the life of that client session. 在客户端浏览器上，可以通过查看 HTTP 标头中的 `x-ms-routing-name` cookie 来查看会话固定到哪个槽。 路由到“暂存”槽的请求具有 cookie `x-ms-routing-name=staging`。 路由到生产槽的请求具有 cookie `x-ms-routing-name=self`。

   > [!NOTE]
   > Next to the Azure Portal, you can also use the [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) command in the Azure CLI to set the routing percentages from CI/CD tools like DevOps pipelines or other automation systems.
   > 

### <a name="route-production-traffic-manually"></a>手动路由生产流量

除了自动流量路由以外，应用服务也可以将请求路由到特定槽。 This is useful when you want your users to be able to opt in to or opt out of your beta app. 若要手动路由生产流量，请使用 `x-ms-routing-name` 查询参数。

To let users opt out of your beta app, for example, you can put this link on your webpage:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

字符串 `x-ms-routing-name=self` 指定生产槽。 After the client browser accesses the link, it's redirected to the production slot. Every subsequent request has the `x-ms-routing-name=self` cookie that pins the session to the production slot.

To let users opt in to your beta app, set the same query parameter to the name of the non-production slot. 下面是一个示例：

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

By default, new slots are given a routing rule of `0%`, shown in grey. When you explicitly set this value to `0%` (shown in black text), your users can access the staging slot manually by using the `x-ms-routing-name` query parameter. But they won't be routed to the slot automatically because the routing percentage is set to 0. This is an advanced scenario where you can "hide" your staging slot from the public while allowing internal teams to test changes on the slot.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Delete a slot

Go to your app's resource page. Select **Deployment slots** >  *\<slot to delete>*  > **Overview**. Select **Delete** on the command bar.  

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
### <a name="create-a-slot"></a>Create a slot
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Initiate a swap with a preview (multi-phase swap), and apply destination slot configuration to the source slot
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Cancel a pending swap (swap with review) and restore the source slot configuration
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>交换部署槽
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Monitor swap events in the activity log
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Delete a slot
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-arm-templates"></a>Automate with ARM templates

[ARM Templates](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview) are declarative JSON files used to automate the deployment and configuration of Azure resources. To swap slots using ARM templates, you will set two properties on the *Microsoft.Web/sites/slots* and *Microsoft.Web/sites* resources:

- `buildVersion`: this is a string property which represents the current version of the app deployed in the slot. For example: "v1", "1.0.0.1", or "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: this is a string property that specifies what `buildVersion` the slot should have. If the targetBuildVersion does not equal the current `buildVersion`, then this will trigger the swap operation by finding the slot which has the specified `buildVersion`.

### <a name="example-arm-template"></a>Example ARM Template

The following ARM template will update the `buildVersion` of the staging slot and set the `targetBuildVersion` on the production slot. This will swap the two slots. The template assumes you already have a webapp created with a slot named "staging".

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

This ARM template is idempotent, meaning that it can be executed repeatedly and produce the same state of the slots. After the first execution, `targetBuildVersion` will match the current `buildVersion`, so a swap will not be triggered.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automate with the CLI

有关用于部署槽的 [Azure CLI](https://github.com/Azure/azure-cli) 命令，请参阅 [az webapp deployment slot](/cli/azure/webapp/deployment/slot)。

## <a name="troubleshoot-swaps"></a>Troubleshoot swaps

If any error occurs during a [slot swap](#AboutConfiguration), it's logged in *D:\home\LogFiles\eventlog.xml*. It's also logged in the application-specific error log.

Here are some common swap errors:

- An HTTP request to the application root is timed. The swap operation waits for 90 seconds for each HTTP request, and retries up to 5 times. If all retries are timed out, the swap operation is stopped.

- Local cache initialization might fail when the app content exceeds the local disk quota specified for the local cache. For more information, see [Local cache overview](overview-local-cache.md).

- During [custom warm-up](#Warm-up), the HTTP requests are made internally (without going through the external URL). They can fail with certain URL rewrite rules in *Web.config*. For example, rules for redirecting domain names or enforcing HTTPS can prevent warm-up requests from reaching the app code. To work around this issue, modify your rewrite rules by adding the following two conditions:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Without a custom warm-up, the URL rewrite rules can still block HTTP requests. To work around this issue, modify your rewrite rules by adding the following condition:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Some [IP restriction rules](app-service-ip-restrictions.md) might prevent the swap operation from sending HTTP requests to your app. IPv4 address ranges that start with `10.` and `100.` are internal to your deployment. You should allow them to connect to your app.

- After slot swaps, the app may experience unexpected restarts. This is because after a swap, the hostname binding configuration goes out of sync, which by itself doesn't cause restarts. However, certain underlying storage events (such as storage volume failovers) may detect these discrepancies and force all worker processes to restart. To minimize these types of restarts, set the [`WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` app setting](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) on *all slots*. However, this app setting does *not* work with Windows Communication Foundation (WCF) apps.

## <a name="next-steps"></a>后续步骤
[阻止对非生产槽进行访问](app-service-ip-restrictions.md)
