---
title: 将 Operations Manager 连接到 Azure Monitor |Microsoft Docs
description: 若要保持 System Center Operations Manager 中的现有投资并将扩展功能用于 Log Analytics，可将 Operations Manager 与工作区集成。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/23/2018
ms.author: magoedte
ms.openlocfilehash: 3e11c4dc8aa082723223fb998e599aa514febd3b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369950"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>将 Operations Manager 连接到 Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

若要维护中的现有投资[System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807)并与 Azure Monitor 中使用扩展的功能，可以集成 Operations Manager 与 Log Analytics 工作区。 这允许您利用，同时继续使用 Operations Manager 连接到 Azure Monitor 中的日志的机会：

* 使用 Operations Manager 监视 IT 服务的运行状况
* 保持与支持事件和问题管理的 ITSM 解决方案集成
* 管理部署到本地和公有云 IaaS 虚拟机（使用 Operations Manager 监视）的代理的生命周期

将与 System Center Operations Manager 集成将值添加到你的服务操作策略使用的速度和效率的收集、 存储和分析日志数据从 Operations Manager 中的 Azure Monitor。 Azure 监视器日志查询帮助关联并设法确定问题的错误和显示以您对现有问题管理过程支持的重复周期。 值得注意 Operations Manager 中将查询引擎将检查性能、 事件和警报数据，具有丰富的仪表板和报告功能将此数据公开有意义的方式，展示了 Azure Monitor 的灵活性。

向 Operations Manager 管理组报告的代理从基于你的服务器收集数据[Log Analytics 数据源](agent-data-sources.md)和已在工作区中启用的解决方案。 根据启用的解决方案，其数据也会直接从 Operations Manager 管理服务器到服务，或者出于在代理托管系统上，收集的数据量直接从代理发送到 Log Analytics 工作区。 管理服务器直接将数据转发到服务，数据永远不会写入操作数据库或数据仓库数据库。 当管理服务器断开连接的 Azure 监视器时，它将数据在本地缓存直到重新建立通信。 如果管理服务器由于计划内的维护或计划外的停机而脱机，在管理组中的另一台管理服务器将恢复与 Azure Monitor 的连接。  

下图显示了在 System Center Operations Manager 管理组和 Azure Monitor，包括方向和端口的管理服务器和代理之间的连接。   

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

如果 IT 安全策略不允许网络上的计算机连接到 Internet，可将管理服务器配置为连接到 Log Analytics 网关，以根据启用的解决方案接收配置信息并发送收集的数据。 有关详细信息和有关如何配置 Operations Manager 管理组以通过 Azure Monitor 的 Log Analytics 网关进行通信的步骤，请参阅[将计算机连接到使用 Log Analytics 网关的 Azure Monitor](../../azure-monitor/platform/gateway.md)。  

## <a name="prerequisites"></a>必备组件 
在开始之前，请查看以下要求。

