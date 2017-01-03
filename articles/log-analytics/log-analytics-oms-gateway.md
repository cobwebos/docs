---
title: "使用 OMS 网关将计算机和设备连接到 OMS | Microsoft 文档"
description: "使用 OMS 网关连接 OMS 管理的设备和 Operations Manager 监视的计算机，以便将数据发送到无法访问 Internet 的 OMS 服务。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: dc6cdf1630407d1c4439d89aca5a8254bb806eba
ms.openlocfilehash: 41382ba85fee1c3f5b1570748cd3d5b0641b9593


---
# <a name="connect-computers-and-devices-to-oms-using-the-oms-gateway"></a>使用 OMS 网关将计算机和设备连接到 OMS
本文档介绍 OMS 管理的设备和 System Center Operations Manager (SCOM) 监视的计算机在无法访问 Internet 的情况下，如何将数据发送到 OMS 服务。 OMS 网关可以收集数据，然后代表这些设备将数据发送到 OMS 服务。

网关是使用 HTTP CONNECT 命令支持 HTTP 隧道的 HTTP 转发代理。 在使用 Windows 操作系统的 4 核 CPU、16-GB 服务器上运行时，网关最多可以处理 2000 个并行连接的 OMS 设备。

例如，企业或大型组织中的某些服务器可能已建立网络连接，但不一定建立了 Internet 连接。 另举一例，客户可能部署了许多销售网点 (POS) 设备，但却无法直接监视这些设备。 再举一例，Operations Manager 可以使用 OMS 网关作为代理服务器。 在这些示例中，OMS 网关可将数据从安装在这些服务器或 POS 设备上的代理传输到 OMS。

所有代理数据并不是由每个代理直接发送到 OMS（这样就需要建立直接 Internet 连接），而是通过一台建立了 Internet 连接的计算机发送。 网关就是在这台计算机上安装和使用的。 在此方案中，可以在想要收集数据的任何计算机上安装代理。 然后，网关会直接将数据从代理传输到 OMS，但不对传输的任何数据进行分析。

若要监视 OMS 网关以及分析装有该网关的服务器的性能或事件数据，必须在同样装有该网关的计算机上安装 OMS 代理。

网关必须能够访问 Internet 才能将数据上载到 OMS。 每个代理也必须与其网关建立网络连接，这样，代理才能自动与网关相互传输数据。 为获得最佳效果，请不要在同时充当域控制器的计算机上安装网关。

下图显示了数据如何从直接代理流向 OMS。

![直接代理示意图](./media/log-analytics-oms-gateway/direct-agent-diagram.png)

下图显示了数据如何从 Operations Manager 流向 OMS。

![Operations Manager 示意图](./media/log-analytics-oms-gateway/scom-mgt-server.png)

## <a name="install-the-oms-gateway"></a>安装 OMS 网关
安装此网关会取代以前已安装的网关版本 (Log Analytics Forwarder)。

先决条件：.Net Framework 4.5、Windows Server 2012 R2 SP1 和更高版本

