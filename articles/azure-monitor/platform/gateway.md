---
title: 使用 Log Analytics 网关连接计算机 |Microsoft Docs
description: 使用 Log Analytics 网关连接设备和 Operations Manager 监视的计算机，以便在无法访问 Internet 时将数据发送到 Azure 自动化和 Log Analytics 服务。
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
ms.date: 08/12/2019
ms.author: magoedte
ms.openlocfilehash: 1d735a3740b473806835f2e80f40cea02b48387e
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955095"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>在 Azure Monitor 中使用 Log Analytics 网关连接无法访问 Internet 的计算机

>[!NOTE]
>随着 Microsoft Operations Management Suite (OMS) 转换到 Microsoft Azure 监视器, 术语也在不断变化。 本文将 OMS 网关称作 Azure Log Analytics 网关。 
>

本文介绍当直接连接的计算机或者受 Operations Manager 监视的计算机无法访问 Internet 时，如何使用 Log Analytics 网关来配置与 Azure 自动化和 Azure Monitor 的通信。 

Log Analytics 网关是使用 HTTP CONNECT 命令支持 HTTP 隧道的 HTTP 转发代理。 此网关代表无法直接连接到 internet 的计算机, 将数据发送到 Azure 自动化和 Azure Monitor 中的 Log Analytics 工作区。 它不会缓存代理中的数据, 代理会在此情况下处理缓存数据, 直到通信恢复。

Log Analytics 网关支持：

* 向其后面的以及配置了 Azure 自动化混合 Runbook 辅助角色的最多四个（相同）Log Analytics 工作区代理发送报告。  
* Microsoft Monitoring Agent 在其上直接连接到 Azure Monitor 中的 Log Analytics 工作区的 Windows 计算机。
* 适用于 Linux 的 Log Analytics 代理在其上直接连接到 Azure Monitor 中的 Log Analytics 工作区的 Linux 计算机。  
* 与 Log Analytics 集成的 System Center Operations Manager 2012 SP1 UR7、Operations Manager 2012 R2 UR3 或 Operations Manager 2016 或更高版本中的管理组。  

某些 IT 安全策略不允许网络计算机连接到 Internet。 例如，这些未连接的计算机可能是销售点 (POS) 设备或支持 IT 服务的服务器。 若要将这些设备连接到 Azure 自动化或 Log Analytics 工作区以便对其进行管理和监视，可将其配置为直接与 Log Analytics 网关通信。 Log Analytics 网关可以接收配置信息并代表这些设备转发数据。 如果这些计算机上配置了可直接连接到 Log Analytics 工作区的 Log Analytics 代理，则它们将改为与 Log Analytics 网关通信。  

Log Analytics 网关直接将数据从代理传输到服务。 它不会分析传输中的任何数据。

如果已将 Operations Manager 管理组与 Log Analytics 集成，可将管理服务器配置为连接到 Log Analytics 网关，以根据启用的解决方案接收配置信息并发送收集的数据。  Operations Manager 代理向管理服务器发送一些数据。 例如，代理可能会发送 Operations Manager 警报、配置评估数据、实例空间数据和容量数据。 其他大批量的数据（例如 Internet Information Services (IIS) 日志、性能数据和安全事件）将直接发送到 Log Analytics 网关。 

如果在外围网络或隔离网络中部署了一个或多个 Operations Manager 网关服务器用于监视不受信任的系统，这些服务器无法与 Log Analytics 网关通信。  Operations Manager 网关服务器只能向管理服务器报告。  如果将 Operations Manager 管理组配置为与 Log Analytics 网关通信，代理配置信息会自动分发到代理管理的、配置为收集 Azure Monitor 日志数据的每台计算机，即使设置为空。

若要为通过网关与 Log Analytics 工作区通信的直接连接的组或 Operations Management 组提供高可用性，可以使用网络负载均衡 (NLB) 在多个网关服务器之间重定向和分配流量。 这样，如果一台网关服务器发生故障，流量将重定向到另一个可用节点。  

运行 Log Analytics 网关的计算机要求使用 Log Analytics Windows 代理来标识需要与该网关通信的服务终结点。 此外，该代理还需要指示网关向网关后的代理或 Operations Manager 管理组中配置的相同工作区报告。 此配置允许网关和代理与其分配的工作区通信。

一个网关最多可以多重驻留到四个工作区。 这是 Windows 代理支持的工作区总数。  

每个代理必须与网关建立网络连接，这样，代理才能自动与网关相互传输数据。 请避免在域控制器上安装网关。

