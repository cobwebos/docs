---
title: 使用 Log Analytics 网关连接计算机 |Microsoft Docs
description: 使用 Log Analytics 的网关将数据发送到 Azure 自动化，并 Operations Manager 监视的计算机，以便在无法访问 internet 时将数据发送到 Azure 自动化和 Log Analytics 服务。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/24/2019
ms.openlocfilehash: ba0ee29b48be259bddd898c3d1119b77f6ee5228
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932303"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>使用 Azure Monitor 中的 Log Analytics 网关连接无 internet 访问的计算机

>[!NOTE]
>随着 Microsoft Operations Management Suite （OMS）转换到 Microsoft Azure 监视器，术语也在不断变化。 本文将 OMS 网关称为 Azure Log Analytics 网关。 
>

本文介绍了如何使用 Log Analytics 网关配置与 Azure 自动化的通信，以及通过使用网关 Azure Monitor Operations Manager 不具有 internet 访问权限的计算机。 

Log Analytics 网关是使用 HTTP CONNECT 命令支持 HTTP 隧道的 HTTP 转发代理。 此网关代表无法直接连接到 internet 的计算机，将数据发送到 Azure 自动化和 Azure Monitor 中的 Log Analytics 工作区。 它不会缓存代理中的数据，代理会在此情况下处理缓存数据，直到通信恢复。

Log Analytics 网关支持：

* 向其后面的同四个 Log Analytics 工作区代理进行报告，并将其配置为 Azure 自动化混合 Runbook 辅助角色。  
* Microsoft Monitoring Agent 直接连接到 Azure Monitor 中 Log Analytics 工作区的 Windows 计算机。
* 适用于 Linux 的 Log Analytics 代理的 linux 计算机直接连接到 Azure Monitor 中的 Log Analytics 工作区。  
* System Center Operations Manager 2012 SP1 with UR7、Operations Manager 2012 R2 with UR3，或是 Operations Manager 2016 或更高版本中与 Log Analytics 集成的管理组。  

某些 IT 安全策略不允许网络计算机建立 internet 连接。 例如，这些未连接的计算机可能是销售点（POS）设备或支持 IT 服务的服务器。 若要将这些设备连接到 Azure 自动化或 Log Analytics 的工作区以便管理和监视这些设备，请将它们配置为直接与 Log Analytics 网关进行通信。 Log Analytics 网关可以接收配置信息并代表它们转发数据。 如果将计算机配置为使用 Log Analytics 代理直接连接到 Log Analytics 工作区，则计算机将改为与 Log Analytics 网关进行通信。  

Log Analytics 网关直接将数据从代理传输到服务。 它不会分析传输中的任何数据。

当 Operations Manager 管理组与 Log Analytics 集成时，可以将管理服务器配置为连接到 Log Analytics 网关，以根据已启用的解决方案接收配置信息并发送收集的数据.  Operations Manager 代理将一些数据发送到管理服务器。 例如，代理可以发送 Operations Manager 警报、配置评估数据、实例空间数据和容量数据。 其他大容量数据（如 Internet Information Services （IIS）日志、性能数据和安全事件）将直接发送到 Log Analytics 网关。 

如果部署一个或多个 Operations Manager 网关服务器来监视外围网络或隔离网络中的不受信任的系统，则这些服务器无法与 Log Analytics 的网关通信。  Operations Manager 网关服务器只能向管理服务器报告。  将 Operations Manager 管理组配置为与 Log Analytics 网关进行通信时，代理配置信息会自动分发到配置为收集 Azure Monitor 日志数据的每个代理管理的计算机。即使设置为空。

若要为通过网关与 Log Analytics 工作区进行通信的直接连接的组或 Operations Management 组提供高可用性，请使用网络负载平衡（NLB）在多个网关服务器之间重定向和分配流量。 这样一来，如果一个网关服务器发生故障，流量将重定向到另一个可用节点。  

运行 Log Analytics 网关的计算机需要 Log Analytics Windows 代理来识别网关需要与之通信的服务终结点。 代理还需要将网关定向到配置了网关后的代理或 Operations Manager 管理组的相同工作区。 此配置允许网关和代理与分配的工作区进行通信。

网关最多可为四个工作区。 这是 Windows 代理支持的工作区总数。  

每个代理都必须与网关建立网络连接，以便代理能够自动将数据传入和传出网关。 不要在域控制器上安装网关。

