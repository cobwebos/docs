---
title: ExpressRoute：如何为播发路由配置自定义警报
description: 本文介绍了如何使用 Azure 自动化和逻辑应用来监视从 ExpressRoute 网关播发到本地网络的路由数量，以避免达到 200 个路由的限制。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: 67591e9227ff32e81b973c181da2c1374f0ded47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766667"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>配置自定义警报来监视播发路由

可以根据本文的要求，使用 Azure 自动化和逻辑应用来持续监视从 ExpressRoute 网关播发到本地网络的路由数量。 可以通过监视来避免达到 [200 个路由的限制](expressroute-faqs.md#how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering)。

**Azure 自动化**允许你自动执行存储在 runbook中的自定义 PowerShell 脚本。 使用本文中的配置时，可以通过 runbook 中包含的 PowerShell 脚本来查询一个或多个 ExpressRoute 网关。 它收集的数据集包含资源组、ExpressRoute 网关名称以及在本地播发的网络前缀数量。

**Azure 逻辑应用**会计划一个可调用 Azure 自动化 runbook 的自定义工作流。 runbook 的执行使用作业来完成。 在数据收集操作运行后，Azure 逻辑应用工作流会对数据进行分类，并会根据高于或低于某个预定义阈值的网络前缀数的匹配条件向目标电子邮件地址发送消息。

### <a name="workflow"></a><a name="workflow"></a>工作流

自定义警报的设置基于三个主要步骤：

1. 使用“运行方式”帐户和权限创建自动化帐户。

2. 创建并配置 runbook。

3. 如果数目大于阈值（例如 160），则创建一个逻辑应用来触发自动化帐户并发送警报电子邮件。

## <a name="before-you-begin"></a><a name="before"></a>准备工作

在开始配置之前，请验证是否符合以下条件：

* 你的部署中至少有一个 ExpressRoute 网关。

* 你对 Azure 自动化中的[运行方式帐户](../automation/manage-runas-account.md)有一个基本的了解。

* 你熟悉 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。

* 你熟悉如何使用 Azure PowerShell。 需要使用 Azure PowerShell 在 ExpressRoute 网关上收集网络前缀。 有关一般 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/?view=azps-4.1.0)。

### <a name="notes-and-limitations"></a><a name="limitations"></a>说明和限制

* 本文中讨论的自定义警报是一个加载项，可实现更好的操作和控制。 它不能替代 ExpressRoute 中的原生警报。
* ExpressRoute 网关的数据收集操作在后台运行。 运行时间可能会超出预期。 若要避免作业队列，必须正确设置工作流重复周期。
* 通过脚本或 ARM 模板进行的部署可能比自定义警报触发器更快。 这可能会导致 ExpressRoute 网关中的网络前缀数量增长为超过 200 个路由的限制。

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>创建和配置帐户