1. 从 [Microsoft 下载中心](http://download.microsoft.com/download/2/5/C/25CF992A-0347-4765-BD7D-D45D5B27F92C/OMS%20Gateway.msi)下载最新版本的 OMS 网关。
2. 若要开始安装，请双击“OMS Gateway.msi”。
3. 在“欢迎”页上，单击“下一步”。  
    ![网关安装向导](./media/log-analytics-oms-gateway/gateway-wizard01.png)
4. 在“许可协议”页上，选择“我接受许可协议中的条款”表示同意 EULA，然后单击“下一步”。
5. 在端口和代理地址页上执行以下操作：
   1. 键入网关使用的 TCP 端口号。 安装程序将在 Windows 防火墙中打开此端口号。 默认值为 8080。
      端口号的有效范围为 1 - 65535。 如果输入的端口号不在此范围内，会出现一条错误消息。
   2. （可选）如果安装网关的服务器需要使用代理，请键入网关需要连接到的代理地址， 例如 `http://myorgname.corp.contoso.com:80`。如果将此地址留空，网关将尝试直接连接到 Internet。 否则，网关将连接到代理。 如果代理服务器要求身份验证，请键入你的用户名和密码。
       ![网关向导代理配置](./media/log-analytics-oms-gateway/gateway-wizard02.png)  
   3. 单击“下一步”
6. 如果尚未启用 Microsoft 更新，将会显示“Microsoft 更新”页，可以在其中选择启用 Microsoft 更新。 做出选择，然后单击“下一步”。 否则，继续执行下一步。
7. 在“目标文件夹”页上，保留默认文件夹 **%ProgramFiles%\OMS Gateway** 或键入网关的安装位置，然后单击“下一步”。
8. 在“准备安装”页上，单击“安装”。 此时可能会显示“用户帐户控制”，请求提供安装权限。 在此情况下，请单击“是”。
9. 安装完成后，单击“完成”。 可以验证该服务是否正在运行，方法是打开 services.msc 管理单元，然后检查服务列表中是否出现“OMS 网关”。  
    ![服务 - OMS 网关](./media/log-analytics-oms-gateway/gateway-service.png)

## <a name="install-an-agent-on-devices"></a>在设备上安装代理
如果需要，请参阅[将 Windows 计算机连接到 Log Analytics](log-analytics-windows-agents.md)，了解如何安装直接连接的代理。 本文介绍如何使用安装向导或命令行来安装代理。

## <a name="configure-oms-agents"></a>配置 OMS 代理
请参阅[使用 Microsoft Monitoring Agent 配置代理和防火墙设置](log-analytics-proxy-firewall.md)，了解如何将代理配置为使用代理服务器（在本例中为网关）。

Operations Manager 代理通过管理服务器发送某些数据，例如 Operations Manager 警报、配置评估、实例空间和容量数据。 其他大批量的数据（例如 IIS 日志、性能和安全数据）将直接发送到 OMS 网关。 请参阅[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)，获取通过每个通道发送的数据的完整列表。

> [!NOTE]
> 如果计划使用具有网络负载均衡功能的网关，请参阅 [Optionally configure network load balancing](#optionally-configure-network-load-balancing)（选择性地配置网络负载均衡）。
>
>

## <a name="configure-a-scom-proxy-server"></a>配置 SCOM 代理服务器
可对 Operations Manager 进行配置，添加要充当代理服务器的网关。 更新代理配置时，代理配置将自动应用到向 Operations Manager 报告的所有代理。

若要使用网关来支持 Operations Manager，需要满足以下条件：

* 在网关服务器上安装 Microsoft Monitoring Agent（代理版本 – **8.0.10900.0** 或以上），并根据要通信的 OMS 工作区配置该代理。
* 该网关必须已建立 Internet 连接，或者已连接到可访问 Internet 的代理服务器。

### <a name="to-configure-scom-for-the-gateway"></a>为网关配置 SCOM
1. 打开 Operations Manager 控制台，在“Operations Management Suite”下面单击“连接”，然后单击“配置代理服务器”：  
    ![Operations Manager – 配置代理服务器](./media/log-analytics-oms-gateway/scom01.png)
2. 选择“使用代理服务器访问 Operations Management Suite”，然后键入 OMS 网关服务器的 IP 地址。 请务必首先键入 `http://` 前缀：  
    ![Operations Manager – 代理服务器地址](./media/log-analytics-oms-gateway/scom02.png)
3. 单击“完成” 。 Operations Manager 服务器已连接到 OMS 工作区。

## <a name="configure-network-load-balancing"></a>配置网络负载均衡
可以通过创建群集，将网关配置为使用网络负载均衡实现高可用性。 该群集通过在其节点之间重定向 Microsoft Monitoring Agent 请求的连接，对代理发出的流量进行管理。 如果一台网关服务器出现故障，流量将重定向到其他节点。

1. 打开网络负载均衡管理器并创建群集。
2. 在添加网关之前请右键单击该群集，然后选择“群集属性”。 将群集配置为使用自身的 IP 地址：  
    ![网络负载均衡管理器 – 群集 IP 地址](./media/log-analytics-oms-gateway/nlb01.png)
3. 若要连接装有 Microsoft Monitoring Agent 的 OMS 网关服务器，请右键单击群集的 IP 地址，然后单击“将主机添加到群集”。  
    ![网络负载均衡管理器 – 将主机添加到群集](./media/log-analytics-oms-gateway/nlb02.png)
4. 输入要连接的网关服务器的 IP 地址：  
    ![网络负载均衡管理器 – 将主机添加到群集：连接](./media/log-analytics-oms-gateway/nlb03.png)
5. 在未建立 Internet 连接的计算机上，请务必在配置“Microsoft Monitoring Agent 属性”时使用群集的 IP 地址：  
    ![Microsoft Monitoring Agent 属性 – 代理设置](./media/log-analytics-oms-gateway/nlb04.png)

## <a name="configure-for-automation-hybrid-workers"></a>配置自动化混合辅助角色
如果在环境中使用自动化混合辅助角色，可遵循以下步骤，通过手动的临时解决方法将网关配置为支持这些角色。

执行以下步骤时，需要知道自动化帐户所在的 Azure 区域。 若要查找该位置，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择 Azure 自动化服务。
3. 选择相应的 Azure 自动化帐户。
4. 在“位置”下面即可查看该帐户所在的区域。  
    ![Azure 门户 – 自动化帐户位置](./media/log-analytics-oms-gateway/location.png)

使用下表识别每个位置的 URL：

**作业运行时数据服务 URL**

| **位置** | **URL** |
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

| **位置** | **URL** |
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

如果计算机已自动注册为混合辅助角色以便使用更新管理解决方案进行修补，请使用以下步骤：

1. 将作业运行时数据服务 URL 添加到 OMS 网关上的“允许的主机”列表。 例如：  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. 使用以下 PowerShell cmdlet 重新启动 OMS 网关服务：`Restart-Service OMSGatewayService`

如果已使用混合辅助角色注册 cmdlet 将计算机加入 Azure 自动化，请使用以下步骤：

1. 将代理服务注册 URL 添加到 OMS 网关上的“允许的主机”列表。 例如： `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. 将作业运行时数据服务 URL 添加到 OMS 网关上的“允许的主机”列表。 例如：  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. 重新启动 OMS 网关服务。
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>有用的 PowerShell cmdlet
可以借助 cmdlet 来完成更新 OMS 网关配置设置所要执行的任务。 在使用这些这些 cmdlet 之前，请确保：

1. 安装 OMS 网关 (MSI)。
2. 打开 PowerShell 窗口。
3. 若要导入模块，请键入以下命令：`Import-Module OMSGateway`
4. 如果上一步骤未发生错误，则表示该模块已成功导入，可以使用 cmdlet。 键入 `Get-Module OMSGateway`
5. 使用 cmdlet 做出更改后，请务必重新启动网关服务。

如果步骤 3 出错，则表示该模块未导入。 出错的原因可能是 PowerShell 找不到模块。 可以在网关的安装路径中找到该模块：C:\Program File\Microsoft OMS Gateway\PowerShell。

| **Cmdlet** | **参数** | **说明** | **示例** |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |密钥（必需） <br> 值 |更改服务的配置 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |密钥 |获取服务的配置 |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |地址 <br> 用户名 <br> 密码 |设置中继（上游）代理的地址（和凭据） |1.设置答复代理和凭据：`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2.设置不需要身份验证的答复代理：`Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3.清除答复代理设置，也就是不需要使用答复代理：`Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |获取中继（上游）代理的地址 |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |主机（必需） |将主机添加到允许列表 |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |主机（必需） |从允许列表中删除主机 |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |获取当前允许的主机（仅限本地配置的允许主机，不包括自动下载的允许主机） |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |使用者（必需） |将客户端证书使用者添加到允许列表 |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |使用者（必需） |从允许列表中删除客户端证书使用者 |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |获取当前允许的客户端证书使用者（仅限本地配置的允许使用者，不包括自动下载的允许使用者） |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshoot"></a>故障排除
建议在装有网关的计算机上安装 OMS 代理。 然后，便可以使用该代理来收集网关记录的事件。

![事件查看器 – OMS 网关日志](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS 网关事件 ID 和说明**

下表显示了 OMS 网关日志事件的事件 ID 和说明。

| **ID** | **说明** |
| --- | --- |
| 400 |没有特定 ID 的任何应用程序错误 |
| 401 |配置错误。 例如：为 listenPort 提供的值是“文本”而不是整数 |
| 402 |分析 TLS 握手消息时发生异常 |
| 403 |网络错误。 例如：无法连接到目标服务器 |
| 100 |常规信息 |
| 101 |服务已启动 |
| 102 |服务已停止 |
| 103 |已从客户端收到 HTTP CONNECT 命令 |
| 104 |不是 HTTP CONNECT 命令 |
| 105 |目标服务器不在允许列表中，或者目标端口不是安全端口 (443) <br> <br> 请确保网关服务器上的 MMA 代理以及与网关通信的代理已连接到同一个 Log Analytics 工作区。 |
| 105 |ERROR TcpConnection – 客户端证书无效: CN=网关 <br><br> 请确保： <br>    <br> &#149; 使用版本号为 1.0.395.0 或更高的网关。 <br> &#149; 网关服务器上的 MMA 代理以及与网关通信的代理已连接到同一个 Log Analytics 工作区。 |
| 106 |将 TLS 会话视为可疑或将其拒绝的任何原因 |
| 107 |已验证 TLS 会话 |

**要收集的性能计数器**

下表显示了 OMS 网关可用的性能计数器。 可以使用性能监视器来添加计数器。

| **Name** | **说明** |
| --- | --- |
| OMS 网关/活动客户端连接 |活动客户端网络 (TCP) 连接数 |
| OMS 网关/错误计数 |错误数 |
| OMS 网关/连接的客户端 |连接的客户端数 |
| OMS 网关/拒绝计数 |由于发生任何 TLS 验证错误而拒绝的数目 |

![OMS 网关性能计数器](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>获取帮助
登录到 Azure 门户后，可以针对 OMS 网关、其他任何 Azure 服务或服务功能创建支持请求。
若要请求帮助，请单击门户右上角的问号，然后单击“新建支持请求”。 接下来，填写新建支持请求的表单。

![新建支持请求](./media/log-analytics-oms-gateway/support.png)

同时，欢迎在 [Microsoft Azure 反馈论坛](https://feedback.azure.com/forums/267889)上提供有关 OMS 或 Log Analytics 的反馈。

## <a name="next-steps"></a>后续步骤
* [添加数据源](log-analytics-data-sources.md)，以便从 OMS 工作区中已连接的源收集数据并将其存储在 OMS 存储库中。



<!--HONumber=Nov16_HO4-->


