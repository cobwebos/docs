---
title: 使用 OMS 网关连接计算机 | Microsoft Docs
description: 使用 OMS 网关连接设备和 Operations Manager 监视的计算机，以便在无法访问 Internet 时将数据发送到 Azure 自动化和 Log Analytics 服务。
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 66e5444f5346a44cfc8a43cf2b43dbaeacffedc9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="connect-computers-without-internet-access-by-using-the-oms-gateway"></a>使用 OMS 网关连接无法访问 Internet 的计算机
本文档介绍直接连接或 Operations Manager 监视的计算机无法访问 Internet 时，如何使用 OMS 网关，配置与 Azure 自动化和 Log Analytics 的通信。 OMS 网关是使用 HTTP CONNECT 命令支持 HTTP 隧道的 HTTP 转发代理。 它可以收集数据，然后代表这些计算机将数据发送到 Azure 自动化和 Log Analytics，而无需访问 Internet。  

OMS 网关支持：

* Azure 自动化混合 Runbook 辅助角色  
* 装有 Microsoft Monitoring Agent，可直接连接到 Log Analytics 工作区的 Windows 计算机
* 装有适用于 Linux 的 OMS 代理，可直接连接到 Log Analytics 工作区的 Linux 计算机  
* 与 Log Analytics 集成的 System Center Operations Manager 2012 SP1 with UR7、Operations Manager 2012 R2 with UR3、Operations Manager 2016 和 Operations Manager 版本 1801 管理组  

如果 IT 安全策略不允许网络中的某些计算机（例如销售点设备或支持 IT 服务的服务器）连接到 Internet，但需要将其连接到 Azure 自动化或 Log Analytics 以便管理和监视这些计算机，则可以将它们配置为直接与 OMS 网关通信。

 如果这些计算机上配置了可直接连接到 Log Analytics 工作区的 OMS 代理，则所有计算机将改为与 OMS 网关通信。 OMS 网关直接将数据从代理传输到服务。 它不会分析传输中的任何数据。

如果已将 Operations Manager 管理组与 Log Analytics 集成，可将管理服务器配置为连接到 OMS 网关，以接收配置信息并发送收集的数据。 Operations Manager 代理向管理服务器发送某些数据，例如 Operations Manager 警报、配置评估、实例空间和容量数据。 其他大批量的数据（例如 IIS 日志、性能信息和安全事件）将直接发送到 OMS 网关。  

如果在外围网络或其他隔离网络中部署了一个或多个 Operations Manager 网关服务器用于监视不受信任的系统，这些服务器无法与 OMS 网关通信。 Operations Manager 网关服务器只能向管理服务器报告。 

如果将 Operations Manager 管理组配置为与 OMS 网关通信，代理配置信息会自动分发到代理管理的、配置为收集 Log Analytics 数据的每台计算机，即使设置为空。    

若要为通过 OMS 网关与 Log Analytics 通信的直接连接的组或 Operations Management 组提供高可用性，可以使用网络负载均衡在多个 OMS 网关服务器之间重定向和分配流量。 如果一台网关服务器发生故障，流量将重定向到另一个可用节点。  

我们建议在运行 OMS 网关软件的计算机上安装 OMS 代理，用于监视 OMS 网关和分析性能或事件数据。 此外，该代理可帮助 OMS 网关标识它需要与之进行通信的服务终结点。

所有代理必须与其网关建立网络连接，这样，代理才能自动与网关相互传输数据。 我们不建议在域控制器上安装网关。

下图显示了如何使用网关服务器将数据从直接代理传送到 Azure 自动化和 Log Analytics。 代理中的代理配置必须使用 OMS 网关中配置用来与服务通信的同一端口。  

