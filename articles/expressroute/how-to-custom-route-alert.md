---
title: ExpressRoute：如何配置播发路由的自定义警报
description: 本文介绍如何使用 Azure 自动化和逻辑应用来监视从 ExpressRoute 网关播发到本地网络的路由数量，以避免达到200路由限制。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: cherylmc
ms.openlocfilehash: 42f416cf6f297eb54298a10162e7ba28f7acd1bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84738475"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>配置自定义警报来监视播发路由

本文可帮助你使用 Azure 自动化和逻辑应用来持续监视从 ExpressRoute 网关播发到本地网络的路由数。 监视有助于避免达到[200 路由限制](expressroute-faqs.md#how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering)。

**Azure 自动化**允许自动执行存储在*runbook*中的自定义 PowerShell 脚本。 使用本文中的配置时，runbook 中包含的 PowerShell 脚本可用于查询一个或多个 ExpressRoute 网关。 它收集包含资源组、ExpressRoute 网关名称和在本地公布的网络前缀数量的数据集。

**Azure 逻辑应用**计划调用 Azure 自动化 runbook 的自定义工作流。 Runbook 的执行使用作业完成。 数据收集运行后，Azure 逻辑应用工作流会根据预定义阈值上或以下的网络前缀数量的匹配条件对数据进行分类，并将信息发送到目标电子邮件地址。

### <a name="workflow"></a><a name="workflow"></a>工作流

设置自定义警报基于三个主要步骤：

1. 使用 "运行身份" 帐户和权限创建自动化帐户。

2. 创建并配置 runbook。

3. 如果数字大于阈值（例如，160），则创建一个逻辑应用，该应用会触发自动化帐户并发送警报电子邮件。

## <a name="before-you-begin"></a><a name="before"></a>开始之前

在开始配置之前，请验证是否符合以下条件：

* 你的部署中至少有一个 ExpressRoute 网关。

* 你基本了解 Azure 自动化中的[运行方式帐户](../automation/manage-runas-account.md)。

* 你熟悉[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。

* 你熟悉如何使用 Azure PowerShell。 需要 Azure PowerShell 才能在 ExpressRoute 网关上收集网络前缀。 有关一般 Azure PowerShell 的详细信息，请参阅[Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/?view=azps-4.1.0)。

### <a name="notes-and-limitations"></a><a name="limitations"></a>说明和限制

* 本文中讨论的自定义警报是一个外接程序，可实现更好的操作和控制。 它不能替代 ExpressRoute 中的本机警报。
* ExpressRoute 网关的数据收集在后台运行。 运行时可以比预期更长。 若要避免作业队列，必须正确设置工作流重复项。
* 脚本或 ARM 模板的部署比自定义警报触发器更快。 这可能会导致 ExpressRoute 网关中的网络前缀数量增长超过200个路由的限制。

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>创建和配置帐户

在 Azure 门户中创建自动化帐户时，会自动创建[运行方式](../automation/manage-runas-account.md#types-of-run-as-accounts)帐户。 此帐户采取以下措施：

* 使用自签名证书创建 Azure Active Directory （Azure AD）应用程序。 运行方式帐户本身具有每年需要续订的证书。

* 为 Azure AD 中的应用程序创建服务主体帐户。

* 在使用的 Azure 订阅上为自己分配参与者角色（RBAC）。 此角色使用 runbook 管理 Azure 资源管理器资源。

若要创建自动化帐户，需要权限和权限。 有关信息，请参阅[创建自动化帐户所需的权限](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account)。

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. 创建自动化帐户

使用 "运行身份" 权限创建自动化帐户。 有关说明，请参阅[创建 Azure 自动化帐户](../automation/automation-quickstart-create-account.md)。

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="添加自动化帐户" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. 为运行方式帐户分配角色

默认情况下，"**参与者**" 角色分配给**运行方式**帐户所使用的服务主体。 你可以保留分配给服务主体的默认角色，也可以通过分配[内置角色](../role-based-access-control/built-in-roles.md)（例如 "读取者"）或[自定义角色](../active-directory/users-groups-roles/roles-create-custom.md)来限制权限。

 使用以下步骤确定分配给运行方式帐户所使用的服务主体的角色：

1. 导航到自动化帐户。 导航到 "**帐户设置**"，然后选择 "**运行方式帐户**"。

2. 选择 "**角色**" 以查看正在使用的角色定义。

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="分配角色":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>创建和配置 runbook

### <a name="1-install-modules"></a><a name="install-modules"></a>1. 安装模块

若要在 Azure 自动化 runbook 中运行 PowerShell cmdlet，需要安装几个附加 Azure PowerShell Az 模块。 使用以下步骤安装模块：

1. 打开 Azure 自动化帐户并导航到 "**模块**"。

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="导航到模块":::

2. 搜索库并导入以下模块： **Az. Accounts**、 **az**、 **az**和**az. Profile**。

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="搜索和导入模块" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. 创建 runbook

1. 若要创建 PowerShell runbook，请导航到自动化帐户。 在 "**流程自动化**" 下，选择 " **runbook** " 磁贴，然后选择 "**创建 runbook**"。

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="创建 runbook。":::

2. 选择 "**创建**" 以创建 runbook。

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="选择 "创建"。":::

3. 选择新创建的 runbook，然后选择 "**编辑**"。

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="编辑 runbook":::

4. 在 "**编辑**" 中，粘贴 PowerShell 脚本。 可以在一个或多个资源组中修改和使用此[示例脚本](#script)来监视 ExpressRoute 网关。

   在示例脚本中，请注意以下设置：

    * 数组 **$rgList**包含具有 ExpressRoute 网关的资源组的列表。 你可以自定义基于列表的 ExpressRoute 网关。
    * 变量 **$thresholdNumRoutes**定义从 ExpressRoute 网关播发到本地网络的网络前缀的数量阈值。

#### <a name="example-script"></a><a name="script"></a>示例脚本

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. 保存并发布 runbook

1. 选择 "**保存**" 以保存 runbook 的草稿副本。
2. 选择 "**发布**" 将 runbook 发布为自动化帐户中 runbook 的官方版本。

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="保存并发布 runbook。":::

运行 PowerShell 脚本时，会收集值的列表：
 
* 资源组

* ExpressRoute 网关名称

* 第一个 BGP 对等节点的 IP 地址（peer1）

* 第二个 BGP 对等节点的 IP 地址（peer2）

* 从 ExpressRoute 网关播发到第一个 BGP 对等节点（peer1）的网络前缀数

* 从 ExpressRoute 网关播发到第二个 BGP 对等节点（peer2）的网络前缀数

* Timestamp

* 状态，分类为：

  * 如果路由数小于阈值，则为 "确定"
  * 如果路由数超过阈值，则为 "ALERT"
  * 如果播发到两个 BGP 对等端的网络前缀的数目不同，则为 "WARNING"

* 警报消息，详细说明状态（"正常"、"警报"、"警告"）

PowerShell 脚本将收集的信息转换为 JSON 输出。 Runbook 使用 PowerShell cmdlet[写入输出](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Write-Output?)作为输出流，将信息传递给客户端。

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. 验证 runbook

创建 runbook 后，必须对其进行验证。 选择 "**启动**"，然后检查不同作业流的输出和错误。

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="验证 runbook" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>创建并配置逻辑应用

Azure 逻辑应用是集合和操作的所有过程的业务流程协调程序。 以下部分介绍如何使用逻辑应用构建工作流。

### <a name="workflow"></a>工作流

在此工作流中，你将构建定期监视 ExpressRoute 网关的逻辑应用。 如果存在新项，逻辑应用会针对每个项发送电子邮件。 完成后，逻辑应用看起来大致与以下工作流类似：

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="逻辑应用工作流":::

### <a name="1-create-a-logic-app"></a>1. 创建逻辑应用

在**逻辑应用设计器**中，使用 "**空白逻辑应用**" 模板创建逻辑应用。 有关步骤，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)。

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="空白模板":::

### <a name="2-add-a-trigger"></a>2. 添加触发器

每个逻辑应用都由触发器启动。 当发生特定事件或满足特定条件时触发触发器。 每当触发器触发时，Azure 逻辑应用引擎就会创建一个逻辑应用实例来启动并运行工作流。

若要定期运行基于预定义时间计划的逻辑应用，请将内置 "**定期：计划**" 添加到工作流。 在搜索框中，键入 " **schedule**"。 选择“触发器”。 从 "触发器" 列表中，选择 "**重复计划**"。

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="重复周期：计划":::

在 "重复计划" 触发器中，可以设置时区以及重复该工作流的重复周期。 时间间隔和频率合在一起，即可定义逻辑应用的触发器的计划。 若要建立合理的最小重复频率，请考虑以下因素：

* 自动化 runbook 中的 PowerShell 脚本需要一定的时间才能完成。 运行时取决于要监视的 ExpressRoute 网关数。 重复频率太短将导致作业队列。

* PowerShell 脚本在后台作为作业运行。 它不会立即启动;它在可变延迟之后运行。

* 定期频率太短将在 Azure ExpressRoute 网关上产生不需要的负载。

在工作流配置结束时，您可以通过运行工作流多次来检查定期频率的一致性，然后在**运行历史记录**中验证结果。

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="定期" lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. 创建作业

逻辑应用通过连接器访问其他应用、服务和平台。 此工作流中的下一步是选择连接器以访问先前定义的 Azure 自动化帐户。

1. 在**逻辑应用设计器**中的 "**重复周期**" 下，选择 "**新建步骤**"。 在 "**选择操作**" 和 "搜索" 框中，选择 "**全部**"。
2. 在搜索框中，键入 " **Azure 自动化**" 和 "搜索"。 选择 "**创建作业**"。 **创建作业**将用于触发前面创建的自动化 runbook。

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="创建作业":::

3. 使用服务主体登录。 您可以使用现有的服务主体，也可以创建一个新的服务主体。 若要创建新的服务主体，请参阅[如何使用门户创建可访问资源 Azure AD 服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。 选择 "**与服务主体连接**"。

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="登录":::

4. 键入**连接名称**、添加**客户端 id** （应用程序 Id）、**客户端密钥**和**租户 ID**。 然后选择“创建”。

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="与服务主体连接":::

5. 在 "**创建作业**" 页上，服务主体应在托管自动化帐户的**资源组**上有 "读取者" 角色，并在**自动化帐户**上具有 "自动化作业操作员"。 此外，请验证是否已将**Runbook 名称**添加为新参数。

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="角色" lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. 获取作业输出

1. 选择“新建步骤”。**** 搜索 "Azure 自动化"。 从 "**操作**" 列表中，选择 "**获取作业输出**"。

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="获取作业输出":::

2. 在 "**获取作业输出**" 页上，指定访问自动化帐户所需的信息。 选择要使用的**订阅、资源组**和**自动化帐户**。 在 "**作业 ID** " 框内单击。 显示**动态内容**列表时，选择 "**作业 ID**"。

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="作业 ID" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. 分析 JSON

"Azure 自动化创建作业操作" 的输出中包含的信息（前面的步骤）生成一个 JSON 对象。 逻辑应用**分析 json**是一项内置操作，用于通过 json 内容中的属性及其值创建用户友好标记。 然后，你可以在工作流中使用这些属性。

1. 添加操作。 在 "**获取作业 >操作**" 下，选择 "**新建步骤**"。
2. 在 "**选择操作**" 搜索框中，键入 "分析 json" 以搜索提供此操作的连接器。 在 "**操作**" 列表中，为要使用的数据操作选择 "**分析 JSON** " 操作。

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="分析 JSON":::

3. 在**内容**框内单击。 显示动态内容列表时，选择 "**内容**"。

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="内容" lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. 分析 JSON 需要架构。 可以使用自动化 runbook 的输出生成架构。 打开新的 web 浏览器会话，运行自动化 runbook 并捕获输出。 返回到**逻辑应用 "分析 JSON 数据操作**" 操作。 在页面底部，选择 "**使用示例有效负载生成架构**"。

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="使用示例有效负载生成架构":::

5. 对于 "**输入或粘贴示例 JSON 有效负载**"，请粘贴自动化 runbook 的输出，然后选择 "**完成**"。

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="粘贴示例有效负载" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. 架构是通过分析 JSON 输入有效负载自动生成的。

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="生成架构" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. 定义并初始化变量

在工作流的此步骤中，我们将创建一个条件以通过电子邮件发送警报。 对于电子邮件正文消息的灵活自定义格式设置，在工作流中引入了一个辅助变量。

1. 在 "**获取作业输出" 操作**下，选择 "**新建步骤**"。 在搜索框中，找到并选择 "**变量**"。

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="变量":::

2. 从 "**操作**" 列表中，选择 "**初始化变量**" 操作。

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="初始化变量":::

3. 指定变量的名称。 对于 "**类型**"，选择 "**字符串**"。 稍后将在工作流中分配变量的**值**。

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="字符串" lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. 创建 "For each" 操作

解析 JSON 后，"**分析 JSON 数据操作**" 操作会在*正文*输出中存储内容。 若要处理输出，可以为数组中的每一项创建一个 "For each" 循环。

1. 在 "**初始化变量**" 下，选择 "**添加操作**"。 在搜索框中键入“for each”作为筛选器。

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="控件":::

2. 从 "**操作**" 列表中，选择**每个控件**的操作。

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="对于每个控件":::

3. 在 "**选择前面步骤的输出**" 文本框中单击。 显示**动态内容**列表时，请选择从分析的 JSON 输出的**正文**。

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="正文":::

4. 对于 JSON 正文的每个元素，我们需要设置一个条件。 从 "操作" 组中，选择 "**控件**"。

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="控件":::

5. 在 "**操作**" 列表中，选择 "**条件"-"控件**"。 "条件" 控件是一个控件结构，它将工作流中的数据与特定值或字段进行比较。 然后，可以根据数据是否满足条件来指定要运行的不同操作。

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="Condition 控件":::

6. 在 "**条件**" 操作的根中，将逻辑操作更改为**或**。

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="或" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. 检查 ExpressRoute 网关播发到两个 BGP 对等节点的网络前缀的值。 路由数量在**动态内容**的 "numRoutePeer1" 和 "numRoutePeer2" 中可用。 在 "值" 框中，键入**numRoutePeer1**的值。

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="numRoutesPeer1":::

8. 若要将其他行添加到条件，请选择 "**添加-> 添加行**"。 在第二个框中，从 "**动态内容**" 中选择 " **numRoutePeer2**"。

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="numRoutesPeer2":::

9. 如果两个动态变量（numRoute1 或 numRoute2）中的一个大于阈值，则逻辑条件为 true。 在此示例中，阈值固定为160（200路由的最大值80%）。 您可以更改阈值来满足您的要求。 为了保持一致性，此值应与 runbook PowerShell 脚本中使用的值相同。

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="逻辑条件":::

10. 在 "**如果为 true**" 下，格式化并创建通过电子邮件发送警报的操作。 在 "选择操作" 中，搜索并选择 "**变量**"。

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="如果为 true":::

11. 在 "变量" 中，选择 "**添加操作**"。 在 "**操作**" 列表中，选择 "**设置变量**"。

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="设置变量":::

12. 在 "**名称**" 中，选择前面创建的名为**EmailBody**的变量。 对于 "**值**"，请粘贴设置警报电子邮件格式所需的 HTML 脚本。 使用**动态内容**来包含 JSON 正文的值。 配置这些设置后，结果为变量**Emailbody**包含与警报相关的所有信息，格式为 HTML。

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="设置变量":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. 添加电子邮件连接器

逻辑应用提供了许多电子邮件连接器。 在此示例中，我们将添加 Outlook connector 以通过电子邮件发送警报。 在 "**设置变量**" 下，选择 "**添加操作**"。 在 "**选择操作**" 中，在搜索框中键入 "发送电子邮件"。

1. 选择**Office 365 Outlook**。

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="发送电子邮件":::

2. 在 "**操作**" 列表中，选择 "**发送电子邮件（V2）**"。

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="发送电子邮件（V2）":::

3. 登录以创建与 Office 365 Outlook 的连接。

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="登录":::

4. 在 "**正文**" 字段中，单击 "**添加动态内容**"。 从动态内容面板中，添加变量**Emailbody**。 填写 "**使用者**" 和 "**到**" 字段。

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="正文":::

5. "**发送电子邮件（v2）** " 操作完成工作流设置。

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="发送电子邮件 v2" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. 工作流验证

最后一步是工作流验证。 在**逻辑应用概述**中，选择 "**运行触发器**"。 选择 "**重复周期**"。 可以在**运行历史记录**中监视和验证工作流。

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="运行触发器":::

## <a name="next-steps"></a>后续步骤

若要了解有关如何自定义工作流的详细信息，请参阅[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。