* Azure Monitor 仅支持 System Center Operations Manager 1807、 Operations Manager 1801、 Operations Manager 2016、 Operations Manager 2012 SP1 UR6 或更高版本，以及 Operations Manager 2012 R2 UR2 或更高版本。 Operations Manager 2012 SP1 UR7 和 Operations Manager 2012 R2 UR3 中添加了代理服务器支持。
* 所有 Operations Manager 代理必须满足最低支持要求。 确保代理中安装了最起码的更新，否则 Windows 代理通信可能失败，并在 Operations Manager 事件日志中生成错误。
* Log Analytics 工作区。 有关详细信息，请查看 [Log Analytics 工作区概述](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json)。
* 使用 [Log Analytics 参与者角色](../../azure-monitor/platform/manage-access.md#manage-accounts-and-users)成员帐户在 Azure 中进行身份验证。  

>[!NOTE]
>Azure Api 的最新更改将阻止客户能够成功配置第一次其管理组与 Azure Monitor 之间集成。 对于已将其管理组与该服务进行集成的客户，除非需要重新配置现有连接，否则他们不受影响。  
>已为以下版本的 Operations Manager 发布了新的管理包：
>  
>* 对于 System Center Operations Manager 1801，请从[此处](https://www.microsoft.com/download/details.aspx?id=57173)下载管理包  
>* 对于 System Center 2016 - Operations Manager，请从[此处](https://www.microsoft.com/download/details.aspx?id=57172)下载管理包  
>* 对于 System Center Operations Manager 2012 R2，请从[此处](https://www.microsoft.com/download/details.aspx?id=57171)下载管理包  
>
>此管理包更新不适用于 System Center Operations Manager 1807，后者是版本 1801 的更新版本，而不是产品的完整版本。   

### <a name="network"></a>网络
下面列出所需的 Operations Manager 代理、 管理服务器和操作控制台与 Azure Monitor 进行通信的代理和防火墙配置信息的信息。 从每个组件的流量是从网络传出到 Azure Monitor。   

|资源 | 端口号| 绕过 HTTP 检查|  
|---------|------|-----------------------|  
|**代理**|||  
|\*.ods.opinsights.azure.com| 443 |是|  
|\*.oms.opinsights.azure.com| 443|是|  
|\*.blob.core.windows.net| 443|是|  
|\*.azure-automation.net| 443|是|  
|**管理服务器**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| 是|  
|\*.ods.opinsights.azure.com| 443| 是|  
|*.azure-automation.net | 443| 是|  
|**Operations Manager 控制台到 Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 和 443||  
|\*.microsoft.com| 80 和 443||  
|\*.microsoftonline.com| 80 和 443||  
|\*.mms.microsoft.com| 80 和 443||  
|login.windows.net| 80 和 443||  
|portal.loganalytics.io| 80 和 443||
|api.loganalytics.io| 80 和 443||
|docs.loganalytics.io| 80 和 443||  

### <a name="tls-12-protocol"></a>TLS 1.2 协议
为了确保传输到 Azure Monitor 中的数据的安全性，我们强烈建议你配置代理和管理组至少使用传输层安全 (TLS) 1.2。 我们发现旧版 TLS/安全套接字层 (SSL) 容易受到攻击，尽管目前出于向后兼容，这些协议仍可正常工作，但我们**不建议使用**。 有关其他信息，请查看[使用 TLS 1.2 安全地发送数据](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)。 

## <a name="connecting-operations-manager-to-azure-monitor"></a>将 Operations Manager 连接到 Azure Monitor
执行以下一系列步骤，将 Operations Manager 管理组配置为连接到你的一个 Log Analytics 工作区。

首次向 Log Analytics 工作区注册 Operations Manager 管理组期间，为管理组指定代理配置的选项在操作控制台中不可用。  必须成功向服务注册管理组后，此选项才可用。  若要解决此问题，需使用 Netsh，对运行操作控制台以配置集成的系统，以及管理组中的所有管理服务器进行系统代理配置的更新。  

1. 打开提升的命令指示符。
   a. 转到“启动”，然后键入“cmd”。
   b. 右键单击“命令提示符”然后选择“以管理员身份运行”**。
1. 键入以下命令并按 Enter：

    `netsh winhttp set proxy <proxy>:<port>`

完成以下步骤将与 Azure Monitor 集成后，您可以通过删除配置运行`netsh winhttp reset proxy`，然后使用**配置代理服务器**在操作控制台中的选项以指定的代理或日志分析网关服务器。 

1. 在 Operations Manager 控制台中，选择“**管理**”工作区。
1. 展开 Operations Management Suite 节点，并单击“**连接**”。
1. 单击“**向 Operations Management Suite 注册**”链接。
1. 在“Operations Management Suite 载入向导:身份验证”页上，输入与 OMS 订阅相关联的管理员帐户的电子邮件地址或电话号码和密码，然后单击“登录”。

   >[!NOTE]
   >Operations Management Suite 名称已停用。 
   
1. 成功进行身份验证后，在“Operations Management Suite 载入向导:选择工作区”页上，系统会提示你选择 Azure 租户、订阅和 Log Analytics 工作区。 如果有多个工作区，从下拉列表中选择想要在 Operations Manager 管理组中注册的工作区，并单击“**下一步**”。
   
   > [!NOTE]
   > Operations Manager 一次仅支持一个 Log Analytics 工作区。 连接和与上一个工作区注册到 Azure Monitor 的计算机已从 Azure Monitor。
   > 
   > 
1. 在“Operations Management Suite 载入向导:摘要”页上，确认设置，如果它们正确无误，请单击“创建”。
1. 在“Operations Management Suite 载入向导:完成”页上，单击“关闭”。

### <a name="add-agent-managed-computers"></a>添加代理管理的计算机
配置与 Log Analytics 工作区的集成后，只是建立了与服务的连接，不会从向管理组报告的代理中收集任何数据。 在配置的特定代理管理计算机的 Azure Monitor 收集日志数据之后，这就不会发生之前。 可以单独选择计算机对象，或者选择一个包含 Windows 计算机对象的组。 不能选择包含另一个类实例的组，如逻辑磁盘或 SQL 数据库。

1. 打开 Operations Manager 控制台并选择“**管理**”工作区。
1. 展开 Operations Management Suite 节点，并单击“**连接**”。
1. 在窗格右侧的“操作”标题下单击“**添加计算机/组**”链接。
1. 在“计算机搜索”对话框中，可以搜索 Operations Manager 监视的计算机或组。 选择计算机或组要载入到 Azure Monitor 中，单击**外**，然后单击**确定**。

可以在 Operations 控制台“**管理**”工作区中的 Operations Management Suite 下，查看配置为从“受管理计算机”节点收集数据的计算机和组。 在此处，可根据需要添加或移除计算机和组。

### <a name="configure-proxy-settings-in-the-operations-console"></a>在操作控制台中配置代理设置
如果内部代理服务器之间的管理组和 Azure Monitor，请执行以下步骤。 这些设置集中从管理组管理和分发到代理管理系统包含在要为 Azure Monitor 收集日志数据的作用域中。  当某些解决方案绕过管理服务器并将数据直接发送到服务时，这很有用。

1. 打开 Operations Manager 控制台并选择“**管理**”工作区。
1. 展开 Operations Management Suite，并单击“**连接**”。
1. 在“OMS 连接”视图中，单击“**配置代理服务器**”。
1. 在“Operations Management Suite 向导:代理服务器”页上，选择“使用代理服务器访问 Operations Management Suite”，然后键入带端口号的 URL，例如 http://corpproxy:80，然后单击“完成”。

如果你的代理服务器要求身份验证，执行以下步骤以配置凭据和传播到被管理的计算机到 Azure Monitor 中的管理组的报表所需的设置。

1. 打开 Operations Manager 控制台并选择“**管理**”工作区。
1. 在“**运行方式配置**”下面，选择“**配置文件**”。
1. 打开 **System Center Advisor Run As Profile Proxy** 配置文件。
1. 在运行方式配置文件向导中，单击“添加”以使用运行方式帐户。 可以创建一个[运行方式帐户](https://technet.microsoft.com/library/hh321655.aspx)或使用现有帐户。 此帐户需要有足够的权限以通过代理服务器。
1. 若要设置管理的帐户，请选择“**选定的类、组或对象**”，单击“**选择...**” 然后单击“**组...**” 打开“**组搜索**”框。
1. 搜索然后选择 **Microsoft System Center Advisor Monitoring Server Group**。 选择组后单击“**确定**”以关闭“**组搜索**”框。
1. 单击“**确定**”以关闭“**添加运行方式帐户**”框。
1. 单击“**保存**”以完成该向导并保存更改。

创建连接并配置将收集并报告给 Azure Monitor 的日志数据的代理后，在管理组中，不一定按顺序应用以下配置：

* 运行方式帐户 **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** 随即创建。 它与运行方式配置文件 **Microsoft System Center Advisor Run As Profile Blob** 相关联且目标为两个类 - **收集服务器**和 **Operations Manager 管理组**。
* 两个连接器已创建。  第一个名为**Microsoft.SystemCenter.Advisor.DataConnector** ，通过将转发到 Azure Monitor 生成从管理组中的所有类的实例的所有警报的订阅自动配置。 第二个连接器**Advisor 连接器**，负责与 Azure Monitor 进行通信和共享数据。
* 选择的要在管理组中收集数据的代理和组将添加到 **Microsoft System Center Advisor Monitoring Server Group**。

## <a name="management-pack-updates"></a>管理包更新
配置完成后，Operations Manager 管理组建立与 Azure Monitor 的连接。 管理服务器将与 Web 服务同步，针对与 Operations Manager 集成的已启用解决方案，以管理包的形式接收更新的配置信息。 Operations Manager 会自动检查这些管理包的更新，并在更新可用时下载和导入。 特别是，有两个控制此行为的规则：

* **Microsoft.SystemCenter.Advisor.MPUpdate** -更新基本的 Azure 监视管理包。 默认情况下，每 12 小时运行一次。
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - 更新在工作区中启用的解决方案管理包。 默认情况下，每五 (5) 分钟运行一次。

您可以重写这些两个规则，以防止自动下载通过禁用它们，或修改管理服务器与 Azure Monitorto 的同步频率的频率确定新的管理包是否可用，并应下载。 请按照“[如何重写规则或监视器](https://technet.microsoft.com/library/hh212869.aspx)”的步骤，通过以秒为单位的值修改“**频率**”参数来更改同步计划，或修改“**已启用**”参数禁用规则。 锁定 Operations Manager 管理组类所有对象的替代项。

若要继续按照现有更改控制过程控制生产管理组中的管理包版本，可以禁用规则并在允许更新的特定时间段内将其启用。 如果环境中有开发或 QA 管理组，并且该组已连接到 Internet，则通过 Log Analytics 工作区配置该管理组，使之支持此方案。 这样，您可以查看和发布到生产管理组之前先评估其迭代版本的 Azure 监视管理包。

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>将 Operations Manager 组切换到新的 Log Analytics 工作区
1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
1. 在 Azure 门户中，单击左下角的“更多服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”，然后创建一个工作区。  
1. 使用属于 Operations Manager 管理员角色成员的帐户打开 Operations Manager 控制台，并选择“**管理**”工作区。
1. 展开 Log Analytics，然后选择“连接”。
1. 在窗格中间选择“**重新配置 Operation Management Suite**”链接。
1. 按照“Log Analytics 载入向导”操作，输入与新 Log Analytics 工作区关联的管理员帐户的电子邮件地址（或电话号码）和密码。
   
   > [!NOTE]
   > “Operations Management Suite 载入向导:选择工作区”页会显示正在使用的现有工作区。
   > 
   > 

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>验证 Operations Manager 与 Azure Monitor 集成
有多种不同的方式可以验证 Azure 监视器与 Operations Manager 集成成功。

### <a name="to-confirm-integration-from-the-azure-portal"></a>通过 Azure 门户确认集成
1. 在 Azure 门户中，单击左下角的“更多服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。
1. 在 Log Analytics 工作区列表中，选择相应的工作区。  
1. 依次选择“高级设置”、“连接的源”、“System Center”。 
1. 在 System Center Operations Manager 部分下的表中，应该可看到列出管理组的名称，以及代理数量和最后一次收到数据的状态。
   
   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>通过 Operations 控制台确认集成
1. 打开 Operations Manager 控制台并选择“**管理**”工作区。
1. 选择“**管理包**”，并在“**查找:**”文本框中键入 “**Advisor**”或“**Intelligence**”。
1. 相应的管理包会在搜索结果中列出，具体取决于已启用的解决方案。  例如，如果已启用警报管理解决方案，管理包 Microsoft System Center Advisor 警报管理会在表中列出。
1. 从“**监视**”视图导航到“**Operations Management Suite\Health State**”视图。  选择“管理服务器状态”窗格下的一个管理服务器，并在“详细信息视图”窗格中确认“身份验证服务 URI”属性值与 Log Analytics 工作区 ID 匹配。
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>删除与 Azure Monitor 集成
当不再需要 Operations Manager 管理组和 Log Analytics 工作区之间的集成时，正确移除管理组中的连接和配置需要几个步骤。 以下过程包含您通过删除管理组的引用更新你的 Log Analytics 工作区中，删除 Azure Monitor 连接器，然后再删除支持与服务集成的管理包。  

针对解决方案，管理包已启用的与 Operations Manager 集成和支持与 Azure Monitor 的集成所需的管理包不能轻松从管理组删除。 这是因为某些 Azure 监视管理包依赖于其他相关的管理包。 若要删除与其他管理包具有依赖关系的管理包，请从 TechNet 脚本中心下载脚本 [remove a management pack with dependencies](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e)（删除具有依赖关系的管理包）。  

1. 使用属于 Operations Manager 管理员角色成员的帐户打开 Operations Manager 命令外壳。
   
    > [!WARNING]
    > 继续操作之前，确认所有自定义管理包的名称中均没有 Advisor 或 IntelligencePack 字样，否则，以下步骤会将它们从管理组中删除。
    > 

1. 在命令外壳提示下，键入 `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. 接着键入 `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. 若要删除与其他 System Center Advisor 管理包具有依赖关系的剩余管理包，请使用之前从 TechNet 脚本中心下载的脚本  *RecursiveRemove.ps1*。  
 
    > [!NOTE]
    > 使用 PowerShell 删除顾问管理包的步骤不会自动删除 Microsoft System Center Advisor 或 Microsoft System Center Advisor Internal 管理包。  不要尝试将其删除。  
    >  

1. 使用属于 Operations Manager 管理员角色成员的帐户打开 Operations Manager Operations 控制台。
1. 在“**管理**”下面选择“**管理包**”节点，并在“**查找:**”框中键入“**Advisor**”并确认以下管理包仍导入到管理组中：
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. 在 Azure 门户中，单击“设置”磁贴。
1. 选择“**相连的源**”。
1. 在 System Center Operations Manager 部分下的表中，应该可看到想要从工作区移除的管理组的名称。 在“**最后的数据**”列下，单击“**移除**”。  
   
    > [!NOTE]
    > 如果没有从连接的管理组中检测到活动，“移除”链接在 14 天后才可用。  
    > 

1. 将出现一个窗口，要求确认是否继续进行移除。  单击“**是**”继续。 

要删除两个连接器 - Microsoft.SystemCenter.Advisor.DataConnector 和 Advisor Connector，请将以下 PowerShell 脚本保存到计算机，并使用以下示例执行删除：

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SystemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> 运行此脚本的计算机如果不是管理服务器，应根据管理组的版本安装 Operations Manager 命令外壳。
> 
> 

```
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

以后如果打算将管理组重新连接到 Log Analytics 工作区，需重新导入 `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` 管理包文件。 可在以下位置找到此文件，具体取决于部署在环境中的 System Center Operations Manager 的版本：

* System Center 2016 的 `\ManagementPacks` 文件夹下的源媒体 - Operations Manager 及更高版本。
* 适用于管理组的最新更新汇总。 Operations Manager 2012 的源文件夹为` %ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups`，而 2012 R2 的源文件夹则位于 `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` 中。

## <a name="next-steps"></a>后续步骤
若要添加的功能并收集数据，请参阅[从解决方案库中的添加 Azure Monitor 解决方案](../../azure-monitor/insights/solutions.md)。


