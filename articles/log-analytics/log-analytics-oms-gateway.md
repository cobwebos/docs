---
title: "使用 OMS 网关将脱机计算机连接到 Operations Management Suite | Microsoft Docs"
description: "使用 OMS 网关来连接由 Operations Management Suite 管理的、由 System Center Operations Manager 监视的脱机计算机，以便将数据发送到 Operations Management Suite 服务。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: magoedte; banders
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19bf9b3d65210458e4f018dc591e35e59287cd8e
ms.lasthandoff: 04/03/2017

---

# <a name="connect-offline-computers-to-operations-management-suite-by-using-oms-gateway"></a>使用 OMS 网关将脱机计算机连接到 Operations Management Suite

由 Operations Management Suite (OMS) 管理的、由 System Center Operations Manager (Operations Manager) 监视的脱机计算机在无法访问 Internet 时，可将数据发送到 Operations Management Suite 服务。 OMS 网关是使用 HTTP CONNECT 命令支持 HTTP 隧道的 HTTP 转发代理。 OMS 网关可以收集数据，然后代表脱机计算机将数据发送到 Operations Management Suite 服务。  

可将 OMS 网关与以下各项配合使用：

* Azure 自动化混合 Runbook 辅助角色。  
* 装有 Microsoft 监视代理并且可直接连接到 Operations Management Suite 工作区的 Windows 计算机。
* 与 Operations Management Suite 集成的 System Center Operations Manager 2012 SP1 更新汇总 (UR) 7、System Center Operations Manager 2012 R2 UR3 或 System Center Operations Manager 2016 管理组。  

有些 IT 安全策略不允许联网的计算机连接到 Internet。 此类计算机包括销售网点 (POS) 设备或支持 IT 服务的服务器。 但是，需要将这些计算机连接到 Operations Management Suite 才能管理和监视它们。 可以将脱机计算机配置为直接与 OMS 网关通信。 网关代表脱机计算机接收配置和转发数据。  

如果在计算机上设置了可直接连接到 Operations Management Suite 工作区的 Operations Management Suite 代理，则所有计算机将改用 OMS 网关通信。 网关直接将数据从代理传输到 Operations Management Suite。 网关不会分析传输中的任何数据。

如果将 Operations Manager 管理组与 Operations Management Suite 集成，可将管理服务器配置为连接到 OMS 网关。 服务器根据设置的解决方案接收配置信息，然后发送收集的数据。 Operations Manager 代理向管理服务器发送某些数据，例如 Operations Manager 警报、配置评估、实例空间和容量数据。 其他大批量的数据（例如 Internet Information Services (IIS) 日志、性能和安全事件）将直接发送到 OMS 网关。 

在外围网络（也称为 *DMZ*、*外围安全区域*或*屏蔽子网*）或其他隔离网络中部署的、用于监视不受信任系统的 Operations Manager 网关服务器无法与 OMS 网关通信。 Operations Manager 网关服务器只能向管理服务器报告。 如果设置一个 Operations Manager 管理组来与 OMS 网关通信，代理配置信息将自动分发到代理管理的、设置为收集 Azure Log Analytics 数据的每台计算机。 即使设置为空，也会自动分发代理配置信息。    

若要为通过网关与 Operations Management Suite 通信的直接连接的代理或 Operations Management 组提供高可用性，可以使用网络负载均衡 (NLB)。 NLB 在多个网关服务器之间重定向和分配流量。 如果一台网关服务器发生故障，流量将重定向到另一个可用节点。  

我们建议在运行 OMS 网关软件的计算机上安装 Operations Management Suite 代理。 这样，便可以监视 OMS 网关和分析性能或事件数据。 该代理还可帮助 OMS 网关识别需要与它通信的服务终结点。

每个代理必须与其网关建立网络连接，这样，代理才能自动与网关相互传输数据。 我们建议不要在域控制器上安装该网关。

下图显示了如何通过网关服务器将数据从直接代理传送到 Operations Management Suite。 代理的代理配置必须与 OMS 网关中设置用来与 Operations Management Suite 通信的端口匹配。  

