---
title: 将 Operations Manager 连接到 Azure Monitor |Microsoft Docs
description: 若要保持 System Center Operations Manager 中的现有投资并将扩展功能用于 Log Analytics，可将 Operations Manager 与工作区集成。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 5dc9412c7884eb62795fd04240f6cfa7d103e3be
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75363653"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>将 Operations Manager 连接到 Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

若要保持对[System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807)的现有投资并使用 Azure Monitor 的扩展功能，可将 Operations Manager 与 Log Analytics 工作区集成。 这样，便可以利用日志 Azure Monitor 的机会，同时继续使用 Operations Manager：

* 使用 Operations Manager 监视 IT 服务的运行状况
* 保持与支持事件和问题管理的 ITSM 解决方案集成
* 管理部署到本地和公有云 IaaS 虚拟机（使用 Operations Manager 监视）的代理的生命周期

与 System Center Operations Manager 集成可通过使用 Azure Monitor 从 Operations Manager 中收集、存储和分析日志数据的速度和效率，增加服务操作策略的价值。 Azure Monitor 日志查询有助于关联和努力确定问题的错误，并将其呈现为支持你现有的问题管理过程。 查询引擎通过丰富的仪表板和报告功能来检查性能、事件和警报数据，以有意义的方式公开此数据，演示了 complimenting Operations Manager 的强度 Azure Monitor。

向 Operations Manager 管理组报告的代理基于工作区中已启用的[Log Analytics 数据源](agent-data-sources.md)和解决方案从服务器收集数据。 根据启用的解决方案，会将其数据从 Operations Manager 管理服务器直接发送到服务，或者因为在代理管理系统上收集的数据量，直接从代理发送到 Log Analytics 工作区。 管理服务器直接将数据转发到服务，数据永远不会写入操作数据库或数据仓库数据库。 当管理服务器失去与 Azure Monitor 的连接时，它会将数据缓存在本地，直到重新建立通信。 如果管理服务器由于计划内维护或计划外停机而处于脱机状态，则管理组中的另一台管理服务器将恢复与 Azure Monitor 的连接。  

下图显示了 System Center Operations Manager 管理组中的管理服务器和代理之间的连接，以及 Azure Monitor （包括方向和端口）。

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

如果 IT 安全策略不允许网络上的计算机连接到 Internet，可将管理服务器配置为连接到 Log Analytics 网关，以根据启用的解决方案接收配置信息并发送收集的数据。 有关如何将 Operations Manager 管理组配置为通过 Log Analytics 网关与 Azure Monitor 通信的详细信息和步骤，请参阅[使用 Log Analytics 网关将计算机连接到 Azure Monitor](../../azure-monitor/platform/gateway.md)。  

## <a name="prerequisites"></a>必备组件

在开始之前，请查看以下要求。