![直接代理与服务通信的示意图](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

下图显示了数据如何从 Operations Manager 管理组流向 Log Analytics。   

![Operations Manager 与 Log Analytics 通信的示意图](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>先决条件

在将某台计算机指定为运行 OMS 网关时，请确保此计算机包含以下组件：

* Windows 10、Windows 8.1 和 Windows 7
* Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 和 Windows Server 2008
* .NET Framework 4.5
* 4 核处理器，8 GB 内存（最低要求） 

### <a name="language-availability"></a>语言可用性

OMS 网关提供了以下语言：

- 简体中文
- 繁体中文
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

### <a name="supported-encryption-protocols"></a>支持的加密协议
OMS 网关仅支持传输层安全性 (TLS) 1.0、1.1 和 1.2。  它不支持安全套接字层 (SSL)。

### <a name="supported-number-of-agent-connections"></a>支持的代理连接数
下表突出显示了支持与网关服务器通信的代理数量。 支持基于代理每 6 秒上传大约 200 KB 数据计算。 测试的每个代理的数据量约为每天 2.7 GB。

|网关 |支持的代理近似数量|  
|--------|----------------------------------|  
|- CPU：Intel XEON CPU E5-2660 v3 @ 2.6 GHz，双核<br> - 内存：4 GB<br> - 网络带宽：1 Gbps| 600|  
|- CPU：Intel XEON CPU E5-2660 v3 @ 2.6 GHz，四核<br> - 内存：8 GB<br> - 网络带宽：1 Gbps| 1000|  

## <a name="download-the-oms-gateway"></a>下载 OMS 网关

可通过两种方法获取最新版本的 OMS 网关安装程序文件。

1. 从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=54443)下载。

2. 从 Azure 门户下载。 登录到 Azure 门户之后，请执行以下步骤：  

   1. 浏览服务列表，并选择“Log Analytics”。  
   2. 选择工作区。
   3. 在工作区边栏选项卡中的“常规”下面，选择“快速启动”。
   4. 在“选择要连接到工作区的数据源”下面，选择“计算机”。
   5. 在“直接代理”面板中，选择“下载 OMS 网关”。
   
    ![下载 OMS 网关](./media/log-analytics-oms-gateway/download-gateway.png)

-- 或 -- 

   1. 在工作区边栏选项卡中的“设置”下方，选择“高级设置”。
   2. 选择“连接的源” > “Windows 服务器”。 然后选择“下载 OMS 网关”。

## <a name="install-the-oms-gateway"></a>安装 OMS 网关

若要安装网关，请执行以下步骤。 如果以前已安装某个版本（前称 *Log Analytics Forwarder*），该版本将升级到此版本。  

1. 在目标文件夹中，选择“OMS Gateway.msi”。
2. 在“欢迎”页上，选择“下一步”。

 ![网关安装向导](./media/log-analytics-oms-gateway/gateway-wizard01.png)
  
3. 在“许可协议”页上，选择“我接受许可协议中的条款”。 然后，选择“下一步”。

4. 在“端口和代理地址”页上执行以下操作：

   a. 键入网关使用的 TCP 端口号。 安装程序会在 Windows 防火墙中配置使用此端口号的入站规则。 默认值为 8080。 端口号的有效范围为 1 到 65535。 如果输入的端口号不在此范围内，会出现一条错误消息。

   b. （可选）如果安装网关的服务器需要通过代理通信，请键入网关需要连接到的代理， 例如，以下屏幕截图中所示的 `http://myorgname.corp.contoso.com:80`。 如果将此地址留空，网关将尝试直接连接到 Internet。  如果代理服务器要求身份验证，请输入用户名和密码。
   
    ![网关向导代理配置](./media/log-analytics-oms-gateway/gateway-wizard02.png)

4. 选择“**下一步**”。

5. 如果尚未启用 Microsoft 更新，会显示“Microsoft 更新”页，可以在其中选择启用 Microsoft 更新。 做出选择，并选择“下一步”。 否则，继续执行下一步。

6. 在“目标文件夹”页上，将默认文件夹保留为“C:\Program Files\OMS Gateway”，或键入 OMS 网关的安装位置。 然后，选择“下一步”。

7. 在“准备安装”页上，选择“安装”。 此时可能会显示“用户帐户控制”，请求提供安装权限。 如果系统请求提供权限，请选择“是”。

8. 安装完成后，选择“完成”。 可以验证该服务是否正在运行，方法是打开 services.msc 管理单元，并确保服务列表中是否出现“OMS 网关”并且其状态为“正在运行”。

    ![服务 - OMS 网关](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>配置网络负载均衡 
可以使用网络负载均衡 (NLB) 来配置网关的高可用性。 请使用 Microsoft 网络负载均衡或基于硬件的负载均衡器。  负载均衡器通过在其节点之间重定向 OMS 代理或 Operations Manager 管理服务器请求的连接来管理流量。 如果一台网关服务器出现故障，流量将重定向到其他节点。

若要了解如何设计和部署 Windows Server 2016 网络负载均衡群集，请参阅[网络负载均衡](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing)。  以下步骤介绍如何配置 Microsoft 网络负载均衡群集。  

1.  使用管理帐户登录到属于 NLB 群集的 Windows 服务器。  

2.  在服务器管理器中打开网络负载均衡管理器。 选择“工具”，并选择“网络负载均衡管理器”。

3. 若要连接装有 Microsoft Monitoring Agent 的 OMS 网关服务器，请右键单击群集的 IP 地址，然后选择“将主机添加到群集”。

 ![网络负载均衡管理器 – 将主机添加到群集](./media/log-analytics-oms-gateway/nlb02.png)

4. 输入要连接的网关服务器的 IP 地址。

    ![网络负载均衡管理器 - 将主机添加到群集：连接](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-the-oms-agent-and-the-operations-manager-management-group"></a>配置 OMS 代理和 Operations Manager 管理组
本文的以下部分包含有关如何使用 OMS 网关配置直接连接的 OMS 代理、Operations Manager 管理组或 Azure 自动化混合 Runbook 辅助角色，使其与 Azure 自动化或 Log Analytics 通信的步骤。  

若要了解在直接连接到 Log Analytics 的 Windows 计算机上安装 OMS 代理的要求和步骤，请参阅[使用 Log Analytics 从环境中的计算机收集数据](log-analytics-concept-hybrid.md#prerequisites)。

 对于 Linux 计算机，请参阅[将 Linux 计算机连接到 Log Analytics](log-analytics-quick-collect-linux-computer.md)。 有关自动化混合 Runbook 辅助角色的信息，请参阅[部署混合 Runbook 辅助角色](../automation/automation-hybrid-runbook-worker.md)。

### <a name="configure-the-standalone-oms-agent"></a>配置独立的 OMS 代理
有关如何将代理配置为使用代理服务器（在本例中为网关）的信息，请参阅[使用 Log Analytics 从环境中的计算机收集数据](log-analytics-concept-hybrid.md#prerequisites)。 如果在网络负载均衡器后面部署了多个网关服务器，OMS 代理的代理配置为 NLB 的虚拟 IP 地址：

![Microsoft Monitoring Agent 属性 – 代理设置](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager-all-agents-use-the-same-proxy-server"></a>配置 Operations Manager：所有代理使用相同的代理服务器
可以配置 Operations Manager 以添加网关服务器。  即使设置为空，Operations Manager 代理配置也会自动应用到向 Operations Manager 报告的所有代理。  

若要使用 OMS 网关来支持 Operations Manager，必须安装以下组件：

* 在网关服务器上安装 Microsoft Monitoring Agent（代理版本 **8.0.10900.0** 或以上），并根据要通信的 Log Analytics 工作区配置该代理。

* 该网关必须已建立 Internet 连接，或者已连接到可访问 Internet 的代理服务器。

> [!NOTE]
> 如果未指定网关的值，会将空值推送到所有代理。
> 

如果这是首次向 Log Analytics 工作区注册 Operations Manager 管理组，为管理组指定代理配置的选项在操作控制台中不可用。 

必须成功向服务注册管理组后，此选项才可用。 在运行操作控制台以及管理组中所有管理服务器的同一系统上使用 Netsh 更新系统代理配置。

1. 打开权限提升的命令提示符。

    a. 转到“开始”。 然后键入 **cmd**。
    
    b. 右键单击“命令提示符”。 然后选择“以管理员身份运行”。
    
2. 输入以下命令并按 **Enter**：

    `netsh winhttp set proxy <proxy>:<port>`

完成与 Log Analytics 的集成后，可运行 `netsh winhttp reset proxy` 删除更改。 然后使用操作控制台中的“配置代理服务器”选项来指定 OMS 网关服务器。 

1. 打开 Operations Manager 控制台。 在“Operations Management Suite”下面选择“连接”。 然后选择“配置代理服务器”。

    ![Operations Manager – 配置代理服务器](./media/log-analytics-oms-gateway/scom01.png)

2. 选择“使用代理服务器访问 Operations Management Suite”。 键入 OMS 网关服务器的 IP 地址或 NLB 的虚拟 IP 地址。 请务必首先键入 `http://` 前缀。

    ![Operations Manager – 代理服务器地址](./media/log-analytics-oms-gateway/scom02.png)

3. 选择“完成”。 Operations Manager 管理组现已配置为通过网关服务器与 Log Analytics 服务通信。

### <a name="configure-operations-manager-specific-agents-use-proxy-server"></a>配置 Operations Manager：特定的代理使用代理服务器
在大型或复杂环境中，可能只希望特定的服务器（或组）使用 OMS 网关服务器。 对于这些服务器，无法直接更新 Operations Manager 代理，因为此值会被管理组的全局值覆盖。 应该重写用于推送这些值的规则。  

> [!NOTE] 
> 可以利用与此相同的配置方法在环境中使用多个 OMS 网关服务器。 例如，可能会要求为每个区域指定特定的 OMS 网关服务器。
>  

1. 打开 Operations Manager 控制台并选择“创作”工作区。

2. 在“创作”工作区中选择“规则”。 然后选择 Operations Manager 工具栏上的“范围”按钮。 如果此按钮不可用，请确保已在“监视”窗格中选择了一个对象而不是文件夹。 “范围管理包对象”对话框显示了通用目标类、组或对象的列表。 

3. 在“查找”字段中键入“运行状况服务”。 然后从列表中选择该服务。 选择“确定”。  

4. 搜索规则“顾问代理设置规则”。 在操作控制台工具栏中选择“重写”。 然后选择“重写规则\针对类: Health Service 的特定对象”。 

5. 接下来，从列表中选择特定的对象。 

6. （可选）创建一个自定义组并在其中包含要将此重写应用到的服务器的运行状况服务对象。 然后，可向该组应用该重写。

7. 在“重写属性”对话框中，勾选“WebProxyAddress”参数旁边的“重写”列。  在“重写值”字段中，输入 OMS 网关服务器的 URL，请确保以 `http://` 前缀开头。  

    >[!NOTE]
    > 不需要启用该规则。 面向 Microsoft System Center 顾问监视服务器组的 Microsoft System Center 顾问安全引用重写管理包中包含的某个重写会自动管理该规则。
    >   

8. 从“选择目标管理包”列表中选择一个管理包，或选择“新建”创建新的未密封管理包。 

9. 完成更改后，选择“确定”。 

### <a name="configure-the-oms-gateway-for-automation-hybrid-runbook-workers"></a>为自动化混合 Runbook 辅助角色配置 OMS 网关
如果在环境中使用自动化混合 Runbook 辅助角色，可遵循以下步骤，通过手动的临时解决方法将 OMS 网关配置为支持这些角色。

执行以下步骤时，需要知道自动化帐户所在的 Azure 区域。 若要查找位置，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择 Azure 自动化服务。
3. 选择相应的 Azure 自动化帐户。
4. 在“位置”下面，查看该帐户所在的区域。

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
| 北欧 |ne-jobruntimedata-prod-su1.azure-automation.net |
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
| 北欧 |ne-agentservice-prod-1.azure-automation.net |
| 东南亚 |sea-agentservice-prod-1.azure-automation.net |
| 印度中部 |cid-agentservice-prod-1.azure-automation.net |
| 日本 |jpe-agentservice-prod-1.azure-automation.net |
| 澳大利亚 |ase-agentservice-prod-1.azure-automation.net |

如果计算机已自动注册为混合 Runbook 辅助角色以便使用更新管理解决方案进行修补，请遵循以下步骤：

1. 将作业运行时数据服务 URL 添加到 OMS 网关上的“允许的主机”列表。 例如，键入以下内容：`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

2. 使用以下 PowerShell cmdlet 重新启动 OMS 网关服务：`Restart-Service OMSGatewayService`

如果已使用混合 Runbook 辅助角色注册 cmdlet 将计算机加入 Azure 自动化，请遵循以下步骤：

1. 将代理服务注册 URL 添加到 OMS 网关上的“允许的主机”列表。 例如，键入以下内容：`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`

2. 将作业运行时数据服务 URL 添加到 OMS 网关上的“允许的主机”列表。 例如，键入以下内容：`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

3. 重启 OMS 网关服务：`Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>有用的 PowerShell cmdlet
可以借助 cmdlet 来完成更新 OMS 网关配置设置所要执行的任务。 在使用这些这些 cmdlet 之前，请确保执行以下步骤：

1. 安装 OMS 网关 (MSI)。
2. 打开 PowerShell 控制台窗口。
3. 键入以下命令导入模块：`Import-Module OMSGateway`
4. 如果上一步骤未发生错误，则表示该模块已成功导入，可以使用 cmdlet。 键入 `Get-Module OMSGateway`。
5. 使用 cmdlet 做出更改后，请务必重启 OMS 网关服务。

如果步骤 3 出错，则表示该模块未导入。 出错的原因可能是 PowerShell 找不到模块。 可在 OMS 网关的安装路径中找到该模块：*C:\Program Files\Microsoft OMS Gateway\PowerShell*。

| **Cmdlet** | **参数** | **说明** | **示例** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |密钥 |获取服务的配置 |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |密钥（必需） <br> 值 |更改服务的配置 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |获取中继（上游）代理的地址 |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |地址<br> 用户名<br> 密码 |设置中继（上游）代理的地址（和凭据） |1.设置中继代理和凭据：<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2.设置不需要身份验证的中继代理：`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3.清除中继代理设置：<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |获取当前允许的主机（仅限本地配置的允许主机；不包括自动下载的允许主机） |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |主机（必需） |将主机添加到允许列表 |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |主机（必需） |从允许列表中删除主机 |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |使用者（必需） |将客户端证书使用者添加到允许列表 |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |使用者（必需） |从允许列表中删除客户端证书使用者 |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |获取当前允许的客户端证书使用者（仅限本地配置的允许使用者；不包括自动下载的允许使用者） |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>故障排除
若要收集网关记录的事件，还需要安装 OMS 代理。

![事件查看器 - OMS 网关日志](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS 网关事件 ID 和说明**

下表显示了 OMS 网关日志事件的事件 ID 和说明：

| **ID** | **说明** |
| --- | --- |
| 400 |没有特定 ID 的任何应用程序错误。 |
| 401 |配置错误。 例如：为 listenPort 提供的值是“文本”而不是整数。 |
| 402 |分析 TLS 握手消息时发生异常。 |
| 403 |网络错误。 例如：无法连接到目标服务器。 |
| 100 |常规信息。 |
| 101 |服务已启动。 |
| 102 |服务已停止。 |
| 103 |已从客户端收到 HTTP CONNECT 命令。 |
| 104 |不是 HTTP CONNECT 命令。 |
| 105 |目标服务器不在允许列表中，或者目标端口不是安全端口 (443)。 <br> <br> 请确保网关服务器上的 MMA 代理以及与网关通信的代理已连接到同一个 Log Analytics 工作区。 |
| 105 |ERROR TcpConnection – 客户端证书无效: CN=网关 <br><br> 请确保： <br>    <br> - 使用版本号为 1.0.395.0 或更高的网关。 <br> - 网关服务器上的 MMA 代理以及与网关通信的代理已连接到同一个 Log Analytics 工作区。 |
| 106 |OMS 网关仅支持 TLS 1.0、TLS 1.1 和 1.2。  它不支持 SSL。 对于任何不支持的 TLS/SSL 协议版本，OMS 网关将生成事件 ID 106。|
| 107 |已验证 TLS 会话。 |

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
登录到 Azure 门户后，可以针对 OMS 网关或其他任何 Azure 服务或服务的功能创建支持请求。

若要请求帮助，请选择门户右上角的问号。 然后选择“新建支持请求”。 填写新建支持请求的表单。

![新建支持请求](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>后续步骤
[添加数据源](log-analytics-data-sources.md)以便从连接的源中收集数据并将其存储在 Log Analytics 工作区中。