![直接代理与 Operations Management Suite 通信的示意图](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

下图显示了数据如何从 Operations Manager 管理组流向 Operations Management Suite。   

![Operations Manager 与 Operations Management Suite 通信的示意图](./media/log-analytics-oms-gateway/oms-omsgateway-opsmgrconnect.png)

## <a name="prerequisites"></a>先决条件

指定用于运行 OMS 网关的计算机必须满足以下要求：

* Windows 10、Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 或 Windows Server 2008 操作系统
* Microsoft .NET Framework 4.5
* 4 核处理器（最低要求）
* 8 GB 内存 

### <a name="language-availability"></a>语言可用性

OMS 网关提供以下语言：

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
- 朝鲜语
- 波兰语
- 葡萄牙语(巴西)
- 葡萄牙语(葡萄牙)
- 俄语
- 西班牙语(国际)

## <a name="download-oms-gateway"></a>下载 OMS 网关

可通过三种方法获取最新版本的 OMS 网关：

* 从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=54443)下载。

* 从 Operations Management Suite 门户下载。 登录到 Operations Management Suite 工作区后：

    1. 转到“设置” > “连接的源” > “Windows 服务器”。 
    2. 选择“下载 OMS 网关”。

* 从 [Azure 门户](https://portal.azure.com)下载。 登录后：  

   1. 在服务列表中，选择“Log Analytics”。  
   2. 选择工作区。
   3. 在工作区边栏选项卡中的“常规”下面，选择“快速启动”。
   4. 在“选择要连接到工作区的数据源”下面，选择“计算机”。
   5. 在“直接代理”边栏选项卡中，选择“下载 OMS 网关”。<br><br> ![下载 OMS 网关](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-oms-gateway"></a>安装 OMS 网关

若要安装网关，请完成以下步骤。 

> [!NOTE]
> 如果以前已安装了某个网关版本（前称 Log Analytics Forwarder），该版本将升级到最新的 OMS 网关版本。
>

1. 在目标文件夹中，双击“OMS Gateway.msi”。
2. 在“欢迎”页上，选择“下一步”。<br><br> ![网关安装向导](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. 在“许可协议”页上，如果同意 Microsoft 软件许可条款，请选择“我接受许可协议中的条款”。 
4. 在“端口和代理地址”页上执行以下操作：

   1. 输入网关使用的 TCP 端口号。 安装程序将在 Windows 防火墙中配置使用此端口号的入站规则。 默认值为 8080。
      端口号的有效范围为 1 到 65535。 如果输入的端口号不在此范围内，会出现一条错误消息。
   2. （可选）如果安装网关的服务器需要通过代理通信，请输入网关需要连接到的代理地址， 例如 **http://myorgname.corp.contoso.com:80**。 如果将代理地址框留空，网关将尝试直接连接到 Internet。 如果代理服务器要求身份验证，请输入用户名和密码。<br><br> ![网关向导代理配置](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
5. 如果未在计算机上启用 Microsoft 更新，将显示“Microsoft 更新”页。 可以选择在此页上启用 Microsoft 更新。 否则，继续执行下一步。
6. 在“目标文件夹”页上，可以保留默认文件夹 C:\Program Files\OMS Gateway，或输入网关的安装位置。
7. 在“准备安装”页上，选择“安装”。 此时可能会显示一个对话框，要求授予安装权限。 请选择“是”。
8. 选择“完成”。 若要验证该服务是否正在运行，请打开 Services.msc 管理单元，然后检查服务列表中是否出现“OMS 网关”。 网关的状态应为“正在运行”。<br><br> ![检查 OMS 网关的服务](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="set-up-network-load-balancing"></a>设置网络负载均衡 
可以使用 NLB 设置 OMS 网关，使其具有高可用性。 可以使用 Windows Server 中的网络负载均衡功能或者基于硬件的负载均衡器。 为了管理流量，负载均衡器将在其节点之间重定向 Operations Management Suite 代理或 Operations Manager 管理服务器请求的连接。 如果一台网关服务器出现故障，流量将重定向到其他节点。

若要了解如何设计和部署 Windows Server 2016 网络负载均衡群集，请参阅[网络负载均衡](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing)。  

若要设置 Windows Server 网络负载均衡群集，请完成以下步骤：  

1. 使用管理帐户登录到属于网络负载均衡群集的 Windows 服务器。  
2. 在服务器管理器中打开网络负载均衡管理器。
3. 选择“工具”，然后选择“网络负载均衡管理器”。
4. 若要将 OMS 网关服务器连接到安装的 Microsoft Monitoring Agent，请右键单击群集的 IP 地址，然后选择“将主机添加到群集”。<br><br> ![网络负载均衡管理器：将主机添加到群集](./media/log-analytics-oms-gateway/nlb02.png)<br> 
5. 输入要连接的网关服务器的 IP 地址。<br><br> ![网络负载均衡管理器：将主机添加到群集 - 连接](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="set-up-the-operations-management-suite-agent-and-an-operations-manager-management-group"></a>设置 Operations Management Suite 代理和 Operations Manager 管理组
本部分介绍如何设置直接连接的 Operations Management Suite 代理和 Operations Manager 管理组。 还可以将 Azure 自动化混合 Runbook 辅助角色设置为使用 OMS 网关来与 Operations Management Suite 通信。  

若要了解有关在直接连接到 Operations Management Suite 的 Windows 计算机上安装 Operations Management Suite 代理的要求和步骤，请参阅[将 Windows 计算机连接到 Operations Management Suite](log-analytics-windows-agents.md)。 对于 Linux 计算机，请参阅[将 Linux 计算机连接到 Operations Management Suite](log-analytics-linux-agents.md)。 

### <a name="set-up-the-operations-management-suite-agent-and-operations-manager-to-use-the-oms-gateway-as-a-proxy-server"></a>将 Operations Management Suite 代理和 Operations Manager 设置为使用 OMS 网关作为代理服务器

### <a name="set-up-a-standalone-operations-management-suite-agent"></a>设置独立的 Operations Management Suite 代理
有关将代理设置为使用代理服务器的信息，请参阅[使用 Microsoft Monitoring Agent 配置代理和防火墙设置](log-analytics-proxy-firewall.md)。 在本例中，代理服务器为网关。 如果在网络负载均衡器后面部署了多个网关服务器，Operations Management Suite 代理程序代理配置是网络负载均衡器的虚拟 IP 地址：<br><br> ![Microsoft Monitoring Agent 属性：代理设置](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="set-up-operations-manager-all-agents-use-the-same-proxy-server"></a>设置 Operations Manager（所有代理使用相同的代理服务器）
设置 Operations Manager 以添加网关服务器。 即使设置为空，Operations Manager 代理配置也会自动应用到向 Operations Manager 报告的所有代理。

若要将 OMS 网关与 Operations Manager 配合使用，必须满足以下要求：

* 必须在网关服务器上安装 Microsoft Monitoring Agent（代理版本 **8.0.10900.0** 或以上）。 必须根据要通信的 Operations Management Suite 代理工作区设置该代理。
* 该网关必须已建立 Internet 连接，或者已连接到可访问 Internet 的代理服务器。

> [!NOTE]
> 如果未指定网关的值，会将空值推送到所有代理。

若要将 Operations Manager 服务器连接到 Operations Management Suite 代理工作区，请执行以下操作：

1. 在 Operations Manager 控制台中，转到“Operations Management Suite” > “连接” > “配置代理服务器”。<br><br> ![Operations Manager：配置代理服务器](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. 选择“使用代理服务器访问 Operations Management Suite”。 输入 OMS 网关服务器的 IP 地址或网络负载均衡器的虚拟 IP 地址。 请务必首先键入 **http://** 前缀。<br><br> ![Operations Manager：代理服务器地址](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. 选择“完成”。 

### <a name="set-up-operations-manager-specific-agents-use-the-proxy-server"></a>设置 Operations Manager（特定的代理使用代理服务器）
在大型或复杂环境中，你可能只希望特定的服务器（或组）使用 OMS 网关服务器。 对于这些服务器，无法直接更新 Operations Manager 代理。 该值将被管理组的全局值覆盖。 应该重写用于推送这些值的规则。

> [!NOTE] 
> 可以使用相同的配置方法在环境中运行多个 OMS 网关服务器。 例如，可能需要根据每个区域指定特定的 OMS 网关服务器。

1. 在 System Center Operations Manager 控制台中，选择“创作”工作区。  
2. 选择“规则”，然后在 System Center Operations Manager 工具栏上选择“范围”按钮。 如果此按钮不可用，请确保已在“监视”窗格中选择了一个对象而不是文件夹。 “范围管理包对象”对话框显示了通用目标类、组或对象的列表。 
3. 在“查找”框中输入“运行状况服务”。 从列表中选择该服务。 选择“确定”。  
4. 在 Operations Manager 控制台工具栏上，搜索规则“顾问代理设置规则”。 选择“重写”，然后指向“重写规则\针对类的特定对象: 运行状况服务”。 从列表中选择特定的对象。 或者，可以创建一个自定义组并在其中包含要将此重写应用到的服务器的运行状况服务对象。 然后向该组应用该重写。
5. 在“重写属性”对话框中的“WebProxyAddress”参数旁边，选择“重写”列。 在“重写值”框中，输入 OMS 网关服务器的 URL。 请务必首先键入 **http://** 前缀。

   >[!NOTE]
   > 不需要启用该规则。 System Center 顾问安全引用重写管理包中的某个重写会自动管理该规则。 该管理包面向 System Center 顾问监视服务器组。
   > 

6. 若要指定管理包，请执行以下操作之一：
    * 在“选择目标管理包”列表中选择一个管理包。
    * 若要创建新的未密封管理包，请选择“新建”。 
7. 选择“确定”。 

### <a name="set-up-automation-hybrid-workers"></a>设置自动化混合辅助角色
如果在环境中使用 Azure 自动化混合 Runbook 辅助角色，可以创建一个手动的临时解决方法，使网关支持这些角色。

执行以下步骤时，需要知道自动化帐户所在的 Azure 区域。 若要查找该位置，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择 Azure 自动化服务。
3. 选择相关的 Azure 自动化帐户。
4. 在“位置”下面即可看到该区域。<br><br> ![Azure 门户：自动化帐户位置](./media/log-analytics-oms-gateway/location.png)  

使用下表识别每个位置的 URL：

**作业运行时数据服务 URL**

| 位置 | 代码 |
| --- | --- |
| 美国中北部 |ncus-jobruntimedata-prod-su1.azure-automation.net |
| 欧洲西部 |we-jobruntimedata-prod-su1.azure-automation.net |
| 美国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 美国东部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 欧洲北部 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 东南亚 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 日本 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 澳大利亚 |ase-jobruntimedata-prod-su1.azure-automation.net |

**代理服务 URL**

| 位置 | 代码 |
| --- | --- |
| 美国中北部 |ncus-agentservice-prod-1.azure-automation.net |
| 欧洲西部 |we-agentservice-prod-1.azure-automation.net |
| 美国中南部 |scus-agentservice-prod-1.azure-automation.net |
| 美国东部 2 |eus2-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-agentservice-prod-1.azure-automation.net |
| 欧洲北部 |ne-agentservice-prod-1.azure-automation.net |
| 东南亚 |sea-agentservice-prod-1.azure-automation.net |
| 印度中部 |cid-agentservice-prod-1.azure-automation.net |
| 日本 |jpe-agentservice-prod-1.azure-automation.net |
| 澳大利亚 |ase-agentservice-prod-1.azure-automation.net |

如果计算机已自动注册为混合 Runbook 辅助角色以便使用更新管理解决方案进行修补，请完成以下步骤：

1. 将作业运行时数据服务 URL 添加到 OMS 网关上的“允许的主机”列表。 例如：  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. 使用以下 Azure PowerShell cmdlet 重新启动 OMS 网关服务：`Restart-Service OMSGatewayService`

如果已使用混合 Runbook 辅助角色注册 cmdlet 将计算机加入 Azure 自动化，请完成以下步骤：

1. 将代理服务注册 URL 添加到 OMS 网关上的“允许的主机”列表。 例如： `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. 将作业运行时数据服务 URL 添加到 OMS 网关上的“允许的主机”列表。 例如：  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. 重新启动 OMS 网关服务： `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>有用的 PowerShell cmdlet
可以借助 Cmdlet 来更新 OMS 网关设置。 在使用 PowerShell cmdlet 之前，请确保：

1. 安装 OMS 网关 (MSI)。
2. 打开 PowerShell 控制台窗口。
3. 若要导入模块，请输入以下命令：`Import-Module OMSGateway` 如果未发生错误，则表示已成功导入该模块，可以使用 cmdlet。 
4. 输入 `Get-Module OMSGateway`。
5. 使用 cmdlet 完成更改后，重新启动 OMS 网关服务。

如果步骤 3 出错，则表示该模块未导入。 如果 PowerShell 找不到模块，则可能会发生此错误。 该模块列在网关的安装路径中：C:\Program Files\Microsoft OMS Gateway\PowerShell。

| Cmdlet | parameters | 说明 | 示例 |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |密钥（必需） <br> 值 |更改服务配置 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |键 |获取服务配置 |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |地址 <br> 用户名 <br> 密码 |设置中继（上游）代理的地址（和凭据） |1.设置答复代理和凭据：`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2.设置不需要身份验证的答复代理：`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3.清除答复代理设置，也就是不需要使用答复代理：`Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |获取中继（上游）代理的地址 |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |主机（必需） |将主机添加到允许列表 |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |主机（必需） |从允许列表中删除主机 |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |获取当前允许的主机（仅限本地配置的允许主机；不包括自动下载的允许主机） |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |使用者（必需） |将客户端证书使用者添加到允许列表 |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |使用者（必需） |从允许列表中删除客户端证书使用者 |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |获取当前允许的客户端证书使用者（仅限本地配置的允许使用者；不包括自动下载的允许使用者） |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshooting"></a>故障排除
若要收集网关记录的事件，需要安装 Operations Management Suite 代理。<br><br> ![事件查看器：OMS 网关日志](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS 网关事件 ID 和说明**

下表列出了 OMS 网关日志事件的事件 ID 和说明：

| ID | 说明 |
| --- | --- |
| 400 |没有特定 ID 的任何应用程序错误 |
| 401 |配置错误。 例如：为 listenPort = "\<text\>" 而不是整数 |
| 402 |分析传输层安全性 (TLS) 握手消息时发生异常 |
| 403 |网络错误。 例如：无法连接到目标服务器 |
| 100 |常规信息 |
| 101 |服务已启动 |
| 102 |服务已停止 |
| 103 |已从客户端收到 HTTP CONNECT 命令 |
| 104 |不是 HTTP CONNECT 命令 |
| 105 |目标服务器不在允许列表中，或者目标端口不是安全端口 (443) <br> <br> 请确保网关服务器上的 Microsoft Monitoring Agent 代理以及与网关通信的代理已连接到同一个 Log Analytics 工作区。 |
| 105 |ERROR TcpConnection – 客户端证书无效: CN=网关 <br><br> 请确保： <br>    <br> &#149; 使用版本号为 1.0.395.0 或更高的 OSM 网关。 <br> &#149; 网关服务器上的 Microsoft Monitoring Agent 代理以及与网关通信的代理已连接到同一个 Log Analytics 工作区。 |
| 106 |将 TLS 会话视为可疑或将其拒绝的任何原因 |
| 107 |已验证 TLS 会话 |

**要收集的性能计数器**

下表列出了 OMS 网关可用的性能计数器。 可以使用 Windows 性能监视器来添加计数器。

| Name | 说明 |
| --- | --- |
| OMS 网关/活动客户端连接 |活动客户端网络 (TCP) 连接数 |
| OMS 网关/错误计数 |错误数 |
| OMS 网关/连接的客户端 |连接的客户端数 |
| OMS 网关/拒绝计数 |由于发生任何 TLS 验证错误而拒绝的数目 |

![OMS 网关性能计数器](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>获取帮助
在 Azure 门户中登录后，可以针对 OMS 网关、其他任何 Azure 服务或服务功能创建支持请求。
若要请求帮助，请选择门户右上角的问号，然后选择“新建支持请求”。 填写新建支持请求的表单。

![新建支持请求](./media/log-analytics-oms-gateway/support.png)

也可以在 [Microsoft Azure 反馈论坛](https://feedback.azure.com/forums/267889)中提供有关 Operations Management Suite 或 Log Analytics 的反馈。

## <a name="next-steps"></a>后续步骤
若要从 Operations Management Suite 工作区中已连接的源收集数据，请[添加数据源](log-analytics-data-sources.md)。 可将数据存储在 Operations Management Suite 存储库中。