下图显示了通过网关从直接代理流向 Azure 自动化和 Log Analytics 的数据。 代理的代理配置必须与配置 Log Analytics 网关所用的端口相匹配。  

![直接代理与服务通信的示意图](./media/gateway/oms-omsgateway-agentdirectconnect.png)

下图显示了数据如何从 Operations Manager 管理组流向 Log Analytics。   

![与 Log Analytics Operations Manager 通信的示意图](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>设置系统

指定运行 Log Analytics 网关的计算机必须具有以下配置：

* Windows 10、Windows 8.1 或 Windows 7
* Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 或 Windows Server 2008
* Microsoft .NET Framework 4.5
* 至少4核处理器和 8 GB 内存 
* 一个[适用于 Windows 的 Log Analytics 代理](agent-windows.md)，该代理配置为与通过网关进行通信的代理报告到相同的工作区

### <a name="language-availability"></a>语言可用性

Log Analytics 网关提供以下语言版本：

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
- 韩语
- 波兰语
- 葡萄牙语（巴西）
- 葡萄牙语(葡萄牙)
- 俄语
- 西班牙语(国际)

### <a name="supported-encryption-protocols"></a>支持的加密协议

Log Analytics 网关仅支持传输层安全性（TLS）1.0、1.1 和1.2。  它不支持安全套接字层（SSL）。  若要确保传输中数据的安全性 Log Analytics，请将网关配置为至少使用 TLS 1.2。 较旧版本的 TLS 或 SSL 很容易受到攻击。 尽管它们目前允许向后兼容性，但避免使用它们。  

有关其他信息，请查看[使用 TLS 1.2 安全地发送数据](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)。 

### <a name="supported-number-of-agent-connections"></a>支持的代理连接数

下表显示了大约有多少代理可以与网关服务器通信。 支持基于每6秒上传大约 200 KB 数据的代理。 对于测试的每个代理，数据量约为每天 2.7 GB。

|网关 |支持的代理（大致）|  
|--------|----------------------------------|  
|CPU： Intel 强处理器 E5-2660 v3 \@ 2.6 GHz 2 核心<br> 内存： 4 GB<br> 网络带宽： 1 Gbps| 600|  
|CPU： Intel 强处理器 E5-2660 v3 \@ 2.6 GHz 4 核心<br> 内存： 8 GB<br> 网络带宽： 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>下载 Log Analytics 网关

从[Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=54443)或 Azure 门户获取 Log Analytics 网关安装程序文件的最新版本。

若要从 Azure 门户获取 Log Analytics 网关，请执行以下步骤：

1. 浏览服务列表，并选择“Log Analytics”。 
1. 选择工作区。
1. 在工作区边栏选项卡中的“常规”下面，选择“快速启动”。 
1. 在“选择要连接到工作区的数据源”下面，选择“计算机”。
1. 在 "**直接代理**" 边栏选项卡中，选择 "**下载 Log Analytics 网关**"。
 
   ![下载 Log Analytics 网关所要执行的步骤的屏幕截图](./media/gateway/download-gateway.png)

或 

1. 在工作区边栏选项卡中的“设置”下方，选择“高级设置”。
1. 在**Windows server** > 中转到 "**连接的源**"，然后选择 "**下载 Log Analytics 网关**"。

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>使用安装向导安装 Log Analytics 网关

若要使用安装向导安装网关，请执行以下步骤。 

1. 在目标文件夹中，双击“Log Analytics gateway.msi”。
1. 在“欢迎”页上，选择“下一步”。

   ![网关安装向导中 "欢迎" 页的屏幕截图](./media/gateway/gateway-wizard01.png)

1. 在 "**许可协议**" 页上，选择 **"我接受许可协议中的条款**以同意 Microsoft 软件许可条款"，然后选择 "**下一步**"。
1. 在“端口和代理地址”页上执行以下操作：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 输入要用于网关的 TCP 端口号。 安装程序使用此端口号在 Windows 防火墙上配置入站规则。  默认值为 8080。
      端口号的有效范围为1到65535。 如果输入的端口号不在此范围内，会出现一条错误消息。

   b. 如果安装网关的服务器需要通过代理进行通信，请输入网关需要连接到的代理地址。 例如，输入 `http://myorgname.corp.contoso.com:80`。  如果将此字段留空，网关将尝试直接连接到 internet。  如果代理服务器要求身份验证，请输入用户名和密码。

   c. 选择“**下一步**”。

   ![网关代理的配置屏幕截图](./media/gateway/gateway-wizard02.png)

1. 如果未启用 Microsoft 更新，则会出现 "Microsoft 更新" 页，您可以选择启用它。 做出选择，然后选择 "**下一步**"。 否则，继续执行下一步。
1. 在 "**目标文件夹**" 页上，保留默认文件夹 C:\Program files\oms gateway gateway 或输入要安装网关的位置。 然后，选择“下一步”。
1. 在“准备安装”页上，选择“安装”。 如果 "用户帐户控制" 请求安装权限，请选择 **"是"** 。
1. 安装完成后，选择 "**完成**"。 若要验证服务是否正在运行，请打开 "services.msc" 管理单元，并验证 " **OMS 网关**" 是否显示在服务列表中以及其状态是否为 "**正在运行**"。

   ![本地服务的屏幕截图，显示 OMS 网关正在运行](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>使用命令行安装 Log Analytics 网关
网关的下载文件是一个 Windows Installer 包，它支持从命令行或其他自动化方法进行无提示安装。 如果你不熟悉 Windows Installer 的标准命令行选项，请参阅[命令行选项](https://docs.microsoft.com/windows/desktop/Msi/command-line-options)。
 
下表突出显示了安装程序支持的参数。

|parameters| 说明|
|----------|------| 
|PORTNUMBER | 要侦听的网关的 TCP 端口号 |
|代理 | 代理服务器的 IP 地址 |
|INSTALLDIR | 指定网关软件文件的安装目录的完全限定路径 |
|用户名 | 用于向代理服务器进行身份验证的用户 Id |
|权限 | 用于向代理进行身份验证的用户 Id 密码 |
|LicenseAccepted | 指定值**1**以验证是否接受许可协议 |
|HASAUTH | 指定用户名/密码参数时，将值指定为**1** |
|HASPROXY | 指定**代理**参数的 IP 地址时，值为**1** |

若要以无提示方式安装网关并使用特定的代理地址和端口号进行配置，请键入以下内容：

```dos
Msiexec.exe /I “oms gateway.msi” /qn PORTNUMBER=8080 PROXY=”10.80.2.200” HASPROXY=1 LicenseAccepted=1 
```

使用/qn 命令行选项将隐藏安装程序，/qb 会在无提示安装过程中显示安装程序。  

如果需要提供凭据以便通过代理进行身份验证，请键入以下内容：

```dos
Msiexec.exe /I “oms gateway.msi” /qn PORTNUMBER=8080 PROXY=”10.80.2.200” HASPROXY=1 HASAUTH=1 USERNAME=”<username>” PASSWORD=”<password>” LicenseAccepted=1 
```

安装完成后，可以使用以下 PowerShell cmdlet 确认是否已接受设置（exlcuding 用户名和密码）：

- **OMSGatewayConfig** –返回网关配置为侦听的 TCP 端口。
- **OMSGatewayRelayProxy** –返回配置与之通信的代理服务器的 IP 地址。

## <a name="configure-network-load-balancing"></a>配置网络负载均衡 
你可以使用 Microsoft[网络负载平衡（nlb）](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)、 [Azure 负载均衡器](../../load-balancer/load-balancer-overview.md)或基于硬件的负载均衡器来配置使用网络负载平衡（nlb）的高可用性网关。 负载均衡器通过在其节点之间重定向 Log Analytics 代理或 Operations Manager 管理服务器请求的连接来管理流量。 如果一台网关服务器出现故障，流量将重定向到其他节点。

### <a name="microsoft-network-load-balancing"></a>Microsoft 网络负载平衡
若要了解如何设计和部署 Windows Server 2016 网络负载均衡群集，请参阅[网络负载均衡](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)。 以下步骤介绍如何配置 Microsoft 网络负载均衡群集。  

1. 使用管理帐户登录到属于 NLB 群集的 Windows 服务器。  
2. 在服务器管理器中打开网络负载均衡管理器，单击“工具”，并单击“网络负载均衡管理器”。
3. 要连接装有 Microsoft Monitoring Agent 的 Log Analytics 网关服务器，请右键单击群集的 IP 地址，然后单击“将主机添加到群集”。 

    ![网络负载平衡管理器–将主机添加到群集](./media/gateway/nlb02.png)
 
4. 输入要连接的网关服务器的 IP 地址。 

    ![网络负载均衡管理器 – 将主机添加到群集：连接](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure 负载均衡器
若要了解如何设计和部署 Azure 负载均衡器，请参阅[什么是 Azure 负载均衡器？](../../load-balancer/load-balancer-overview.md)。 若要部署基本的负载均衡器，请执行本[快速入门](../../load-balancer/quickstart-create-basic-load-balancer-portal.md)中概述的步骤，排除**创建后端服务器**部分中所述的步骤。   

> [!NOTE]
> 使用**基本 SKU**配置 Azure 负载均衡器需要将 azure 虚拟机属于可用性集。 若要详细了解可用性集，请参阅[在 Azure 中管理 Windows 虚拟机的可用性](../../virtual-machines/windows/manage-availability.md)。 若要将现有虚拟机添加到可用性集，请参阅[设置 Azure 资源管理器 VM 可用性集](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)。
> 

创建负载均衡器后，需要创建后端池，以将流量分发到一个或多个网关服务器。 按照快速入门文章部分[为负载均衡器创建资源](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer)中所述的步骤进行操作。  

>[!NOTE]
>配置运行状况探测时，应将其配置为使用网关服务器的 TCP 端口。 运行状况探测根据其对运行状况检查的响应，动态添加或删除负载均衡器轮换中的网关服务器。 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>配置 Log Analytics 代理和 Operations Manager 管理组
在本部分中，你将了解如何使用 Log Analytics 网关配置直接连接的 Log Analytics 代理、Operations Manager 管理组或 Azure 自动化混合 Runbook 辅助角色，以便与 Azure 自动化或 Log Analytics 通信。  

### <a name="configure-a-standalone-log-analytics-agent"></a>配置独立的 Log Analytics 代理
配置 Log Analytics 代理时，请将代理服务器值替换为 Log Analytics 网关服务器的 IP 地址及其端口号。 如果在负载平衡器后面部署了多个网关服务器，则 Log Analytics 代理的代理配置是负载均衡器的虚拟 IP 地址。  

>[!NOTE]
>若要在直接连接到 Log Analytics 的网关和 Windows 计算机上安装 Log Analytics 代理，请参阅[将 Windows 计算机连接到 Azure 中的 Log Analytics 服务](agent-windows.md)。 若要连接 Linux 计算机，请参阅[在混合环境中配置适用于 Linux 计算机的 Log Analytics 代理](../../azure-monitor/learn/quick-collect-linux-computer.md)。 
>

在网关服务器上安装代理后，将其配置为向与网关通信的工作区或工作区代理报告。 如果未在网关上安装 Log Analytics Windows 代理，则会将事件300写入 OMS 网关事件日志，指出需要安装代理。 如果安装了代理，但未将其配置为与通过它进行通信的代理报告到相同的工作区，则会将事件105写入同一日志，指出需要将网关上的代理配置为与共同通过网关 mmunicate。

完成配置后，重新启动 OMS 网关服务以应用所做的更改。 否则，网关将拒绝尝试与 Log Analytics 进行通信的代理，并将在 OMS 网关事件日志中报告事件105。 当你在网关服务器上的代理配置中添加或删除工作区时，也会发生这种情况。   

有关自动化混合 Runbook 辅助角色的信息，请参阅[使用混合 Runbook 辅助角色自动执行数据中心或云中的资源](../../automation/automation-hybrid-runbook-worker.md)。

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>配置 Operations Manager，其中所有代理使用相同的代理服务器

即使设置为空，Operations Manager 代理配置也会自动应用到向 Operations Manager 报告的所有代理。  

若要使用 OMS 网关支持 Operations Manager，你必须：

* Microsoft Monitoring Agent （版本8.0.10900.0 或更高版本）安装在 OMS 网关服务器上，并配置了管理组配置为向其报告的相同 Log Analytics 工作区。
* Internet 连接。 此外，OMS 网关必须连接到连接到 internet 的代理服务器。

> [!NOTE]
> 如果未指定网关的值，则会将空值推送到所有代理。
>

如果你的 Operations Manager 管理组首次向 Log Analytics 的工作区注册，你将看不到在操作控制台中为管理组指定代理配置的选项。 仅当管理组已注册到服务时，此选项才可用。  

若要配置集成，请在运行操作控制台的系统上以及管理组中的所有管理服务器上使用 Netsh 更新系统代理配置。 执行以下步骤：

1. 打开提升的命令提示符：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择 "**启动**"，然后输入**cmd**。  

   b. 右键单击 "**命令提示符**"，然后选择 "以**管理员身份运行**"。  

1. 输入以下命令：

   `netsh winhttp set proxy <proxy>:<port>`

完成与 Log Analytics 的集成后，通过运行 `netsh winhttp reset proxy`删除更改。 然后，在操作控制台中，使用 "**配置代理服务器**" 选项指定 Log Analytics 网关服务器。 

1. 在 Operations Manager 控制台上，在 " **Operations Management Suite**" 下选择 "**连接**"，然后选择 "**配置代理服务器**"。

   ![Operations Manager 的屏幕截图，显示选择配置代理服务器](./media/gateway/scom01.png)

1. 选择 "**使用代理服务器访问 Operations Management Suite** "，然后输入负载均衡器的 Log Analytics 网关服务器或虚拟 IP 地址的 ip 地址。 请注意 `http://`的前缀开头。

   ![显示代理服务器地址的 Operations Manager 屏幕截图](./media/gateway/scom02.png)

1. 选择“完成”。 Operations Manager 管理组现已配置为通过网关服务器与 Log Analytics 服务通信。

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>配置 Operations Manager，其中特定代理使用代理服务器

对于大型或复杂的环境，可能只希望特定的服务器（或组）使用 Log Analytics 的网关服务器。  对于这些服务器，无法直接更新 Operations Manager 代理，因为此值会被管理组的全局值覆盖。  相反，请重写用于推送这些值的规则。  

> [!NOTE] 
> 如果要在环境中允许多个 Log Analytics 网关服务器，请使用此配置技术。  例如，你可以要求按区域指定特定 Log Analytics 网关服务器。
>

若要配置特定的服务器或组以使用 Log Analytics 的网关服务器： 

1. 打开 Operations Manager 控制台并选择“创作”工作区。  
1. 在“创作”工作区中选择“规则”。 
1. 在 Operations Manager 工具栏上，选择 "**作用域**" 按钮。 如果此按钮不可用，请确保已在 "**监视**" 窗格中选择了对象而不是文件夹。 “范围管理包对象”对话框显示了通用目标类、组或对象的列表。 
1. 在 "**查找**" 字段中，输入**运行状况服务**并从列表中选择它。 选择“确定”。  
1. 搜索**Advisor 代理设置规则**。 
1. 在 Operations Manager 工具栏上，选择 "**替代**"，然后指向 **"覆盖类：运行状况服务的特定对象的规则 \ 针对"** ，然后从列表中选择一个对象。  或创建一个自定义组，其中包含要将此重写应用到的服务器的运行状况服务对象。 然后，将替代应用于自定义组。
1. 在 "**替代属性**" 对话框中，在**WebProxyAddress**参数旁的 "**替代**" 列中添加一个复选标记。  在 "**替代值**" 字段中，输入 Log Analytics 网关服务器的 URL。 请注意 `http://`的前缀开头。  

    >[!NOTE]
    > 不需要启用该规则。 它已使用 Microsoft system center advisor 安全引用替代管理包中的替代自动进行管理，该管理包面向 Microsoft System Center Advisor 监视服务器组。
    > 

1. 从 "**选择目标管理包**" 列表中选择管理包，或者通过选择 "**新建**" 创建新的未密封管理包。 
1. 完成后，选择“确定”。 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>为自动化混合 Runbook 辅助角色配置

如果你的环境中有自动化混合 Runbook 辅助角色，请按照以下步骤配置网关以支持辅助角色。

若要查找每个区域的 URL，请参阅自动化文档的[配置网络](../../automation/automation-hybrid-runbook-worker.md#network-planning)部分。

如果计算机已自动注册为混合 Runbook 辅助角色，例如，如果为一个或多个 Vm 启用了更新管理解决方案，请执行以下步骤：

1. 将作业运行时数据服务 URL 添加到 Log Analytics 网关上的“允许的主机”列表。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. 使用以下 PowerShell cmdlet 重新启动 Log Analytics 网关服务：`Restart-Service OMSGatewayService`

如果使用混合 Runbook 辅助角色注册 cmdlet 将计算机加入 Azure 自动化，请遵循以下步骤：

1. 将代理服务注册 URL 添加到 Log Analytics 网关上的“允许的主机”列表。 例如： `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. 将作业运行时数据服务 URL 添加到 Log Analytics 网关上的“允许的主机”列表。 例如： `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. 重新启动 Log Analytics 网关服务。
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>有用的 PowerShell cmdlet

你可以使用 cmdlet 来完成用于更新 Log Analytics 网关的配置设置的任务。 使用 cmdlet 之前，请务必执行以下操作：

1. 安装 Log Analytics 网关（Microsoft Windows Installer）。
1. 打开 PowerShell 控制台窗口。
1. 通过键入以下命令导入模块： `Import-Module OMSGateway`
1. 如果上一步骤未发生错误，则表示该模块已成功导入，可以使用 cmdlet。 输入 `Get-Module OMSGateway`
1. 使用 cmdlet 进行更改后，请重新启动 OMS 网关服务。

步骤3中的错误表示模块未导入。 当 PowerShell 无法找到该模块时，可能会发生此错误。 可在 OMS 网关安装路径中找到该模块： *C:\Program FILES\MICROSOFT OMS Gateway\PowerShell\OmsGateway*。

| **Cmdlet** | **参数** | **说明** | **示例** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |密钥 |获取服务的配置 |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |密钥（必需） <br> Value |更改服务的配置 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |获取中继（上游）代理的地址 |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |地址<br> 用户名<br> 密码 |设置中继（上游）代理的地址（和凭据） |1. 设置中继代理和凭据：<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. 设置不需要身份验证的中继代理： `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. 清除中继代理设置：<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |获取当前允许的主机（仅限本地配置的允许主机，而不是自动下载的允许主机） |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |主机（必需） |将主机添加到允许列表 |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |主机（必需） |从允许列表中删除主机 |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |使用者（必需） |将客户端证书使用者添加到允许列表 |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |使用者（必需） |从允许列表中删除客户端证书使用者 |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |获取当前允许的客户端证书使用者（仅限本地配置的允许使用者，而不是自动下载的允许使用者） |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>故障排除

若要收集网关记录的事件，应安装 Log Analytics 代理。

![Log Analytics 网关日志中事件查看器列表的屏幕截图](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Log Analytics 网关事件 Id 和说明

下表显示 Log Analytics 的网关日志事件的事件 Id 和说明。

| **ID** | **说明** |
| --- | --- |
| 400 |没有特定 ID 的任何应用程序错误。 |
| 401 |配置错误。 例如，Listenport 提供 = "text" 而不是整数。 |
| 402 |分析 TLS 握手消息时发生异常。 |
| 403 |网络错误。 例如，无法连接到目标服务器。 |
| 100 |常规信息。 |
| 101 |服务已启动。 |
| 102 |服务已停止。 |
| 103 |已从客户端收到 HTTP CONNECT 命令。 |
| 104 |不是 HTTP CONNECT 命令。 |
| 105 |目标服务器不在允许列表中，或者目标端口不安全（443）。 <br> <br> 确保 OMS 网关服务器上的 MMA 代理和与 OMS 网关通信的代理已连接到同一个 Log Analytics 工作区。 |
| 105 |错误 TcpConnection –客户端证书无效： CN = Gateway。 <br><br> 确保使用 OMS 网关版本版本号1.0.395.0 或更高版本。 还要确保 OMS 网关服务器上的 MMA 代理和与 OMS 网关通信的代理已连接到同一个 Log Analytics 工作区。 |
| 106 |TLS/SSL 协议版本不受支持。<br><br> Log Analytics 网关仅支持 TLS 1.0、TLS 1.1 和1.2。 它不支持 SSL。|
| 107 |已验证 TLS 会话。 |

### <a name="performance-counters-to-collect"></a>要收集的性能计数器

下表显示了 Log Analytics 网关可用的性能计数器。 使用性能监视器来添加计数器。

| 名称 | **说明** |
| --- | --- |
| Log Analytics 网关/活动客户端连接 |活动客户端网络 (TCP) 连接数 |
| Log Analytics 网关/错误计数 |错误数 |
| Log Analytics 网关/连接的客户端 |连接的客户端数 |
| Log Analytics 网关/拒绝计数 |由于发生任何 TLS 验证错误而拒绝的数目 |

![显示性能计数器的 Log Analytics 网关界面的屏幕截图](./media/gateway/counters.png)

## <a name="assistance"></a>协助

登录到 Azure 门户时，可以通过 Log Analytics 网关或任何其他 Azure 服务或功能获得帮助。
若要获取帮助，请选择门户右上角的问号图标，然后选择 "**新建支持请求**"。 然后完成新的支持请求表单。

![新支持请求的屏幕截图](./media/gateway/support.png)

## <a name="next-steps"></a>后续步骤

[添加数据源](../../azure-monitor/platform/agent-data-sources.md)以便从连接的源收集数据，并将数据存储在 Log Analytics 工作区中。