* Azure Monitor 仅支持 System Center Operations Manager 2016 或更高版本，Operations Manager 2012 SP1 UR6 或更高版本，并 Operations Manager 2012 R2 UR2 或更高版本。 Operations Manager 2012 SP1 UR7 和 Operations Manager 2012 R2 UR3 中添加了代理服务器支持。
* 将 System Center Operations Manager 2016 与美国政府云集成需要更新汇总2或更高版本中包含的更新的 Advisor 管理包。 System Center Operations Manager 2012 R2 需要更新汇总3或更高版本附带的更新的 Advisor 管理包。
* 所有 Operations Manager 代理必须满足最低支持要求。 确保代理中安装了最起码的更新，否则 Windows 代理通信可能失败，并在 Operations Manager 事件日志中生成错误。
* Log Analytics 工作区。 有关详细信息，请查看 [Log Analytics 工作区概述](design-logs-deployment.md)。 
* 使用 [Log Analytics 参与者角色](manage-access.md#manage-access-using-azure-permissions)成员帐户在 Azure 中进行身份验证。

* 支持的区域-System Center Operations Manager 连接到 Log Analytics 工作区，则仅支持以下 Azure 区域：
    - 美国中西部
    - 澳大利亚东南部
    - 西欧
    - 美国东部
    - 东南亚
    - 日本东部
    - 英国南部
    - 印度中部
    - 加拿大中部
    - 美国西部 2

>[!NOTE]
>Azure Api 的最新更改将阻止客户首次成功配置其管理组与 Azure Monitor 的集成。 对于已将其管理组与该服务进行集成的客户，除非需要重新配置现有连接，否则他们不受影响。  
>已为以下版本的 Operations Manager 发布了新的管理包：
> - 对于 System Center Operations Manager 2019，此管理包包含在源媒体中并在安装新管理组或升级过程中安装。
>- Operations Manager 1801 管理包还适用于 Operations Manager 1807。
>- 对于 System Center Operations Manager 1801，请从[此处](https://www.microsoft.com/download/details.aspx?id=57173)下载管理包。
>- Operations Manager，请从[此处](https://www.microsoft.com/download/details.aspx?id=57172)下载管理包。2016  
>- 对于 System Center Operations Manager 2012 R2，请从[此处](https://www.microsoft.com/download/details.aspx?id=57171)下载管理包。  


### <a name="network"></a>网络

下面的信息列出了 Operations Manager 代理、管理服务器和操作控制台与 Azure Monitor 通信所需的代理和防火墙配置信息。 来自每个组件的流量从你的网络出站到 Azure Monitor。

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
|\* .azure-automation.net | 443| 是|  
|**将控制台 Operations Manager 到 Azure Monitor**|||  
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

为了确保传输中数据的安全性 Azure Monitor，我们强烈建议将代理和管理组配置为至少使用传输层安全性（TLS）1.2。 我们发现旧版 TLS/安全套接字层 (SSL) 容易受到攻击，尽管目前出于向后兼容，这些协议仍可正常工作，但我们**不建议使用**。 有关其他信息，请查看[使用 TLS 1.2 安全地发送数据](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)。

## <a name="connecting-operations-manager-to-azure-monitor"></a>将 Operations Manager 连接到 Azure Monitor

执行以下一系列步骤，将 Operations Manager 管理组配置为连接到你的一个 Log Analytics 工作区。

首次向 Log Analytics 工作区注册 Operations Manager 管理组期间，为管理组指定代理配置的选项在操作控制台中不可用。  必须成功向服务注册管理组后，此选项才可用。  若要解决此问题，需使用 Netsh，对运行操作控制台以配置集成的系统，以及管理组中的所有管理服务器进行系统代理配置的更新。  

1. 打开提升的命令指示符。
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 转到“启动”，然后键入“cmd”。
   b.保留“数据库类型”设置，即设置为“共享”。 右键单击“命令提示符”然后选择“以管理员身份运行”**。
1. 键入以下命令并按 Enter：

    `netsh winhttp set proxy <proxy>:<port>`

完成以下步骤以与 Azure Monitor 集成后，可以通过运行 `netsh winhttp reset proxy` 删除配置，然后使用操作控制台中的 "**配置代理服务器**" 选项来指定代理或 Log Analytics 网关服务器。

1. 在 Operations Manager 控制台中，选择“**管理**”工作区。
1. 展开 Operations Management Suite 节点，并单击“**连接**”。
1. 单击“**向 Operations Management Suite 注册**”链接。
1. 在“**Operations Management Suite 载入向导: 身份验证**”页面上，输入电子邮件地址或电话号码以及与 OMS 订阅关联的管理员帐户的密码，并单击“**登录**”。

   >[!NOTE]
   >Operations Management Suite 名称已停用。

1. 成功进行身份验证后，在“Operations Management Suite 载入向导: 选择工作区”页面上，系统会提示选择 Azure 租户、订阅和 Log Analytics 工作区。 如果有多个工作区，从下拉列表中选择想要在 Operations Manager 管理组中注册的工作区，并单击“**下一步**”。

   > [!NOTE]
   > Operations Manager 一次仅支持一个 Log Analytics 工作区。 将从 Azure Monitor 中删除与前一个工作区 Azure Monitor 注册的连接和计算机。
   >
   >
1. 在“**Operations Management Suite 载入向导:摘要**”页面上确认设置，如果设置正确无误，请单击 “**创建**”。
1. 在“**Operations Management Suite 载入向导:完成**”页面上，单击“**关闭**”。

### <a name="add-agent-managed-computers"></a>添加代理管理的计算机

配置与 Log Analytics 工作区的集成后，只是建立了与服务的连接，不会从向管理组报告的代理中收集任何数据。 在配置了哪些特定代理管理的计算机为 Azure Monitor 收集日志数据之前，将不会发生这种情况。 可以单独选择计算机对象，或者选择一个包含 Windows 计算机对象的组。 不能选择包含另一个类实例的组，如逻辑磁盘或 SQL 数据库。

1. 打开 Operations Manager 控制台并选择“**管理**”工作区。
1. 展开 Operations Management Suite 节点，并单击“**连接**”。
1. 在窗格右侧的“操作”标题下单击“**添加计算机/组**”链接。
1. 在“计算机搜索”对话框中，可以搜索 Operations Manager 监视的计算机或组。 选择包括 Operations Manager 管理服务器的计算机或组以加载到 Azure Monitor 中，单击 "**添加**"，然后单击 **"确定"** 。

可以在 Operations 控制台“**管理**”工作区中的 Operations Management Suite 下，查看配置为从“受管理计算机”节点收集数据的计算机和组。 在此处，可根据需要添加或移除计算机和组。

### <a name="configure-proxy-settings-in-the-operations-console"></a>在操作控制台中配置代理设置

如果内部代理服务器位于管理组与 Azure Monitor 之间，请执行以下步骤。 这些设置是从管理组集中管理的，并分发给代理管理系统，这些系统包含在为 Azure Monitor 收集日志数据的作用域中。  当某些解决方案绕过管理服务器并将数据直接发送到服务时，这很有用。

1. 打开 Operations Manager 控制台并选择“**管理**”工作区。
1. 展开 Operations Management Suite，并单击“**连接**”。
1. 在“OMS 连接”视图中，单击“**配置代理服务器**”。
1. 在“Operations Management Suite 向导: 代理服务器”页上，选择“使用代理服务器访问 Operations Management Suite”，键入包含端口号的 URL，例如 http://corpproxy:80 ，并单击“完成”。

如果你的代理服务器要求身份验证，请执行以下步骤来配置需要传播到管理组中的 Azure Monitor 的被管理计算机的凭据和设置。

1. 打开 Operations Manager 控制台并选择“**管理**”工作区。
1. 在“**运行方式配置**”下面，选择“**配置文件**”。
1. 打开 **System Center Advisor Run As Profile Proxy** 配置文件。
1. 在运行方式配置文件向导中，单击“添加”以使用运行方式帐户。 可以创建一个[运行方式帐户](https://technet.microsoft.com/library/hh321655.aspx)或使用现有帐户。 此帐户需要有足够的权限以通过代理服务器。
1. 若要设置管理的帐户，请选择“**选定的类、组或对象**”，单击“**选择...** ” 然后单击“**组...** ” 打开“**组搜索**”框。
1. 搜索然后选择 **Microsoft System Center Advisor Monitoring Server Group**。 选择组后单击“**确定**”以关闭“**组搜索**”框。
1. 单击“**确定**”以关闭“**添加运行方式帐户**”框。
1. 单击“**保存**”以完成该向导并保存更改。

创建连接并配置将 Azure Monitor 收集和报告日志数据的代理后，将在管理组中应用以下配置，而不是按顺序应用：

* 运行方式帐户 **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** 随即创建。 它与运行方式配置文件 **Microsoft System Center Advisor Run As Profile Blob** 相关联且目标为两个类 - **收集服务器**和 **Operations Manager 管理组**。
* 两个连接器已创建。  第一个名称为**SystemCenter** ，它使用订阅自动配置，该订阅将从管理组中所有类的实例中生成的所有警报转发到 Azure Monitor。 第二个连接器是**Advisor 连接器**，负责与 Azure Monitor 通信和共享数据。
* 选择的要在管理组中收集数据的代理和组将添加到 **Microsoft System Center Advisor Monitoring Server Group**。

## <a name="management-pack-updates"></a>管理包更新

完成配置后，Operations Manager 管理组将建立与 Azure Monitor 的连接。 管理服务器将与 Web 服务同步，针对与 Operations Manager 集成的已启用解决方案，以管理包的形式接收更新的配置信息。 Operations Manager 会自动检查这些管理包的更新，并在更新可用时下载和导入。 特别是，有两个控制此行为的规则：

* **SystemCenter** -更新基本 Azure Monitor 管理包。 默认情况下，每 12 小时运行一次。
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - 更新在工作区中启用的解决方案管理包。 默认情况下，每五 (5) 分钟运行一次。

可以重写这两个规则，以防止自动下载，方法是禁用自动下载，或修改管理服务器与 Azure Monitor 进行同步的频率，以确定新的管理包是否可用且应下载。 请按照“[如何重写规则或监视器](https://technet.microsoft.com/library/hh212869.aspx)”的步骤，通过以秒为单位的值修改“**频率**”参数来更改同步计划，或修改“**已启用**”参数禁用规则。 锁定 Operations Manager 管理组类所有对象的替代项。

若要继续按照现有更改控制过程控制生产管理组中的管理包版本，可以禁用规则并在允许更新的特定时间段内将其启用。 如果环境中有开发或 QA 管理组，并且该组已连接到 Internet，则通过 Log Analytics 工作区配置该管理组，使之支持此方案。 这样，便可以在将 Azure Monitor 管理包发布到生产管理组之前，查看和评估这些包的迭代版本。

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>将 Operations Manager 组切换到新的 Log Analytics 工作区

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
1. 在 Azure 门户中，单击左下角的“更多服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”，然后创建一个工作区。  
1. 使用属于 Operations Manager 管理员角色成员的帐户打开 Operations Manager 控制台，并选择“**管理**”工作区。
1. 展开 Log Analytics，然后选择“连接”。
1. 在窗格中间选择“**重新配置 Operation Management Suite**”链接。
1. 按照“Log Analytics 载入向导”操作，输入与新 Log Analytics 工作区关联的管理员帐户的电子邮件地址（或电话号码）和密码。

   > [!NOTE]
   > “Operations Management Suite 载入向导：选择工作区”页面会显示使用中的现有工作区。
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>验证与 Azure Monitor Operations Manager 集成

可以通过几种不同的方法来验证 Azure Monitor Operations Manager 集成是否成功。

### <a name="to-confirm-integration-from-the-azure-portal"></a>通过 Azure 门户确认集成

1. 在 Azure 门户中，单击左下角的“更多服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。
1. 在 Log Analytics 工作区列表中，选择相应的工作区。  
1. 依次选择“高级设置”、“连接的源”、“System Center”。
1. 在 System Center Operations Manager 部分下的表中，应该可看到列出管理组的名称，以及代理数量和最后一次收到数据的状态。

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>通过 Operations 控制台确认集成

1. 打开 Operations Manager 控制台并选择“**管理**”工作区。
1. 选择“**管理包**”，并在“**查找:** ”文本框中键入 “**Advisor**”或“**Intelligence**”。
1. 相应的管理包会在搜索结果中列出，具体取决于已启用的解决方案。  例如，如果已启用警报管理解决方案，管理包 Microsoft System Center Advisor 警报管理会在表中列出。
1. 从“**监视**”视图导航到“**Operations Management Suite\Health State**”视图。  选择“管理服务器状态”窗格下的一个管理服务器，并在“详细信息视图”窗格中确认“身份验证服务 URI”属性值与 Log Analytics 工作区 ID 匹配。

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>删除与 Azure Monitor 的集成

当不再需要 Operations Manager 管理组和 Log Analytics 工作区之间的集成时，正确移除管理组中的连接和配置需要几个步骤。 以下过程通过删除管理组的引用来更新 Log Analytics 工作区，删除 Azure Monitor 连接器，然后删除支持与服务集成的管理包。  

已启用的解决方案的管理包，这些管理包与 Operations Manager 和支持与 Azure Monitor 集成所需的管理包无法轻松地从管理组中删除。 这是因为某些 Azure Monitor 管理包依赖于其他相关管理包。 若要删除与其他管理包具有依赖关系的管理包，请从 TechNet 脚本中心下载脚本 [remove a management pack with dependencies](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e)（删除具有依赖关系的管理包）。  

1. 使用属于 Operations Manager 管理员角色成员的帐户打开 Operations Manager 命令外壳。

    > [!WARNING]
    > 继续操作之前，确认所有自定义管理包的名称中均没有 Advisor 或 IntelligencePack 字样，否则，以下步骤会将它们从管理组中删除。
    >

1. 在命令外壳提示下，键入 `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. 接着键入 `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. 若要删除与其他 System Center Advisor 管理包具有依赖关系的剩余管理包，请使用之前从 TechNet 脚本中心下载的脚本  *RecursiveRemove.ps1*。  

    > [!NOTE]
    > 使用 PowerShell 删除顾问管理包的步骤不会自动删除 Microsoft System Center Advisor 或 Microsoft System Center Advisor Internal 管理包。  不要尝试将其删除。  
    >  

1. 使用属于 Operations Manager 管理员角色成员的帐户打开 Operations Manager Operations 控制台。
1. 在“**管理**”下面选择“**管理包**”节点，并在“**查找:** ”框中键入“**Advisor**”并确认以下管理包仍导入到管理组中：

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. 在 Azure 门户中，单击“设置”磁贴。
1. 选择“**相连的源**”。
1. 在 System Center Operations Manager 部分下的表中，应该可看到想要从工作区移除的管理组的名称。 在“**最后的数据**”列下，单击“**移除**”。  

    > [!NOTE]
    > 如果没有从连接的管理组中检测到活动，“移除”链接在 14 天后才可用。  
    >

1. 将出现一个窗口，要求确认是否继续进行移除。  单击“是” 继续。

要删除两个连接器 - Microsoft.SystemCenter.Advisor.DataConnector 和 Advisor Connector，请将以下 PowerShell 脚本保存到计算机，并使用以下示例执行删除：

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> 运行此脚本的计算机如果不是管理服务器，应根据管理组的版本安装 Operations Manager 命令外壳。
>
>

```powershell
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
* 适用于管理组的最新更新汇总。 对于 Operations Manager 2012，将 `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` 源文件夹; 对于 2012 R2，此文件夹位于 `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`中。

## <a name="next-steps"></a>后续步骤

若要添加功能并收集数据，请参阅[从解决方案库添加 Azure Monitor 解决方案](../../azure-monitor/insights/solutions.md)。
