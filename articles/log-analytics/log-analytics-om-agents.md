---
title: "将 Operations Manager 连接到 Log Analytics | Microsoft Docs"
description: "要保持 System Center Operations Manager 中的现有投资并将扩展功能用于 Log Analytics，可将 Operations Manager 与 OMS 工作区集成。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: magoedte
ms.openlocfilehash: 387ec757ec17799408ef45bfeb523eb98a5b1013
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="connect-operations-manager-to-log-analytics"></a>将 Operations Manager 连接到 Log Analytics
要保持 System Center Operations Manager 中的现有投资并将扩展功能用于 Log Analytics，可将 Operations Manager 与 OMS 工作区集成。  这会允许利用 OMS 的机会，同时继续使用 Operations Manager 执行以下操作：

* 继续监视 Operations Manager IT 服务的运行状况
* 保持与支持事件和问题管理的 ITSM 解决方案集成
* 管理部署到本地和公有云 IaaS 虚拟机（使用 Operations Manager 监视）的代理的生命周期

通过在收集、存储和分析 Operations Manager 数据时利用 OMS 的速度和效率，与 System Center Operations Manager 集成会提高服务操作策略的价值。  OMS 可以帮助你确定问题的相关因素和根本原因，并重现其发生过程，为现有问题管理过程提供支持。   搜索引擎在检查性能、事件和警报数据方面非常灵活，丰富的仪表板和报告功能可以有意义的方式公开此数据，这均展示了 OMS 为使 Operations Manager 锦上添花所引入的强大优势。

向 Operations Manager 管理组报告的代理基于在 OMS 订阅中启用的 Log Analytics 数据源和解决方案收集来自服务器的数据。  根据已启用的解决方案，这些解决方案的数据可直接从 Operations Manager 管理服务器发送到 OMS Web 服务，或者出于在代理管理系统上收集的数据量，直接从代理发送到 OMS Web 服务。 管理服务器直接将 OMS 数据转发到 OMS Web 服务，数据永远不会写入 OperationsManager 或 OperationsManagerDW 数据库。  当管理服务器与 OMS Web 服务断开连接时，会将数据缓存在本地直到与 OMS 重新建立通信。  如果管理服务器由于计划内维护或计划外停机而处于脱机状态，管理组中的另一台管理服务器将恢复与 OMS 的连接。  

以下图表描绘了 System Center Operations Manager 管理组中的管理服务器及代理与 OMS 之间的连接，包括方向和端口。   