在 Azure 门户中创建自动化帐户时，会自动创建[运行方式](../automation/automation-security-overview.md#run-as-accounts)帐户。 此帐户执行以下操作：

* 使用自签名证书创建 Azure Active Directory (Azure AD) 应用程序。 运行方式帐户本身具有默认情况下需要每年续订的证书。

* 在 Azure AD 中为应用程序创建服务主体帐户。

* 在使用的 Azure 订阅上为帐户本身分配参与者角色 (RBAC)。 此角色使用 runbook 管理 Azure 资源管理器资源。

若要创建自动化帐户，你需要具有特权和权限。 有关信息，请参阅[创建自动化帐户所需的权限](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account)。

### <a name="1-create-an-automation-account"></a><a name="about"></a>1.创建自动化帐户

使用运行方式权限创建自动化帐户。 有关说明，请参阅[创建 Azure 自动化帐户](../automation/automation-quickstart-create-account.md)。

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="添加自动化帐户" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2.为运行方式帐户分配角色

默认情况下，将为**运行方式**帐户所使用的服务主体分配**参与者**角色。 你可以保留分配给服务主体的默认角色，也可以通过分配[内置角色](../role-based-access-control/built-in-roles.md)（例如，读取者）或[自定义角色](../active-directory/users-groups-roles/roles-create-custom.md)来限制权限。

 请使用以下步骤来确定分配给运行方式帐户所使用的服务主体的角色：

1. 导航到自动化帐户。 导航到“帐户设置”，然后选择“运行方式帐户”。 

2. 选择“角色”，以查看正在使用的角色定义。

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="添加自动化帐户":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>创建并配置 runbook

### <a name="1-install-modules"></a><a name="install-modules"></a>1.安装模块

若要在 Azure 自动化 runbook 中运行 PowerShell cmdlet，需要安装几个其他的 Azure PowerShell Az 模块。 使用以下步骤安装模块：

1. 打开你的 Azure 自动化帐户并导航到“模块”。

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="添加自动化帐户":::

2. 搜索库并导入以下模块：**Az.Accounts**、**Az.Network**、**Az.Automation** 和 **Az.Profile**。

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="添加自动化帐户" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2.创建 runbook

1. 若要创建 PowerShell runbook，请导航到你的自动化帐户。 在“流程自动化”下选择“Runbook”磁贴，然后选择“创建 runbook”。

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="添加自动化帐户":::

2. 选择“创建”以创建 runbook。

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="添加自动化帐户":::

3. 选择新创建的 runbook，然后选择“编辑”。

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="添加自动化帐户":::

4. 在“编辑”中，粘贴 PowerShell 脚本。 可以修改[示例脚本](#script)并使用它来监视一个或多个资源组中的 ExpressRoute 网关。

   在示例脚本中，请注意以下设置：

    * 数组 **$rgList** 包含带 ExpressRoute 网关的资源组的列表。 你可以自定义基于列表的 ExpressRoute 网关。
    * 变量 **$thresholdNumRoutes** 定义从 ExpressRoute 网关播发到本地网络的网络前缀数的阈值。

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

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3.保存并发布 runbook

1. 选择“保存”以保存 runbook 的草稿副本。
2. 选择“发布”，将此 runbook 作为正式版 runbook 发布到自动化帐户中。

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="添加自动化帐户":::

当你运行 PowerShell 脚本时，系统会收集一个值列表：
 
* 资源组

* ExpressRoute 网关名称

* 第一个 BGP 对等机 (peer1) 的 IP 地址

* 第二个 BGP 对等机 (peer2) 的 IP 地址

* 从 ExpressRoute 网关播发到第一个 BGP 对等机 (peer1) 的网络前缀数

* 从 ExpressRoute 网关播发到第二个 BGP 对等机 (peer2) 的网络前缀数

* Timestamp

* 状态，分类为：

  * “正常”（如果路由数小于阈值）
  * “警报”（如果路由数高于阈值）
  * “警告”（如果播发到两个 BGP 对等机的网络前缀的数目不同）

* 警报消息：对状态（正常、警报、警告）的详细说明

PowerShell 脚本会将收集的信息转换为 JSON 输出。 runbook 使用 PowerShell cmdlet [Write-Output](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Write-Output?) 作为输出流，以便将信息传递到客户端。

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4.验证 runbook

创建 runbook 后，必须对其进行验证。 请选择“启动”并检查不同作业流的输出和错误。

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="添加自动化帐户" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>创建并配置逻辑应用

Azure 逻辑应用是集合和操作的所有流程的业务流程协调程序。 在以下各部分中，你将使用逻辑应用构建工作流。

### <a name="workflow"></a>工作流

在此工作流中，我们构建一个逻辑应用来定期监视 ExpressRoute 网关。 如果存在新项，逻辑应用会针对每个项发送电子邮件。 完成后，逻辑应用看起来大致与以下工作流类似：

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="添加自动化帐户":::

### <a name="1-create-a-logic-app"></a>1.创建逻辑应用

在“逻辑应用设计器”中，使用“空白逻辑应用”模板创建逻辑应用。 有关步骤，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)。

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="添加自动化帐户":::

### <a name="2-add-a-trigger"></a>2.添加触发器

每个逻辑应用都由触发器启动。 触发器在发生特定事件时或特定条件得到满足时触发。 每当触发器触发时，Azure 逻辑应用引擎就会创建一个逻辑应用实例来启动并运行工作流。

若要定期运行基于预定义时间计划的逻辑应用，请向工作流添加内置的“重复周期:计划”。 在搜索框中键入“计划”。 选择“触发器”。 从“触发器”列表中选择“重复周期计划”。

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="添加自动化帐户":::

在“重复周期计划”触发器中，可以设置时区以及重复执行该工作流时需遵循的重复周期。 时间间隔和频率合在一起，即可定义逻辑应用的触发器的计划。 若要确定合理的最小重复频率，请考虑以下因素：

* 自动化 runbook 中的 PowerShell 脚本需要一定的时间才能完成。 运行时间取决于要监视的 ExpressRoute 网关数。 重复频率太高会导致作业排队。

* PowerShell 脚本作为作业在后台运行。 它不会立即启动，而是在延迟一段时间（可变）后运行。

* 重复频率太高会在 Azure ExpressRoute 网关上生成不必要的负载。

在工作流配置结束时，可以通过多次运行工作流来检查重复频率的一致性，然后在“运行历史记录”中验证结果。

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="添加自动化帐户" lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3.创建作业

逻辑应用通过连接器访问其他应用、服务和平台。 在此工作流中，下一步是选择连接器以访问先前定义的 Azure 自动化帐户。

1. 在“逻辑应用设计器”中，在“定期”下 选择“新建步骤”。 在“选择操作”和搜索框下，选择“全部” 。
2. 在搜索框中键入“Azure 自动化”，然后进行搜索。 选择“创建作业”。 “创建作业”将用于触发先前创建的自动化 runbook。

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="添加自动化帐户":::

3. 使用一个服务主体登录。 可以使用现有的服务主体，也可以创建一个新的。 若要创建新的服务主体，请参阅[如何使用门户创建可访问资源的 Azure AD 服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。 选择“通过服务主体进行连接”。

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="添加自动化帐户":::

4. 键入一个**连接名称**，然后添加你的**客户端 ID**（应用程序 ID）、**客户端机密**和**租户 ID**。 然后选择“创建”。

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="添加自动化帐户":::

5. 在“创建作业”页上，服务主体应在承载自动化帐户的**资源组**上具有“读取者”角色，并在**自动化帐户**上具有“自动化作业操作员”角色。 此外，请验证是否已将“Runbook 名称”添加为新参数。

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="添加自动化帐户" lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4.获取作业输出

1. 选择“新建步骤”。 搜索“Azure 自动化”。 从“操作”列表中选择“获取作业输出”。 

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="添加自动化帐户":::

2. 在“获取作业输出”页上，指定访问自动化帐户所需的信息。 选择要使用的“订阅”、“资源组”和“自动化帐户”。 在“作业 ID”框内单击。 当“动态内容”列表出现时，选择“作业 ID”。 

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="添加自动化帐户" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5.分析 JSON

“Azure 自动化的‘创建作业’操作”（前面的步骤）的输出中包含的信息会生成一个 JSON 对象。 逻辑应用的“分析 JSON”是一个内置操作，用于基于 JSON 内容中的属性及其值创建用户易记的令牌。 然后，你可以在工作流中使用这些属性。

1. 添加操作。 在“获取作业输出 -> 操作”下，选择“新建步骤”。 
2. 在“选择操作”搜索框中键入“分析 json”以搜索提供此操作的连接器。 在“操作”列表中，选择与要使用的数据操作相对应的“分析 JSON”操作。

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="添加自动化帐户":::

3. 在“内容”框中单击。 当“动态内容”列表出现时，选择“内容”。

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="添加自动化帐户" lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. 分析 JSON 需要一个架构。 可以使用自动化 runbook 的输出来生成架构。 打开一个新的 Web 浏览器会话，运行自动化 runbook 并捕获输出。 返回到“逻辑应用的分析 JSON 数据操作”操作。 在页面底部，选择“使用示例有效负载生成架构”。

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="添加自动化帐户":::

5. 对于“输入或粘贴示例 JSON 有效负载”，请粘贴自动化 runbook 的输出，然后选择“完成”。

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="添加自动化帐户" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. 架构是通过对 JSON 输入有效负载进行分析自动生成的。

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="添加自动化帐户" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6.定义并初始化变量

在工作流的此步骤中，我们创建一个通过电子邮件发送警报所需的条件。 为了对电子邮件正文消息进行灵活的自定义格式设置，我们在工作流中引入了一个辅助变量。

1. 在“获取作业输出”操作下，选择“新建步骤”。  在搜索框中找到并选择“变量”。

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="添加自动化帐户":::

2. 在“操作”列表中，选择“初始化变量”操作。 

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="添加自动化帐户":::

3. 指定变量的名称。 对于“类型”，请选择“字符串” 。 稍后将在工作流中分配变量的**值**。

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="添加自动化帐户" lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7.创建“For each”操作

分析 JSON 后，“分析 JSON 数据操作”操作会将内容存储在“正文”输出中。 为了处理输出，可以创建一个“For each”循环，对数组中的每一项重复执行一个或多个操作。

1. 在“初始化变量”下，选择“添加操作”。 在搜索框中键入“for each”作为筛选器。

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="添加自动化帐户":::

2. 从“操作”列表中选择“For each - 控制”操作。

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="添加自动化帐户":::

3. 单击“从先前的步骤中选择一个输出”文本框。 当“动态内容”列表出现时，请选择“正文”，这是分析的 JSON 的输出。 

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="添加自动化帐户":::

4. 对于 JSON 正文的每个元素，我们需要设置一个条件。 从操作组中选择“控制”。

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="添加自动化帐户":::

5. 在“操作”列表中选择“条件-控制”。  条件-控制是一个控制结构，可将工作流中的数据与特定值或字段进行比较。 然后，可以指定不同的操作，这些操作是否运行取决于数据是否符合条件。

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="添加自动化帐户":::

6. 在“条件”操作的根中，将逻辑操作更改为“或”。

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="添加自动化帐户" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. 检查 ExpressRoute 网关播发到两个 BGP 对等机的网络前缀数的值。 路由数在“动态内容”中的“numRoutePeer1”和“numRoutePeer2”中提供。 在“值”框中，键入“numRoutePeer1”的值。

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="添加自动化帐户":::

8. 若要在条件中添加其他行，请选择“添加”->“添加行”。 在第二个框中，从“动态内容”选择“numRoutePeer2”。

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="添加自动化帐户":::

9. 如果两个动态变量中的一个（numRoute1 或 numRoute2）大于阈值，则逻辑条件为 true。 在此示例中，阈值固定为 160（200 个路由这一最大值的 80%）。 你可以更改阈值来适应你的需求。 为了保持一致性，此值应与 runbook PowerShell 脚本中使用的值相同。

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="添加自动化帐户":::

10. 在“如果为 true”下，格式化并创建可通过电子邮件发送警报的操作。 在“选择操作”**中，搜索并选择“变量”。

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="添加自动化帐户":::

11. 在“变量”中，选择“添加操作”。 在“操作”列表中，选择“设置变量”。 

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="添加自动化帐户":::

12. 在“名称”中，选择前面创建的名为 **EmailBody** 的变量。 对于“值”，请粘贴设置警报电子邮件格式所需的 HTML 脚本。 使用“动态内容”来包括 JSON 正文的值。 配置这些设置后，获得的结果就是：变量 **Emailbody** 包含 HTML 格式的与警报相关的所有信息。

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="添加自动化帐户":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8.添加电子邮件连接器

逻辑应用提供了许多电子邮件连接器。 在此示例中，我们将添加通过电子邮件发送警报所需的 Outlook 连接器。 在“设置变量”下，选择“添加操作”。 在“选择操作”中，在搜索框中键入“发送电子邮件”。

1. 选择“Office 365 Outlook”。

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="添加自动化帐户":::

2. 在“操作”列表中选择“发送电子邮件(V2)”。 

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="添加自动化帐户":::

3. 登录以创建到 Office 365 Outlook 的连接。

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="添加自动化帐户":::

4. 在“正文”字段中，单击“添加动态内容”。 在“动态内容”面板中，添加“Emailbody”变量。 填写“主题”和“收件人”字段。 

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="添加自动化帐户":::

5. “发送电子邮件(V2)”操作完成工作流设置。

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="添加自动化帐户" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9.工作流验证

最后一步是工作流验证。 在“逻辑应用概览”中，选择“运行触发器” 。 选择“重复周期”。 可以在“运行历史记录”中监视和验证工作流。

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="添加自动化帐户":::

## <a name="next-steps"></a>后续步骤

若要详细了解如何自定义工作流，请参阅 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)。
