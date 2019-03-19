---
title: 使用 Log Analytics 网关连接计算机 |Microsoft Docs
description: 使用 Log Analytics 网关将数据发送到 Azure 自动化和 Log Analytics 服务，它们没有 internet 访问权限时连接你的设备和 Operations Manager 监视的计算机。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: magoedte
ms.openlocfilehash: a497662ac7a885b53e69bb8c86a646045bd2eef7
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314664"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway"></a>通过使用 Log Analytics 网关无法访问 internet 的计算机连接

>[!NOTE]
>Microsoft Operations Management Suite (OMS) 过渡到 Microsoft Azure 监视器，因为术语发生了变化。 此篇文章介绍了 OMS 网关与 Azure Log Analytics 网关。 
>

本文介绍如何使用 Log Analytics 网关直接连接或 Operations Manager 监视的计算机没有 internet 访问时配置与 Azure 自动化和 Log Analytics 的通信。 

Log Analytics 网关是使用 HTTP CONNECT 命令支持 HTTP 隧道的 HTTP 转发代理。 此网关可以收集数据并将其发送到 Azure 自动化和 Log Analytics 代表未连接到 internet 的计算机。  

Log Analytics 网关支持：

* 报告到相同的四个 Log Analytics 工作区代理，位于它并使用 Azure 自动化混合 Runbook 辅助角色进行配置的。  
* Windows 计算机的 Microsoft Monitoring Agent 直接连接到 Log Analytics 工作区。
* 在其适用于 Linux 的 Log Analytics 代理直接连接到 Log Analytics 工作区的 Linux 计算机。  
* System Center Operations Manager 2012 SP1 UR7、 Operations Manager 2012 R2 ur3 或 Operations Manager 2016 或更高版本的管理组与 Log Analytics 集成。  

有些 IT 安全策略不允许网络计算机的 internet 连接。 这些未连接的计算机可能是销售点 (POS) 设备或支持 IT 服务，例如服务器的点。 若要将这些设备连接到 Azure 自动化或 Log Analytics 以便管理和监视它们，将其配置为直接与 Log Analytics 网关通信。 Log Analytics 网关可以接收配置信息和代表他们转发数据。 如果计算机上配置了可直接连接到 Log Analytics 工作区的 Log Analytics 代理，计算机改为与 Log Analytics 网关通信。  

Log Analytics 网关直接将数据传输从代理到服务。 它不会分析传输中的数据。

当与 Log Analytics 集成的 Operations Manager 管理组时，可以配置管理服务器为连接到 Log Analytics 网关，以接收配置信息并发送收集的数据，具体取决于已启用的解决方案.  Operations Manager 代理向管理服务器发送某些数据。 例如，代理可能会发送 Operations Manager 警报、 配置评估数据、 实例空间数据和容量数据。 其他大批量的数据，如 Internet 信息服务 (IIS) 日志、 性能数据和安全事件是直接发送到 Log Analytics 网关。 

如果部署了一个或多个 Operations Manager 网关服务器用于监视在外围网络或隔离的网络中的不受信任的系统，这些服务器无法与 Log Analytics 网关通信。  Operations Manager 网关服务器可以只向管理服务器报告。  如果将 Operations Manager 管理组配置为与 Log Analytics 网关通信，代理配置信息会自动分发到代理管理的、配置为收集 Log Analytics 数据的每台计算机，即使设置为空。    

若要直接提供的高可用性连接或通过网关与 Log Analytics 通信的 Operations Management 组使用网络负载平衡 (NLB) 重定向和分配流量跨多个网关服务器。 这样一来，如果一个网关服务器出现故障，流量将重定向到另一个可用节点。  

运行 Log Analytics 网关的计算机需要 Log Analytics Windows 代理，以标识该网关需要与进行通信的服务终结点。 此外需要直接向同一个工作区报告的网关代理的代理或 Operations Manager 管理组在网关后面配置了。 此配置允许网关和代理后，若要向其分配工作区进行通信。

网关可以是多宿主到最多四个工作区。 这是 Windows 代理支持的工作区的总数。  

每个代理必须具有网络连接到网关，以便代理才能自动传输到和从网关的数据。 避免在域控制器上安装网关。

下图显示了从直接代理，通过网关到 Azure 自动化和 Log Analytics 的数据流。 代理的代理配置必须与 Log Analytics 网关配置的端口匹配。  