![oms-operations-manager-integration-diagram](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

如果 IT 安全策略不允许网络上的计算机连接到 Internet，可将管理服务器配置为连接到 OMS 网关，以根据启用的解决方案接收配置信息并发送收集的数据。  要详细了解如何将 Operations Manager 管理组配置为通过 OMS 网关与 OMS 服务进行通信，请参阅[使用 OMS 网关将计算机连接到 OMS](log-analytics-oms-gateway.md)。  

## <a name="system-requirements"></a>系统要求
开始之前，请查看以下详细信息来验证是否满足先决条件。

* OMS 仅支持 Operations Manager 2016、Operations Manager 2012 SP1 UR10 及更高版本，以及 Operations Manager 2012 R2 UR11 及更高版本。
* 所有 Operations Manager 代理必须满足最低支持要求。 确保代理达到最低更新，否则 Windows 代理流量会失败，Operations Manager 事件日志可能会出现许多错误。
* 一个 Azure Log Analytics 订阅。  有关进一步的详细信息，请参阅 [Log Analytics入门](log-analytics-get-started.md)。

### <a name="network"></a>网络
下面的信息列出了 Operations Manager 代理、管理服务器和操作控制台与 OMS 进行通信时必需的代理和防火墙配置信息。  来自每个组件的流量将从网络传出到 OMS 服务。     

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
|**Operations Manager 控制台到 OMS**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 和 443||  
|\*.microsoft.com| 80 和 443||  
|\*.microsoftonline.com| 80 和 443||  
|\*.mms.microsoft.com| 80 和 443||  
|login.windows.net| 80 和 443||  


## <a name="connecting-operations-manager-to-oms"></a>将 Operations Manager 连接到 OMS
执行以下一系列步骤来将 Operations Manager 管理组配置为连接到其中一个 OMS 工作区。

1. 在 Operations Manager 控制台中，选择“**管理**”工作区。
2. 展开 Operations Management Suite 节点，并单击“**连接**”。
3. 单击“**向 Operations Management Suite 注册**”链接。
4. 在“**Operations Management Suite 载入向导: 身份验证**”页面上，输入电子邮件地址或电话号码以及与 OMS 订阅关联的管理员帐户的密码，并单击“**登录**”。
5. 成功进行身份验证后，在“Operations Management Suite 载入向导: 选择工作区”页面上，系统会提示选择 OMS 工作区。  如果有多个工作区，从下拉列表中选择想要在 Operations Manager 管理组中注册的工作区，并单击“**下一步**”。
   
   > [!NOTE]
   > Operations Manager 一次仅支持一个 OMS 工作区。 连接以及通过上一个工作区注册到 OMS 的计算机将从 OMS 中删除。
   > 
   > 
6. 在“**Operations Management Suite 载入向导:摘要**”页面上确认设置，如果设置正确无误，请单击 “**创建**”。
7. 在“**Operations Management Suite 载入向导:完成**”页面上，单击“**关闭**”。

### <a name="add-agent-managed-computers"></a>添加代理管理的计算机
配置与 OMS 工作区的集成后，这只是建立了与 OMS 的连接，不会从向管理组报告的代理中收集任何数据。 配置针对 Log Analytics 收集数据的特定代理管理计算机后才会出现这种情况。 可以单独选择计算机对象，或者选择一个包含 Windows 计算机对象的组。 不能选择包含另一个类实例的组，如逻辑磁盘或 SQL 数据库。

1. 打开 Operations Manager 控制台并选择“**管理**”工作区。
2. 展开 Operations Management Suite 节点，并单击“**连接**”。
3. 在窗格右侧的“操作”标题下单击“**添加计算机/组**”链接。
4. 在“计算机搜索”对话框中，可以搜索 Operations Manager 监视的计算机或组。 选择要载入到 OMS 的计算机或组，单击“**添加**”，并单击“**确定**”。

可以在 Operations 控制台“**管理**”工作区中的 Operations Management Suite 下，查看配置为从“受管理计算机”节点收集数据的计算机和组。  在此处，可根据需要添加或移除计算机和组。

### <a name="configure-oms-proxy-settings-in-the-operations-console"></a>在 Operations 控制台中配置 OMS 代理服务器设置
如果内部代理服务器位于管理组和 OMS Web 服务之间，请执行以下步骤。  这些设置可通过管理组集中进行管理并分发到代理管理系统，这些代理管理系统包含在收集 OMS 数据的范围之中。  当某些解决方案绕过管理服务器并将数据直接发送到 OMS Web 服务时，这是很有用的。

1. 打开 Operations Manager 控制台并选择“**管理**”工作区。
2. 展开 Operations Management Suite，并单击“**连接**”。
3. 在“OMS 连接”视图中，单击“**配置代理服务器**”。
4. 在“**Operations Management Suite 向导:代理服务器**”页面上，选择“**使用代理服务器访问 Operations Management Suite**”，键入包含端口号的 URL，例如 http://corpproxy:80 ，并单击“**完成**”。

如果代理服务器要求身份验证，请执行以下步骤，配置需要向管理组中 OMS 报告的受管理计算机传播的凭据和设置。

1. 打开 Operations Manager 控制台并选择“**管理**”工作区。
2. 在“**运行方式配置**”下面，选择“**配置文件**”。
3. 打开 **System Center Advisor Run As Profile Proxy** 配置文件。
4. 在运行方式配置文件向导中，单击“添加”以使用运行方式帐户。 可以创建一个[运行方式帐户](https://technet.microsoft.com/library/hh321655.aspx)或使用现有帐户。 此帐户需要有足够的权限以通过代理服务器。
5. 若要设置管理的帐户，请选择“**选定的类、组或对象**”，单击“**选择...**” 然后单击“**组...**” 打开“**组搜索**”框。
6. 搜索然后选择 **Microsoft System Center Advisor Monitoring Server Group**。  选择组后单击“**确定**”以关闭“**组搜索**”框。
7. 单击“**确定**”以关闭“**添加运行方式帐户**”框。
8. 单击“**保存**”以完成该向导并保存更改。

在创建连接并配置将收集数据并将数据报告给 OMS 的代理后，会在管理组中应用以下配置（不一定按顺序）：

* 运行方式帐户 **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** 随即创建。  它与运行方式配置文件 **Microsoft System Center Advisor Run As Profile Blob** 相关联且目标为两个类 - **收集服务器**和 **Operations Manager 管理组**。
* 两个连接器已创建。  第一个命名为 **Microsoft.SystemCenter.Advisor.DataConnector**，可通过订阅自动配置，该订阅将把从管理组中所有类的实例生成的警报转发到 OMS Log Analytics。 第二个连接器是 **Advisor Connector**，此连接器负责与 OMS Web 服务通信和共享数据。
* 选择的要在管理组中收集数据的代理和组将添加到 **Microsoft System Center Advisor Monitoring Server Group**。

## <a name="management-pack-updates"></a>管理包更新
配置完成后，Operations Manager 管理组会与 OMS 服务建立连接。  管理服务器将与 Web 服务同步，针对与 Operations Manager 集成的已启用解决方案，以管理包的形式接收更新的配置信息。   Operations Manager 会自动检查这些管理包的更新，并在更新可用时下载和导入。  特别是，有两个控制此行为的规则：

* **Microsoft.SystemCenter.Advisor.MPUpdate** - 更新 OMS 基础管理包。 默认情况下，每 12 小时运行一次。
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - 更新在工作区中启用的解决方案管理包。 默认情况下，每五 (5) 分钟运行一次。

可以重写这两个规则：通过禁用规则防止自动下载，或者修改管理服务器与 OMS 同步确定新管理包是否可用且是否应下载的频率。  请按照“[如何重写规则或监视器](https://technet.microsoft.com/library/hh212869.aspx)”的步骤，通过以秒为单位的值修改“**频率**”参数来更改同步计划，或修改“**已启用**”参数禁用规则。  锁定 Operations Manager 管理组类所有对象的替代项。

如果想要继续按照现有更改控制过程控制生产管理组中的管理包版本，可以禁用规则并在允许更新的特定时间段内将其启用。 如果环境中有开发或 QA 管理组，并且已连接到 Internet，可以通过 OMS 工作区配置该管理组以支持此方案。  这样，在将 OMS 管理包发布到生产管理组之前，就可以查看和评估其迭代版本。

## <a name="switch-an-operations-manager-group-to-a-new-oms-workspace"></a>将 Operations Manager 组切换到新 OMS 工作区中
1. 登录到 OMS 订阅并在 [Microsoft Operations Management Suite](http://oms.microsoft.com/) 中创建一个工作区。
2. 使用属于 Operations Manager 管理员角色成员的帐户打开 Operations Manager 控制台，并选择“**管理**”工作区。
3. 展开 Operations Management Suite，并选择“**连接**”。
4. 在窗格中间选择“**重新配置 Operation Management Suite**”链接。
5. 按照“**Operations Management Suite 载入向导**”操作，输入电子邮件地址或电话号码以及与新 OMS 工作区关联的管理员帐户的密码。
   
   > [!NOTE]
   > “Operations Management Suite 载入向导：选择工作区”页面会显示使用中的现有工作区。
   > 
   > 

## <a name="validate-operations-manager-integration-with-oms"></a>验证 Operations Manager 与 OMS 的集成
有多种不同的方式可验证 OMS 与 Operations Manager 的集成是否成功。

### <a name="to-confirm-integration-from-the-oms-portal"></a>通过 OMS 门户确认集成
1. 在 OMS 门户中，单击“设置”磁贴。
2. 选择“**相连的源**”。
3. 在 System Center Operations Manager 部分下的表中，应该可看到列出管理组的名称，以及代理数量和最后一次收到数据的状态。
   
   ![oms-settings-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)
4. 请注意“设置”页面左下方的“**工作区 ID**”值。  根据下面的 Operations Manager 管理组对它进行验证。  

### <a name="to-confirm-integration-from-the-operations-console"></a>通过 Operations 控制台确认集成
1. 打开 Operations Manager 控制台并选择“**管理**”工作区。
2. 选择“**管理包**”，并在“**查找:**”文本框中键入 “**Advisor**”或“**Intelligence**”。
3. 相应的管理包会在搜索结果中列出，具体取决于已启用的解决方案。  例如，如果已启用警报管理解决方案，管理包 Microsoft System Center Advisor 警报管理会在表中列出。
4. 从“**监视**”视图导航到“**Operations Management Suite\Health State**”视图。  选择“**管理服务器状态**”窗格下的一个管理服务器，并在“**详细信息视图**”窗格确认“**身份验证服务 URI**”属性值与 OMS 工作区 ID 匹配。
   
   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-oms"></a>移除与 OMS 的集成
当不再需要 Operations Manager 管理组和 OMS 工作区之间的集成时，正确移除管理组中的连接和配置需要几个步骤。 下面的过程通过删除管理组的引用让用户更新 OMS 工作区、删除 OMS 连接器，再删除支持 OMS 的管理包。   

无法轻松从管理组中删除用于已启用的与 Operations Manager 集成的解决方案的管理包以及支持与 OMS 服务集成所需的管理包。  这是因为某些 OMS 管理包依赖于其他相关管理包。  若要删除与其他管理包具有依赖关系的管理包，请从 TechNet 脚本中心下载脚本 [remove a management pack with dependencies](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e)（删除具有依赖关系的管理包）。  

1. 使用属于 Operations Manager 管理员角色成员的帐户打开 Operations Manager 命令外壳。
   
    > [!WARNING]
    > 继续操作之前，确认所有自定义管理包的名称中均没有 Advisor 或 IntelligencePack 字样，否则，以下步骤会将它们从管理组中删除。
    > 

2. 在命令外壳提示下，键入 `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
3. 接着键入 `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
4. 若要删除与其他 System Center Advisor 管理包具有依赖关系的剩余管理包，请使用之前从 TechNet 脚本中心下载的脚本  *RecursiveRemove.ps1*。  
 
    > [!NOTE]
    > 请勿删除 Microsoft System Center Advisor 或 Microsoft System Center Advisor Internal 管理包。  
    >  

5. 使用属于 Operations Manager 管理员角色成员的帐户打开 Operations Manager Operations 控制台。
6. 在“**管理**”下面选择“**管理包**”节点，并在“**查找:**”框中键入“**Advisor**”并确认以下管理包仍导入到管理组中：
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal
7. 在 OMS 门户中，单击“设置”磁贴。
8. 选择“**相连的源**”。
9. 在 System Center Operations Manager 部分下的表中，应该可看到想要从工作区移除的管理组的名称。  在“**最后的数据**”列下，单击“**移除**”。  
   
    > [!NOTE]
    > 如果没有从连接的管理组中检测到活动，“移除”链接在 14 天后才可用。  
    > 

10. 将出现一个窗口，要求确认是否继续进行移除。  单击“**是**”继续。 

要删除两个连接器 - Microsoft.SystemCenter.Advisor.DataConnector 和 Advisor Connector，请将以下 PowerShell 脚本保存到计算机，并使用以下示例执行删除：

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> 运行此脚本的计算机如果不是管理服务器，应根据管理组的版本安装 Operations Manager 命令外壳。
> 
> 

```
    `param(
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

以后如果打算将管理组重新连接到 OMS 工作区，需要从应用于管理组的最新更新汇总中重新导入 `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` 管理包文件。  可以在 `%ProgramFiles%\Microsoft System Center 2012` 或 `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` 文件夹中找到此文件。

## <a name="next-steps"></a>后续步骤
若要添加功能并收集数据，请参阅[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)。