下图显示了如何通过网关将数据从直接代理传送到 Azure 自动化和 Log Analytics。 代理配置必须与 Log Analytics 网关配置的端口相匹配。  

![直接代理与服务通信的示意图](./media/gateway/oms-omsgateway-agentdirectconnect.png)

下图显示了数据如何从 Operations Manager 管理组流向 Log Analytics。   

![Operations Manager 与 Log Analytics 通信的示意图](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>设置系统

指定为运行 Log Analytics 网关的计算机必须采用以下配置：

* Windows 10、Windows 8.1 或 Windows 7
* Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 或 Windows Server 2008
* Microsoft .NET Framework 4.5
* 4 核处理器，8 GB 内存（最低要求） 
* 配置的[适用于 Windows 的 Log Analytics 代理](agent-windows.md)可以在代理通过网关通信时向同一工作区报告。

### <a name="language-availability"></a>语言可用性

Log Analytics 网关支持以下语言：

- 简体中文
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

Log Analytics 网关仅支持传输层安全性 (TLS) 1.0、1.1 和 1.2。  它不支持安全套接字层 (SSL)。  为了确保传输到 Log Analytics 的数据的安全性，请将网关配置为至少使用 TLS 1.2。 旧版 TLS 或 SSL 容易受到攻击。 尽管这些协议目前允许后向兼容，但请勿使用。  

有关其他信息，请查看[使用 TLS 1.2 安全地发送数据](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)。 

### <a name="supported-number-of-agent-connections"></a>支持的代理连接数

下表显示了可与一个网关服务器通信的大致代理数量。 此项支持基于代理每 6 秒上传约 200 KB 数据。 对于测试的每个代理，数据量约为每天 2.7 GB。

|网关 |支持的代理数（近似）|  
|--------|----------------------------------|  
|CPU:Intel Xeon 处理器 E5-2660 v3 \@ 2.6 GHz 双核<br> 内存:4 GB<br> 网络带宽：1 Gbps| 600|  
|CPU:Intel Xeon 处理器 E5-2660 v3 \@ 2.6 GHz 4 核<br> 内存:8 GB<br> 网络带宽：1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>下载 Log Analytics 网关

从  [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=54443)或 Azure 门户获取最新版本的 Log Analytics 网关安装程序文件。

若要从 Azure 门户获取 Log Analytics 网关，请执行以下步骤：

1. 浏览服务列表，并选择“Log Analytics”。 
1. 选择工作区。
1. 在工作区边栏选项卡中的“常规”下面，选择“快速启动”。 
1. 在“选择要连接到工作区的数据源”下面，选择“计算机”。
1. 在“直接代理”边栏选项卡中，选择“下载 Log Analytics 网关”。
 
   ![下载 Log Analytics 网关的步骤屏幕截图](./media/gateway/download-gateway.png)

或 

1. 在工作区边栏选项卡中的“设置”下方，选择“高级设置”。
1. 转到“连接的源” > “Windows 服务器”，然后选择“下载 Log Analytics 网关”。

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>使用安装向导安装 Log Analytics 网关

若要使用安装向导安装网关，请执行以下步骤。 

1. 在目标文件夹中，双击“Log Analytics gateway.msi”。
1. 在“欢迎”页上，选择“下一步”。

   ![网关安装向导中的“欢迎”页屏幕截图](./media/gateway/gateway-wizard01.png)

1. 在“许可协议”页上，选择“我接受许可协议中的条款”表示同意 Microsoft 软件许可条款，然后选择“下一步”。
1. 在“端口和代理地址”页上执行以下操作：

   a. 输入网关使用的 TCP 端口号。 安装程序会使用此端口号在 Windows 防火墙中配置一个入站规则。  默认值为 8080。
      端口号的有效范围为 1 至 65535。 如果输入的端口号不在此范围内，会出现一条错误消息。

   b. 如果安装网关的服务器需要通过代理通信，请输入网关需要连接到的代理地址， 例如，输入 `http://myorgname.corp.contoso.com:80`。  如果将此地址留空，网关将尝试直接连接到 Internet。  如果代理服务器要求身份验证，请输入用户名和密码。

   c. 选择“**下一步**”。

   ![网关代理配置的屏幕截图](./media/gateway/gateway-wizard02.png)

1. 如果尚未启用 Microsoft 更新，会显示“Microsoft 更新”页，可以在其中选择启用 Microsoft 更新。 做出选择，并选择“下一步”。 否则，继续执行下一步。
1. 在“目标文件夹”页上，保留默认文件夹 C:\Program Files\OMS Gateway，或输入网关的安装位置。 然后，选择“下一步”。
1. 在“准备安装”页上，选择“安装”。 如果用户帐户控制请求提供安装权限，请选择“是”。
1. 安装完成后，选择“完成”。 若要验证该服务是否正在运行，请打开 services.msc 管理单元，并检查服务列表中是否出现“OMS 网关”并且其状态为“正在运行”。

   ![本地服务的屏幕截图，其中显示 OMS 网关正在运行](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>使用命令行安装 Log Analytics 网关
网关的下载文件是一个 Windows Installer 包，它支持通过命令行或其他自动化方法以无提示方式完成安装。 如果你不熟悉 Windows Installer 的标准命令行选项，请参阅[命令行选项](https://docs.microsoft.com/windows/desktop/Msi/command-line-options)。
 
下表突出显示了安装程序支持的参数。

|Parameters| 说明|
|----------|------| 
|PORTNUMBER | 网关侦听的 TCP 端口号 |
|PROXY | 代理服务器的 IP 地址 |
|INSTALLDIR | 完全限定的路径，用于指定网关软件文件的安装目录 |
|USERNAME | 用于在代理服务器中进行身份验证的用户 ID |
|PASSWORD | 用于在代理中进行身份验证的用户 ID 的密码 |
|LicenseAccepted | 指定值 **1** 表示确认接受许可协议 |
|HASAUTH | 指定 USERNAME/PASSWORD 参数时请指定值 **1** |
|HASPROXY | 指定 **PROXY** 参数的 IP 地址时请指定值 **1** |

若要以无提示方式安装网关并使用特定的代理地址、端口号对其进行配置，请键入以下命令：

```dos
Msiexec.exe /I “oms gateway.msi” /qn PORTNUMBER=8080 PROXY=”10.80.2.200” HASPROXY=1 LicenseAccepted=1 
```

使用 /qn 命令行选项会在无提示安装期间隐藏安装程序，而使用 /qb 则会显示安装程序。  

如果需要提供用于在代理服务器中进行身份验证的凭据，请键入以下命令：

```dos
Msiexec.exe /I “oms gateway.msi” /qn PORTNUMBER=8080 PROXY=”10.80.2.200” HASPROXY=1 HASAUTH=1 USERNAME=”<username>” PASSWORD=”<password>” LicenseAccepted=1 
```

安装完成后，可使用以下 PowerShell cmdlet 确认接受设置（不包括用户名和密码）：

- **Get-OMSGatewayConfig** – 返回网关配置为侦听的 TCP 端口。
- **Get-OMSGatewayRelayProxy** – 返回配置为与网关通信的代理服务器的 IP 地址。

## <a name="configure-network-load-balancing"></a>配置网络负载均衡 
你可以使用 Microsoft[网络负载平衡 (nlb)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)、 [Azure 负载均衡器](../../load-balancer/load-balancer-overview.md)或基于硬件的负载均衡器来配置使用网络负载平衡 (nlb) 的高可用性网关。 负载均衡器通过在其节点之间重定向 Log Analytics 代理或 Operations Manager 管理服务器请求的连接来管理流量。 如果一台网关服务器出现故障，流量将重定向到其他节点。

### <a name="microsoft-network-load-balancing"></a>Microsoft 网络负载均衡
若要了解如何设计和部署 Windows Server 2016 网络负载均衡群集，请参阅[网络负载均衡](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)。 以下步骤介绍如何配置 Microsoft 网络负载均衡群集。  

1. 使用管理帐户登录到属于 NLB 群集的 Windows 服务器。  
2. 在服务器管理器中打开网络负载均衡管理器，单击“工具”，并单击“网络负载均衡管理器”。
3. 要连接装有 Microsoft Monitoring Agent 的 Log Analytics 网关服务器，请右键单击群集的 IP 地址，然后单击“将主机添加到群集”。 

    ![网络负载均衡管理器 – 将主机添加到群集](./media/gateway/nlb02.png)
 
4. 输入要连接的网关服务器的 IP 地址。 

    ![网络负载均衡管理器 – 将主机添加到群集：连接](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure 负载均衡器
若要了解如何设计和部署 Azure 负载均衡器，请参阅[什么是 Azure 负载均衡器？](../../load-balancer/load-balancer-overview.md)。 若要部署基本负载均衡器，请遵循此[快速入门](../../load-balancer/quickstart-create-basic-load-balancer-portal.md)中所述的步骤，但不要遵循“创建后端服务器”部分所述的步骤。   

> [!NOTE]
> 使用**基本 SKU** 配置 Azure 负载均衡器需要 Azure 虚拟机属于某个可用性集。 若要详细了解可用性集，请参阅[在 Azure 中管理 Windows 虚拟机的可用性](../../virtual-machines/windows/manage-availability.md)。 若要将现有的虚拟机添加到可用性集，请参阅[设置 Azure 资源管理器 VM 可用性集](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)。
> 

创建负载均衡器后，需要创建一个后端池，以便将流量分发到一个或多个网关服务器。 请遵循该快速入门文章的[为负载均衡器创建资源](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer)部分所述的步骤。  

>[!NOTE]
>配置运行状况探测时，应将它配置为使用网关服务器的 TCP 端口。 运行状况探测器基于其对运行状况检查的响应，从负载均衡器中动态添加或删除网关服务器。 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>配置 Log Analytics 代理和 Operations Manager 管理组
本部分介绍有关如何使用 Log Analytics 网关配置直接连接的 Log Analytics 代理、Operations Manager 管理组或 Azure 自动化混合 Runbook 辅助角色，使其与 Azure 自动化或 Log Analytics 通信的步骤。  

### <a name="configure-a-standalone-log-analytics-agent"></a>配置独立的 Log Analytics 代理
配置 Log Analytics 代理时，请将代理服务器值替换为 Log Analytics 网关服务器的 IP 地址及其端口号。 如果在负载均衡器后面部署了多个网关服务器，Log Analytics 代理的代理配置为该负载均衡器的虚拟 IP 地址。  

>[!NOTE]
>若要在网关和直接连接到 Log Analytics 的 Windows 计算机上安装 Log Analytics 代理，请参阅[将 Windows 计算机连接到 Azure 中的 Log Analytics 服务](agent-windows.md)。 若要连接 Linux 计算机，请参阅[在混合环境中为 Linux 计算机配置 Log Analytics 代理](../../azure-monitor/learn/quick-collect-linux-computer.md)。 
>

在网关服务器上安装代理后，请将其配置为向与网关通信的工作区或工作区代理报告。 如果未在网关上安装 Log Analytics Windows 代理，则会将事件 300 写入 OMS 网关事件日志，指示需要安装代理。 如果代理已安装，但未配置为在代理通过它通信时向同一工作区报告，则会将事件 105 写入相同的日志，指出需要将网关上的代理配置为在代理与网关通信时，向同一工作区报告。

完成配置后，重启 OMS 网关服务以应用所做的更改。 否则，网关将拒绝代理尝试与 Log Analytics 通信，并在 OMS 网关事件日志中报告事件 105。 在网关服务器上的代理配置中添加或删除工作区时，同样会发生这种情况。   

有关自动化混合 Runbook 辅助角色的信息, 请参阅[使用混合 Runbook 辅助角色自动执行数据中心或云中的资源](../../automation/automation-hybrid-runbook-worker.md)。

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>配置 Operations Manager，其中的所有代理使用相同的代理服务器

即使设置为空，Operations Manager 代理配置也会自动应用到向 Operations Manager 报告的所有代理。  

若要使用 OMS 网关来支持 Operations Manager，必须满足以下条件：

* 已在 OMS 网关服务器上安装 Microsoft Monitoring Agent（版本 8.0.10900.0 或以上），并在其中配置了相同的 Log Analytics 工作区，管理组已配置为向这些工作区报告。
* Internet 连接。 或者，OMS 网关必须已连接到与 Internet 连接的代理服务器。

> [!NOTE]
> 如果未指定网关的值，会将空值推送到所有代理。
>

首次向 Log Analytics 工作区注册 Operations Manager 管理组时，操作控制台中不会显示为管理组指定代理配置的选项。 只有在成功向服务注册管理组之后，此选项才可用。  

若要配置集成，请在运行操作控制台的系统上，以及管理组中的所有管理服务器上，使用 Netsh 更新系统代理配置。 请执行以下步骤：

1. 打开权限提升的命令提示符：

   a. 选择“启动”并输入 **cmd**。  

   b. 右键单击“命令提示符”并选择“以管理员身份运行”。  

1. 输入以下命令：

   `netsh winhttp set proxy <proxy>:<port>`

完成与 Log Analytics 的集成后，请运行 `netsh winhttp reset proxy` 删除更改。 然后在操作控制台中，使用“配置代理服务器”选项来指定 Log Analytics 网关服务器。 

1. 在 Operations Manager 控制台的“Operations Management Suite”下面，依次选择“连接”、“配置代理服务器”。

   ![Operations Manager 的屏幕截图，其中显示了“配置代理服务器”选项](./media/gateway/scom01.png)

1. 选择“使用代理服务器访问 Operations Management Suite”，然后输入 Log Analytics 网关服务器的 IP 地址或负载均衡器的虚拟 IP 地址。 请注意需要以前缀 `http://` 开头。

   ![Operations Manager 的屏幕截图，其中显示了代理服务器地址](./media/gateway/scom02.png)

1. 选择“完成”。 Operations Manager 管理组现已配置为通过网关服务器与 Log Analytics 服务通信。

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>配置 Operations Manager，其中的特定代理使用代理服务器

在大型或复杂环境中，你可能只希望特定的服务器（或组）使用 Log Analytics 网关服务器。  对于这些服务器，无法直接更新 Operations Manager 代理，因为此值会被管理组的全局值覆盖。  应该重写用于推送这些值的规则。  

> [!NOTE] 
> 若要在环境中允许使用多个 Log Analytics 网关服务器，请使用此配置方法。  例如，你可能要求按区域指定特定的 Log Analytics 网关服务器。
>

若要将特定的服务器或组配置为使用 Log Analytics 网关服务器： 

1. 打开 Operations Manager 控制台并选择“创作”工作区。  
1. 在“创作”工作区中选择“规则”。 
1. 在 Operations Manager 工具栏上，选择“范围”按钮。 如果此按钮不可用，请确保已在“监视”窗格中选择了一个对象而不是文件夹。 “范围管理包对象”对话框显示了通用目标类、组或对象的列表。 
1. 在“查找”字段中输入“运行状况服务”，并从列表中选择该服务。 选择“确定”。  
1. 搜索“顾问代理设置规则”。 
1. 在 Operations Manager 工具栏上选择“重写”，指向“重写规则\针对类的特定对象:运行状况服务”，然后从列表中选择一个对象。  或者，创建一个自定义组并在其中包含要将此重写应用到的服务器的运行状况服务对象。 然后应用对自定义组的重写。
1. 在“重写属性”对话框中，勾选“WebProxyAddress”参数旁边的“重写”列。  在“重写值”字段中，输入 Log Analytics 网关服务器的 URL。 请注意需要以前缀 `http://` 开头。  

    >[!NOTE]
    > 不需要启用该规则。 面向 Microsoft System Center 顾问监视服务器组的 Microsoft System Center 顾问安全引用重写管理包中的某个重写会自动管理该规则。
    > 

1. 从“选择目标管理包”列表中选择一个管理包，或选择“新建”创建新的未密封管理包。 
1. 完成后，选择“确定”。 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>配置自动化混合 Runbook 辅助角色

如果在环境中使用自动化混合 Runbook 辅助角色，请遵循以下步骤，通过手动的临时解决方法将 OMS 网关配置为支持这些辅助角色。

若要遵循本部分所述的步骤，需要知道自动化帐户所在的 Azure 区域。 若要查找该位置：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 选择 Azure 自动化服务。
1. 选择相应的 Azure 自动化帐户。
1. 在“位置”下面即可查看该帐户所在的区域。

   ![Azure 门户中自动化帐户位置的屏幕截图](./media/gateway/location.png)

使用下表识别每个位置的 URL。

**作业运行时数据服务 Url**

| **Location** | **URL** |
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

| **Location** | **URL** |
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

如果计算机已自动注册为混合 Runbook 辅助角色，请使用“更新管理”解决方案来管理修补程序。 请执行以下步骤：

1. 将作业运行时数据服务 URL 添加到 Log Analytics 网关上的“允许的主机”列表。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. 使用以下 PowerShell cmdlet 重新启动 Log Analytics 网关服务：`Restart-Service OMSGatewayService`

如果已使用混合 Runbook 辅助角色注册 cmdlet 将计算机加入 Azure 自动化，请遵循以下步骤：

1. 将代理服务注册 URL 添加到 Log Analytics 网关上的“允许的主机”列表。 例如： `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. 将作业运行时数据服务 URL 添加到 Log Analytics 网关上的“允许的主机”列表。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. 重新启动 Log Analytics 网关服务。
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>有用的 PowerShell cmdlet

可以使用 cmdlet 来完成更新 Log Analytics 网关配置设置的任务。 在使用 cmdlet 之前，请确保：

1. 安装 Log Analytics 网关（Microsoft Windows Installer）。
1. 打开 PowerShell 控制台窗口。
1. 键入以下命令导入模块：`Import-Module OMSGateway`
1. 如果上一步骤未发生错误，则表示该模块已成功导入，可以使用 cmdlet。 输入 `Get-Module OMSGateway`
1. 使用 cmdlet 做出更改后，重启 OMS 网关服务。

如果步骤 3 出错，则表示该模块未导入。 如果 PowerShell 找不到该模块，则可能会发生此错误。 可以在 OMS 网关安装路径中找到该模块：*C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*。

| **Cmdlet** | **参数** | **说明** | **示例** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Key |获取服务的配置 |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |密钥（必需） <br> ReplTest1 |更改服务的配置 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |获取中继（上游）代理的地址 |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |地址<br> 用户名<br> 密码 |设置中继（上游）代理的地址（和凭据） |1.设置中继代理和凭据：<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2.设置不需要身份验证的中继代理：`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3.清除中继代理设置：<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |获取当前允许的主机（仅限本地配置的允许主机，而不是自动下载的允许主机） |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |主机（必需） |将主机添加到允许列表 |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |主机（必需） |从允许列表中删除主机 |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |使用者（必需） |将客户端证书使用者添加到允许列表 |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |使用者（必需） |从允许列表中删除客户端证书使用者 |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |获取当前允许的客户端证书使用者（仅限本地配置的允许使用者，而不是自动下载的允许使用者） |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>疑难解答

若要收集网关记录的事件，应该安装 Log Analytics 代理。

![Log Analytics 网关日志中“事件查看器”列表的屏幕截图](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Log Analytics 网关事件 ID 和说明

下表显示 Log Analytics 的网关日志事件的事件 Id 和说明。

| **ID** | **说明** |
| --- | --- |
| 400 |未提供特定 ID 的任何应用程序错误。 |
| 401 |配置错误。 例如，为 listenPort 提供的值是“文本”而不是整数。 |
| 402 |分析 TLS 握手消息时发生异常。 |
| 403 |网络错误。 例如，无法连接到目标服务器。 |
| 100 |常规信息。 |
| 101 |服务已启动。 |
| 102 |服务已停止。 |
| 103 |已从客户端收到 HTTP CONNECT 命令。 |
| 104 |不是 HTTP CONNECT 命令。 |
| 105 |目标服务器不在允许列表中，或者目标端口不是安全端口 (443)。 <br> <br> 请确保 OMS 网关服务器上的 MMA 代理以及与 OMS 网关通信的代理已连接到同一个 Log Analytics 工作区。 |
| 105 |ERROR TcpConnection – 客户端证书无效：CN=Gateway。 <br><br> 确保使用 OMS 网关版本 1.0.395.0 或更高版本。 另请确保 OMS 网关服务器上的 MMA 代理以及与 OMS 网关通信的代理已连接到同一个 Log Analytics 工作区。 |
| 106 |不支持的 TLS/SSL 协议版本。<br><br> Log Analytics 网关仅支持 TLS 1.0、TLS 1.1 和 1.2。 它不支持 SSL。|
| 107 |已验证 TLS 会话。 |

### <a name="performance-counters-to-collect"></a>要收集的性能计数器

下表显示了 Log Analytics 网关可用的性能计数器。 使用性能监视器来添加计数器。

| **名称** | **说明** |
| --- | --- |
| Log Analytics 网关/活动客户端连接 |活动客户端网络 (TCP) 连接数 |
| Log Analytics 网关/错误计数 |错误数 |
| Log Analytics 网关/连接的客户端 |连接的客户端数 |
| Log Analytics 网关/拒绝计数 |由于发生任何 TLS 验证错误而拒绝的数目 |

![Log Analytics 网关接口的屏幕截图，其中显示了性能计数器](./media/gateway/counters.png)

## <a name="assistance"></a>帮助

登录到 Azure 门户后，可以获取 Log Analytics 网关或其他任何 Azure 服务或功能的帮助。
若要取得帮助，请选择门户右上角的问号图标，然后选择“新建支持请求”。 接下来，填写新建支持请求的表单。

![新支持请求的屏幕截图](./media/gateway/support.png)

## <a name="next-steps"></a>后续步骤

[添加数据源](../../azure-monitor/platform/agent-data-sources.md)以便从连接的源中收集数据，并将数据存储在 Log Analytics 工作区中。