![直接代理与服务通信的示意图](./media/gateway/oms-omsgateway-agentdirectconnect.png)

下图显示了数据如何从 Operations Manager 管理组流向 Log Analytics。   

![使用 Log Analytics 的 Operations Manager 通信示意图](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>设置系统

指定要在其中运行 Log Analytics 网关的计算机必须具有以下配置：

* Windows 10、 Windows 8.1 或 Windows 7
* Windows Server 2016、 Windows Server 2012 R2、 Windows Server 2012、 Windows Server 2008 R2 或 Windows Server 2008
* Microsoft .NET Framework 4.5
* 至少 4 核处理器，8 GB 的内存 
* 一个[适用于 Windows 的 Log Analytics 代理](agent-windows.md)，配置为向通过网关进行通信的代理作为同一工作区报告

### <a name="language-availability"></a>语言可用性

Log Analytics 网关是提供了以下语言：

- 中文(简体)
- 中文(繁体)
- 捷克语
- 荷兰语
- 英语
- 法语
- 德语
- 匈牙利语
- 意大利语
- 日语
- 韩语
- 波兰语
- 葡萄牙语(巴西)
- 葡萄牙语(葡萄牙)
- 俄语
- 西班牙语(国际)

### <a name="supported-encryption-protocols"></a>支持的加密协议
Log Analytics 网关支持仅传输层安全 (TLS) 1.0、 1.1 和 1.2。  它不支持安全套接字层 (SSL)。  若要确保传输到 Log Analytics 中的数据的安全性，网关配置为至少使用 TLS 1.2。 较旧版本的 TLS 或 SSL 是易受攻击。 尽管它们目前允许向后兼容性，但避免使用它们。  

有关其他信息，请查看[使用 TLS 1.2 安全地发送数据](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)。 

### <a name="supported-number-of-agent-connections"></a>支持的代理连接数
下表显示大约多少代理可以与网关服务器通信。 支持基于代理每 6 秒上传大约 200 KB 的数据。 对于每个测试代理，数据量是大约每天 2.7 GB。

|网关 |受支持的代理 （近似）|  
|--------|----------------------------------|  
|CPU：Intel Xeon 处理器 E5 2660 v3 \@ 2.6ghz 2 Cores<br> 内存: 4 GB<br> 网络带宽：1 Gbps| 600|  
|CPU：Intel Xeon 处理器 E5 2660 v3 \@ 2.6ghz 4 Cores<br> 内存: 8 GB<br> 网络带宽：1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>下载 Log Analytics 网关

获取从 Log Analytics 网关安装程序文件的最新版本[Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443)或 Azure 门户。

若要从 Azure 门户中获取 Log Analytics 网关，请执行以下步骤：

1. 浏览服务列表，并选择“Log Analytics”。 
1. 选择工作区。
1. 在工作区边栏选项卡中的“常规”下面，选择“快速启动”。 
1. 在“选择要连接到工作区的数据源”下面，选择“计算机”。
1. 在中**直接代理**边栏选项卡，选择**下载 Log Analytics 网关**。
 
   ![下载 Log Analytics 网关的步骤的屏幕截图](./media/gateway/download-gateway.png)

或 

1. 在工作区边栏选项卡中的“设置”下方，选择“高级设置”。
1. 转到**连接的源** > **Windows 服务器**，然后选择**下载 Log Analytics 网关**。

## <a name="install-the-log-analytics-gateway"></a>安装 Log Analytics 网关

若要安装网关，请执行以下步骤。  （如果安装了一个名为 Log Analytics 转发器的上一版本，它将升级到此版本。）

1. 在目标文件夹中，双击“Log Analytics gateway.msi”。
1. 在“欢迎”页上，选择“下一步”。

   ![在网关安装向导的屏幕截图的欢迎页](./media/gateway/gateway-wizard01.png)

1. 上**许可协议**页上，选择**我接受许可协议中的条款**以同意 Microsoft 软件许可条款，然后选择**下一步**。
1. 在“端口和代理地址”页上执行以下操作：

   a. 输入要用于网关的 TCP 端口号。 安装程序使用此端口号上 Windows 防火墙配置入站的规则。  默认值为 8080。
      端口号的有效范围是 1 到 65535 之间。 如果输入的端口号不在此范围内，会出现一条错误消息。

   b. 如果安装网关的服务器需要通过代理通信，请输入代理地址需要将网关连接。 例如，输入 `http://myorgname.corp.contoso.com:80`。  如果将此字段留空，网关将尝试直接连接到 internet。  如果代理服务器要求身份验证，请输入用户名和密码。

   c. 选择“**下一步**”。

   ![网关代理的配置的屏幕截图](./media/gateway/gateway-wizard02.png)

1. 如果还没有启用 Microsoft 更新、 Microsoft Update 页出现后，并可以选择启用它。 做出选择，然后选择**下一步**。 否则，继续执行下一步。
1. 上**目标文件夹**页上，可以保留默认文件夹 C:\Program Files\OMS Gateway，或输入你想要安装网关的位置。 然后，选择“下一步”。
1. 在“准备安装”页上，选择“安装”。 如果用户帐户控制请求安装的权限，则选择**是**。
1. 安装程序完成后，选择**完成**。 若要验证该服务是否正在运行，打开 services.msc 管理单元中，并验证**OMS 网关**在服务列表中显示其状态是否**运行**。

   ![显示运行 OMS 网关的本地服务的屏幕截图](./media/gateway/gateway-service.png)


## <a name="configure-network-load-balancing"></a>配置网络负载均衡 
你可以配置为使用网络负载平衡 (NLB) 使用 Microsoft 的高可用性网关[网络负载平衡 (NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)， [Azure 负载均衡器](../../load-balancer/load-balancer-overview.md)，或基于硬件的负载均衡器。 负载均衡器通过在其节点之间重定向 Log Analytics 代理或 Operations Manager 管理服务器请求的连接来管理流量。 如果一台网关服务器出现故障，流量将重定向到其他节点。

### <a name="microsoft-network-load-balancing"></a>Microsoft 网络负载平衡
若要了解如何设计和部署 Windows Server 2016 网络负载均衡群集，请参阅[网络负载均衡](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)。 以下步骤介绍如何配置 Microsoft 网络负载均衡群集。  

1. 使用管理帐户登录到属于 NLB 群集的 Windows 服务器。  
2. 在服务器管理器中打开网络负载均衡管理器，单击“工具”，并单击“网络负载均衡管理器”。
3. 要连接装有 Microsoft Monitoring Agent 的 Log Analytics 网关服务器，请右键单击群集的 IP 地址，然后单击“将主机添加到群集”。 

    ![网络负载平衡管理器 – 将主机添加到群集](./media/gateway/nlb02.png)
 
4. 输入要连接的网关服务器的 IP 地址。 

    ![网络负载均衡管理器 – 将主机添加到群集：连接](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure 负载均衡器
若要了解如何设计和部署 Azure 负载均衡器，请参阅[什么是 Azure 负载均衡器？](../../load-balancer/load-balancer-overview.md)。 若要部署的基本负载均衡器，请在此所述的步骤[快速入门](../../load-balancer/quickstart-create-basic-load-balancer-portal.md)排除部分中所述的步骤**创建后端服务器**。   

> [!NOTE]
> 配置 Azure 负载均衡器使用**基本 SKU**，要求 Azure 虚拟机属于可用性集。 若要了解有关可用性集的详细信息，请参阅[管理 Azure 中 Windows 虚拟机的可用性](../../virtual-machines/windows/manage-availability.md)。 若要将现有的虚拟机添加到可用性集，请参阅[设置 Azure 资源管理器 VM 可用性集](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)。
> 

创建负载均衡器后后, 端池需要创建，这样可以将分发到一个或多个网关服务器的流量。 按照快速入门文章部分中所述的步骤[创建负载均衡器资源](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer)。  

>[!NOTE]
>配置运行状况探测时它应配置为使用网关服务器的 TCP 端口。 运行状况探测动态添加或从基于其对运行状况检查的响应负载均衡器轮转中删除网关服务器。 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>配置 Log Analytics 代理和 Operations Manager 管理组
在本部分中，将看到如何使用与 Azure 自动化或 Log Analytics 进行通信的 Log Analytics 网关配置直接连接的 Log Analytics 代理、 Operations Manager 管理组或 Azure 自动化混合 Runbook 辅助角色。  

### <a name="configure-a-standalone-log-analytics-agent"></a>配置独立的 Log Analytics 代理
在配置 Log Analytics 代理时，替换的 Log Analytics 网关服务器和其端口号的 IP 地址的代理服务器值。 如果已部署负载均衡器后面的多个网关服务器，Log Analytics 代理的代理配置为负载均衡器的虚拟 IP 地址。  

>[!NOTE]
>若要在网关和直接连接到 Log Analytics 的 Windows 计算机上安装 Log Analytics 代理，请参阅[连接 Windows 计算机连接到 Azure 中的 Log Analytics 服务](agent-windows.md)。 若要将 Linux 计算机的连接，请参阅[混合环境中配置 Linux 计算机的 Log Analytics 代理](../../azure-monitor/learn/quick-collect-linux-computer.md)。 
>

网关服务器上安装代理后，将其配置为向工作区或与网关通信的工作区代理报告。 如果 Log Analytics Windows 代理未安装在网关上，事件 300 写入 OMS 网关事件日志中，指示需要安装代理。 如果代理已安装但未配置为向通过它进行通信的代理作为同一工作区报告，事件 105 写入同一日志，该值指示在网关代理需要配置为向同一工作区中与代理报告该 communicate 与网关。

完成配置后，重新启动 OMS 网关服务才能应用所做的更改。 否则，网关将拒绝代理尝试与 Log Analytics 进行通信，并将报告事件 105 OMS 网关事件日志中。 添加或删除工作区从网关服务器上的代理配置时，也会发生这种情况。   

与自动化混合 Runbook 辅助角色相关的信息，请参阅[使用混合 Runbook 辅助角色自动在数据中心或云资源](../../automation/automation-hybrid-runbook-worker.md)。

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>配置 Operations Manager 中，其中所有代理都使用相同的代理服务器
即使设置为空，Operations Manager 代理配置也会自动应用到向 Operations Manager 报告的所有代理。  

若要使用 OMS 网关来支持 Operations Manager，必须具有：

* Microsoft Monitoring Agent (版本 8.0.10900.0 或更高版本) 的 OMS 网关服务器上安装并使用同一个 Log Analytics 工作区，您的管理组配置为向报告配置。
* Internet 连接。 或者，OMS 网关必须连接到连接到 internet 的代理服务器。

> [!NOTE]
> 如果指定没有网关的值，空值推送到所有代理。
>

如果将 Operations Manager 管理组与 Log Analytics 工作区注册第一次，不会看到操作控制台中指定管理组的代理配置的选项。 此选项才可用，仅当尚未向服务注册管理组。  

若要配置集成，请使用 Netsh，系统在运行操作控制台和管理组中的所有管理服务器上更新系统代理配置。 执行以下步骤:

1. 打开提升的命令提示符：

   a. 选择**启动**并输入**cmd**。  

   b. 右键单击**命令提示符**，然后选择**以管理员身份运行**。  

1. 输入以下命令：

   `netsh winhttp set proxy <proxy>:<port>`

使用 Log Analytics 集成后，通过运行删除更改`netsh winhttp reset proxy`。 然后，在操作控制台中，使用**配置代理服务器**选项来指定 Log Analytics 网关服务器。 

1. 在 Operations Manager 控制台中下, **Operations Management Suite**，选择**连接**，然后选择**配置代理服务器**。

   ![屏幕截图的 Operations Manager 中，显示所选内容配置代理服务器](./media/gateway/scom01.png)

1. 选择**使用代理服务器访问 Operations Management Suite** ，然后输入 Log Analytics 网关服务器的 IP 地址或负载均衡器虚拟 IP 地址。 请注意，若要以前缀开头`http://`。

   ![屏幕截图的 Operations Manager 中，显示代理服务器地址](./media/gateway/scom02.png)

1. 选择“完成”。 Operations Manager 管理组现已配置为通过网关服务器与 Log Analytics 服务通信。

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>配置 Operations Manager 中，其中特定的代理使用代理服务器
对于大型或复杂环境，您可能希望只有特定的服务器 （或组） 使用 Log Analytics 网关服务器。  对于这些服务器，你无法更新 Operations Manager 代理直接因为管理组的全局值覆盖此值。  相反，重写用于推送这些值的规则。  

> [!NOTE] 
> 如果你想要允许你的环境中的多个 Log Analytics 网关服务器，请使用此配置方法。  例如，可以要求特定的 Log Analytics 网关服务器，用于指定区域的基础上。
>

若要配置特定服务器或组，以使用 Log Analytics 网关服务器： 

1. 打开 Operations Manager 控制台并选择“创作”工作区。  
1. 在“创作”工作区中选择“规则”。 
1. 在 Operations Manager 工具栏上，选择**作用域**按钮。 如果此按钮不可用，请确保您选择的对象，而不是文件夹中**监视**窗格。 “范围管理包对象”对话框显示了通用目标类、组或对象的列表。 
1. 在中**寻找**字段中，输入**运行状况服务**并从列表中选择它。 选择“确定”。  
1. 搜索**顾问代理设置规则**。 
1. 在 Operations Manager 工具栏上，选择**重写**，然后指向**重写规则 \ 针对类的特定对象：运行状况服务**并从列表中选择一个对象。  或创建包含你想要应用到此重写的服务器的运行状况服务对象的自定义组。 将重写应用到你的自定义组。
1. 在中**重写属性**对话框框中，添加中的复选标记**重写**旁边的列**WebProxyAddress**参数。  在中**替代值**字段中，输入 Log Analytics 网关服务器的 URL。 请注意，若要以前缀开头`http://`。  

    >[!NOTE]
    > 不需要启用该规则。 它具有的自动管理 Microsoft System Center 顾问安全引用重写管理包中重写了面向 Microsoft System Center 顾问监视服务器组。
    > 

1. 选择管理包从**选择目标管理包**列表，或通过选择创建新的未密封的管理包**新建**。 
1. 完成后，选择“确定”。 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>为自动化混合 Runbook 辅助角色配置
如果必须在环境中自动化混合 Runbook 辅助角色，请按照这些步骤的手动的临时解决方法，若要配置 OMS 网关，以辅助角色提供支持。

若要按照本部分中的步骤，需要知道自动化帐户所在的 Azure 区域。 若要查找该位置：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 选择 Azure 自动化服务。
1. 选择相应的 Azure 自动化帐户。
1. 在“位置”下面即可查看该帐户所在的区域。

   ![在 Azure 门户中的自动化帐户位置的屏幕截图](./media/gateway/location.png)

使用下表来标识每个位置的 URL。

**作业运行时数据服务 Url**

| **位置** | **URL** |
| --- | --- |
| 美国中北部 |ncus-jobruntimedata-prod-su1.azure-automation.net |
| 西欧 |we-jobruntimedata-prod-su1.azure-automation.net |
| 美国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 美国东部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 北欧 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 东南亚 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 日本 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 澳大利亚 |ase-jobruntimedata-prod-su1.azure-automation.net |

**代理服务 URL**

| **位置** | **URL** |
| --- | --- |
| 美国中北部 |ncus-agentservice-prod-1.azure-automation.net |
| 西欧 |we-agentservice-prod-1.azure-automation.net |
| 美国中南部 |scus-agentservice-prod-1.azure-automation.net |
| 美国东部 2 |eus2-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-agentservice-prod-1.azure-automation.net |
| 北欧 |ne-agentservice-prod-1.azure-automation.net |
| 东南亚 |sea-agentservice-prod-1.azure-automation.net |
| 印度中部 |cid-agentservice-prod-1.azure-automation.net |
| 日本 |jpe-agentservice-prod-1.azure-automation.net |
| 澳大利亚 |ase-agentservice-prod-1.azure-automation.net |

如果您的计算机已自动注册为混合 Runbook 辅助角色，使用更新管理解决方案来管理修补程序。 执行以下步骤:

1. 将作业运行时数据服务 URL 添加到 Log Analytics 网关上的“允许的主机”列表。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. 使用以下 PowerShell cmdlet 重新启动 Log Analytics 网关服务：`Restart-Service OMSGatewayService`

如果您的计算机已加入到 Azure 自动化使用混合 Runbook 辅助角色注册 cmdlet，请按照下列步骤：

1. 将代理服务注册 URL 添加到 Log Analytics 网关上的“允许的主机”列表。 例如： `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. 将作业运行时数据服务 URL 添加到 Log Analytics 网关上的“允许的主机”列表。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. 重新启动 Log Analytics 网关服务。
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>有用的 PowerShell cmdlet
Cmdlet 可用于完成的任务来更新 Log Analytics 网关的配置设置。 使用 cmdlet 之前，请确保：

1. 安装 Log Analytics 网关 （Microsoft Windows 安装程序）。
1. 打开 PowerShell 控制台窗口。
1. 通过键入以下命令导入模块： `Import-Module OMSGateway`
1. 如果上一步骤未发生错误，则表示该模块已成功导入，可以使用 cmdlet。 输入 `Get-Module OMSGateway`
1. 使用 cmdlet 来进行更改后，重新启动 OMS 网关服务。

步骤 3 中的错误意味着该模块未导入。 PowerShell 找不到该模块时，可能发生该错误。 也可以在 OMS 网关安装路径中找到该模块：*C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*。

| **Cmdlet** | **参数** | **说明** | **示例** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |密钥 |获取服务的配置 |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |密钥（必需） <br> 值 |更改服务的配置 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |获取中继（上游）代理的地址 |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |地址<br> 用户名<br> 密码 |设置中继（上游）代理的地址（和凭据） |1.设置中继代理和凭据：<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2.设置不需要身份验证的中继代理：`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3.清除中继代理设置：<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |获取当前允许的主机 （仅限本地配置的允许的主机，不会自动下载的允许主机） |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |主机（必需） |将主机添加到允许列表 |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |主机（必需） |从允许列表中删除主机 |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |使用者（必需） |将客户端证书使用者添加到允许列表 |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |使用者（必需） |从允许列表中删除客户端证书使用者 |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |获取当前允许的客户端证书使用者 （仅限本地配置允许使用者，不会自动下载的允许使用者） |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>故障排除
若要收集网关记录的事件，应已安装 Log Analytics 代理。

![在 Log Analytics 网关日志事件查看器列表的屏幕截图](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>日志分析网关事件 Id 和说明

下表显示了事件 Id 和 Log Analytics 网关日志事件的说明。

| **ID** | **说明** |
| --- | --- |
| 400 |没有特定 id。 任何应用程序错误 |
| 401 |配置错误。 例如，为 listenPort ="text"而不是整数。 |
| 402 |分析 TLS 握手消息时发生异常。 |
| 403 |网络错误。 例如，不能连接到目标服务器。 |
| 100 |常规信息。 |
| 101 |服务已启动。 |
| 102 |服务已停止。 |
| 103 |从客户端收到 HTTP CONNECT 命令。 |
| 104 |不是 HTTP CONNECT 命令。 |
| 105 |目标服务器不在允许列表中，或者目标端口不是安全的 (443)。 <br> <br> 请确保你的 OMS 网关服务器上的 MMA 代理以及与 OMS 网关通信的代理已连接到同一个 Log Analytics 工作区。 |
| 105 |ERROR TcpConnection – 客户端证书无效：CN = 网关。 <br><br> 确保使用 OMS 网关版本号为 1.0.395.0 或更高版本。 此外请确保你的 OMS 网关服务器上的 MMA 代理以及与 OMS 网关通信的代理已连接到同一个 Log Analytics 工作区。 |
| 106 |不受支持的 TLS/SSL 协议版本。<br><br> Log Analytics 网关仅支持 TLS 1.0、 TLS 1.1 和 1.2。 它不支持 SSL。|
| 107 |已验证 TLS 会话。 |

### <a name="performance-counters-to-collect"></a>要收集的性能计数器

下表显示了 Log Analytics 网关可用的性能计数器。 使用性能监视器来添加计数器。

| **名称** | **说明** |
| --- | --- |
| Log Analytics 网关/活动客户端连接 |活动客户端网络 (TCP) 连接数 |
| Log Analytics 网关/错误计数 |错误数 |
| Log Analytics 网关/连接的客户端 |连接的客户端数 |
| Log Analytics 网关/拒绝计数 |由于发生任何 TLS 验证错误而拒绝的数目 |

![Log Analytics 的屏幕截图网关接口，显示性能计数器](./media/gateway/counters.png)

## <a name="assistance"></a>获取帮助
如果已登录到 Azure 门户，可以获取与 Log Analytics 网关或任何其他 Azure 服务或功能的帮助。
若要获取帮助，请选择问号图标的门户并选择右上角**新建支持请求**。 然后完成新建支持请求的表单。

![新的支持请求的屏幕截图](./media/gateway/support.png)

## <a name="next-steps"></a>后续步骤
[添加数据源](../../azure-monitor/platform/agent-data-sources.md)从连接的源收集数据并将数据存储在 Log Analytics 工作区中。