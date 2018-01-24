---
title: "在 Azure 数据工厂中创建自承载的集成运行时 | Microsoft Docs"
description: "了解如何在 Azure 数据工厂中创建自承载的集成运行时，从而允许数据工厂访问私有网络中存储的数据。"
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: abnarain
ms.openlocfilehash: 2c7df5c0a976aae8e3e0b99b083bbde942493bfa
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>如何创建和配置自承载的集成运行时
集成运行时 (IR) 是 Azure 数据工厂用于在不同的网络环境之间提供数据集成功能的计算基础结构。 有关 IR 的详细信息，请参阅[集成运行时概述](concepts-integration-runtime.md)。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [数据工厂版本 1 文档](v1/data-factory-introduction.md)。

自承载集成运行时能够在云数据存储和专用网络中数据存储之间运行复制活动，并针对本地或 Azure 虚拟网络中的计算资源调度转换活动。 在本地计算机或专用网络中的虚拟机上安装自承载集成运行时的需求。  

本文档介绍了如何创建和配置自承载 IR。

## <a name="high-level-steps-to-install-self-hosted-ir"></a>安装自承载 IR 的高级步骤
1.  创建自承载集成运行时。 下面是 PowerShell 示例：

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2.  下载和安装自承载集成运行时（在本地计算机上）。
3.  检索身份验证密钥并使用密钥注册自承载集成运行时。 下面是 PowerShell 示例：

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="command-flow-and-data-flow"></a>命令流和数据流
在本地和云之间移动数据时，该活动使用自承载集成运行时将数据从本地数据源传输到云，反之亦然。

下面是使用自承载 IR 复制的步骤摘要的高级数据流：

![综合概述](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. 数据开发者使用 PowerShell cmdlet 在 Azure 数据工厂中创建了自承载集成运行时。 目前，Azure 门户不支持此功能。
2. 数据开发者通过指定应用于连接数据存储的自承载集成运行时实例，为本地数据存储创建了链接服务。 作为设置链接服务的一部分，数据开发者使用“凭据管理员”应用程序（当前不受支持）来设置身份验证类型和凭据。 凭据管理员应用程序对话框与数据存储进行通信，以测试连接和自承载集成运行时，从而保存凭据。
4.  自承载集成运行时节点使用 Windows 数据保护应用程序编程接口 (DPAPI) 加密凭据，并将它保存在本地。 如果设置多个节点以实现高可用性，则凭据将跨其他节点进一步同步。 每个节点都使用 DPAPI 加密凭据并将其存储在本地。 凭据同步对数据开发者透明并由自承载 IR 处理。    
5.  数据工厂服务与自承载集成运行时通信，以通过使用共享 Azure 服务总线队列的**控件通道**调度和管理作业。 当需要运行活动作业时，数据工厂会将请求与任何凭据信息一起加入队列（以防凭证尚未存储在自承载集成运行时）。 自承载集成运行时轮询队列后启动该作业。
6.  自承载集成运行时将数据从本地存储复制到云存储，反之亦然，具体取决于数据管道中复制活动的配置方式。 对于此步骤，自承载集成运行时直接通过安全 (HTTPS) 通道与基于云的存储服务（如 Azure Blob 存储）通信。

## <a name="considerations-for-using-self-hosted-ir"></a>使用自承载 IR 的注意事项

- 单个自承载集成运行时可用于多个本地数据源。 但是，**单个自承载集成运行时只与一个 Azure 数据工厂关联**，不能与另一个数据工厂共享。
- 在一台计算机上只能安装**一个自承载集成运行时实例**。 假设有两个需要访问本地数据源的数据工厂，那么需要在两台本地计算机上安装自承载集成运行时。 换言之，自承载集成运行时与特定的数据工厂相关联
- **自承载集成运行时不需要位于数据源所在的计算机上**。 但是，使自承载集成运行时更接近于数据源会减少自承载集成运行时连接到数据源的时间。 建议在不同于托管本地数据源的计算机上安装自承载集成运行时。 当自承载集成运行时和数据源位于不同的计算机上时，自承载集成运行时不会与数据源竞争资源。
- 可以**将不同计算机上的多个自承载集成运行时连接到同一本地数据源**。 例如，可以让两个自承载集成运行时服务两个数据工厂，但这两个数据工厂注册了同一个本地数据源。
- 如果已在计算机中安装了为 **Power BI** 方案提供服务的网关，那么在其他计算机上安装**用于 Azure 数据工厂的单独自承载集成运行时**。
- 自承载集成运行时必须用于支持 Azure 虚拟网络内的数据集成。
- 即使使用 **ExpressRoute**，也要将数据源视为本地数据源（位于防火墙之后）。 使用自承载集成运行时在服务和数据源之间建立连接。
- 即使数据存储位于 **Azure IaaS 虚拟机**上的云中，也必须使用自承载集成运行时。

## <a name="prerequisites"></a>先决条件

- 支持的**操作系统**版本有 Windows 7 Service Pack 1、Windows 8.1、Windows 10、Windows Server 2008 R2 SP1、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016。 **不支持在域控制器**上安装自承载集成运行时。
- 需要 **.NET Framework 4.6.1 或更高版本**。 如果在 Windows 7 计算机上安装自承载集成运行时，请安装 .NET Framework 4.6.1 或更高版本。 有关详细信息，请参阅 [.NET Framework 系统需求](/dotnet/framework/get-started/system-requirements)。
- 推荐的自托管集成运行时计算机**配置**至少为 2 GHz，4 核，8 GB RAM 和 80 GB 磁盘。
- 如果主机计算机进入休眠状态，则自承载集成运行时不响应数据请求。 因此，安装自承载集成运行时之前，请在计算机上配置相应的电源计划。 如果计算机配置为休眠，则自承载集成运行时安装会提示消息。
- 只有计算机管理员才能成功安装和配置自承载集成运行时。
- 由于复制活动按特定频率发生，因此计算机上的资源使用率（CPU、内存）也遵循相同的高峰期和空闲期模式。 资源利用率还很大程度上取决于正在移动的数据量。 进行多个复制作业时，会看到资源使用率在高峰期上升。

## <a name="installation-best-practices"></a>安装最佳做法
可以通过从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=39717)下载 MSI 安装程序包来安装自承载集成运行时。 请参阅文章[在本地和云之间移动数据](tutorial-hybrid-copy-powershell.md)以获取分步说明。

- 在主机上为自承载集成运行时配置电源计划，从而让计算机不要休眠。 如果主机进入休眠状态，则自承载集成运行时将转为离线。
- 定期备份与自承载集成运行时相关的凭据。

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>从下载中心安装并注册自承载 IR

1. 导航到 [Microsoft 集成运行时下载页](https://www.microsoft.com/download/details.aspx?id=39717)。
2. 单击“下载”，选择适当的版本（“32 位”或“64 位”），并单击“下一步”。
3. 直接运行 **MSI** 或将其保存到硬盘再运行。
4. 在“欢迎”页上，选择“语言”，单击“下一步”。
5. 选择“接受”以接受最终用户许可协议，并单击“下一步”。
6. 选择**文件夹**来安装自承载集成运行时，然后单击“下一步”。
7. 在“准备安装”页上，单击“安装”。
8. 单击“完成”，完成安装。
9. 使用 Azure PowerShell 获取身份验证密钥。 检索身份验证密钥的 PowerShell 示例：

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. 在计算机上运行的 Microsoft 集成运行时配置管理器的“注册集成运行时（自承载）”页上，执行以下步骤：
    1. 将**身份验证密钥**粘贴到文本区域。
    2. 或者单击“显示身份验证密钥”，以查看密钥文本。
    3. 单击“注册”。


## <a name="high-availability-and-scalability"></a>高可用性和可伸缩性
一个自托管 Integration Runtime 可关联到多台本地计算机。 这些计算机称为节点。 至多可将 4 个节点与一个自承载集成运行时相关联。 一个逻辑网关配多个节点（已安装网关的本地计算机）的好处如下：
1. 更高的自承载集成运行时可用性，使其不再是大数据解决方案或与 Azure 数据工厂集成的云数据中的单点故障，从而确保最多 4 个节点的连续性。
2. 在本地和云数据存储之间移动数据期间提高了性能和吞吐量。 获取有关[性能比较](copy-activity-performance.md)的更多信息。

如[教程](tutorial-hybrid-copy-powershell.md)中所述，可以通过直接从[下载中心](https://www.microsoft.com/download/details.aspx?id=39717)安装自托管 Integration Runtime 软件，并通过使用从 New-AzureRmDataFactoryV2IntegrationRuntimeKey cmdlet 获取的任一身份验证密钥进行注册来关联多个节点

> [!NOTE]
> 不需要为关联每个节点而创建新的自承载集成运行时。 可以在另一台计算机上安装自承载集成运行时，并使用同一身份验证密钥注册它。 

> [!NOTE]
> 在添加另一个节点以实现**高可用性和可伸缩性**之前，请确保已在第 1 个节点上**启用**“远程访问 Intranet”选项（Microsoft Integration Runtime Configuration Manager ->“设置”->“远程访问 Intranet”）。 

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 证书要求
下面是用于保护集成运行时节点间通信的 TLS/SSL 证书的相关要求：

- 证书必须是公共可信的 X509 v3 证书。 建议使用公共（即第三方）证书颁发机构 (CA) 颁发的证书。
- 每个集成运行时节点必须信任此证书。
- 支持通配符证书。 如果 FQDN 名称为 node1.domain.contoso.com，可以使用 *.domain.contoso.com 作为证书的使用者名称。
- 不建议使用 SAN 证书，因为鉴于当前限制，只会使用使用者可选名称的最后一项，其他所有项都会遭忽略。 例如 有一个 SAN 证书，其中 SAN 为 node1.domain.contoso.com 和 node2.domain.contoso.com，那么只能在 FQDN 为 node2.domain.contoso.com 的计算机上使用此证书。
- 针对 SSL 证书支持受 Windows Server 2012 R2 支持的任何密钥大小。
- 不支持使用 CNG 密钥的证书。 不支持使用 CNG 密钥的证书。

## <a name="system-tray-icons-notifications"></a>系统托盘图标/通知
如果将游标移动到系统托盘图标/通知消息上，可以查看自承载集成运行时状态的详细信息。

![系统托盘通知](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>端口和防火墙
需要考虑两个防火墙：在组织的中央路由器上运行的**企业防火墙**和在安装了自承载集成运行时的本地计算机上配置为守护程序的 **Windows 防火墙**。

![防火墙](media\create-self-hosted-integration-runtime\firewall.png)

在**企业防火墙**级别，需配置以下域和出站端口：

域名 | 端口 | 说明
------------ | ----- | ------------
*.servicebus.windows.net | 443, 80 | 用于与数据移动服务后端进行通信
*.core.windows.net | 443 | 用于使用 Azure Blob 的暂存复制（如果已配置）
*.frontend.clouddatahub.net | 443 | 用于与数据移动服务后端进行通信

在 **Windows 防火墙**级别（计算机级别），通常已启用这些出站端口。 如果没有，可以在自承载集成运行时计算机上相应地配置域和端口。

> [!NOTE]
> 根据源/接收器，可能需要在企业/Windows 防火墙中将其他域和出站端口加入允许列表。
>
> 对于部分云数据库（例如，Azure SQL 数据库、Azure Data Lake 等），可能需要在其防火墙配置中将自承载集成运行时计算机的 IP 地址列入白名单。

### <a name="copy-data-from-a-source-to-a-sink"></a>将数据从源复制到接收器
确保在企业防火墙、自承载集成运行时计算机上的 Windows 防火墙和数据存储上正确启用防火墙规则。 启用这些规则可以让自承载集成运行时成功连接到源和接收器。 为复制操作涉及的每个数据存储启用规则。

例如，若要从**本地数据存储复制到 Azure SQL 数据库接收器或 Azure SQL 数据仓库接收器**，请执行以下步骤：

- 对于 Windows 防火墙和企业防火墙，允许 **1433** 端口上的出站 **TCP** 通信。
- 配置 Azure SQL 服务器的防火墙设置，以将自承载集成运行时计算机的 IP 地址添加到允许的 IP 地址列表。

> [!NOTE]
> 如果防火墙不允许出站端口 1433，则自承载集成运行时无法直接访问 Azure SQL。 在这种情况下，可以将[暂存复制](copy-activity-performance.md)用于 SQL Azure 数据库/SQL Azure DW。 在这种情况下，仅需要将 HTTPS（端口 443）用于数据移动。


## <a name="proxy-server-considerations"></a>代理服务器注意事项
如果企业网络环境使用代理服务器访问 Internet，请配置自承载集成运行时以使用合适的代理设置。 可以在初始注册阶段设置代理。

![指定代理](media\create-self-hosted-integration-runtime\specify-proxy.png)

自承载集成运行时使用代理服务器连接到云服务。 在初始设置期间单击“更改”链接。 会看到“代理设置”对话框。

![设置代理](media\create-self-hosted-integration-runtime\set-http-proxy.png)

有三个配置选项：

- **不使用代理**：自承载集成运行时不显式使用任何代理来连接到云服务。
- **使用系统代理**：自承载集成运行时使用在 diahost.exe.config 和 diawp.exe.config 中配置的代理设置。如果 diahost.exe.config 和 diawp.exe.config 中未配置代理，则自承载集成运行时无需通过代理，可直接连接到云服务。
- **使用自定义代理**：配置用于自承载集成运行时的 HTTP 代理设置，而不使用 diahost.exe.config 和 diawp.exe.config 中的配置。需要地址和端口。 用户名和密码可选，具体取决于代理的身份验证设置。 所有设置都使用 Windows DPAPI 在自承载集成运行时进行加密，并存储在本地计算机上。

保存更新的代理设置之后，集成运行时主机服务会自动重启。

成功注册自承载集成运行时后，如果想要查看或更新代理设置，请使用集成运行时配置管理器。

1.  启动 **Microsoft 集成运行时配置管理器**。
2.  切换到“设置”选项卡。
3.  单击“HTTP 代理”部分的“更改”链接，以启动“设置 HTTP 代理”对话框。
4.  单击“下一步”按钮后，将出现警告对话框，询问是否允许保存代理设置和重启集成运行时主机服务。

可以使用配置管理器工具查看和更新 HTTP 代理。

![查看代理](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> 如果使用 NTLM 身份验证设置代理服务器，集成运行时主机服务会在域帐户下运行。 如果之后更改域帐户密码，请记得更新服务的配置设置，并相应地将其重启。 鉴于此要求，建议使用专用域帐户来访问代理服务器，可以无需经常更新密码。

### <a name="configure-proxy-server-settings"></a>配置代理服务器设置

如果为 HTTP 代理选择“使用系统代理”设置，则自承载集成运行时使用 diahost.exe.config 和 diawp.exe.config 中的代理设置。如果 diahost.exe.config 和 diawp.exe.config 中未指定代理，则自承载集成运行时无需通过代理，可直接连接到云服务。 以下过程说明如何更新 diahost.exe.config 文件。

1. 在文件资源管理器中，生成 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config 的安全副本，以备份原始文件。
2. 启动作为管理员运行的 Notepad.exe，并打开文本文件“C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config”。找到 system.net 的默认标记，如以下代码中所示：

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    然后可以添加代理服务器的详细信息，如以下示例所示：

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    允许在代理标记中使用其他属性，以指定所需设置（如 scriptLocation）。 关于语法，请参阅[代理元素（网络设置）](https://msdn.microsoft.com/library/sa91de1e.aspx)。

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. 将配置文件保存到原始位置，然后重启自承载集成运行时主机服务，以获得更改。 若要重启服务，请执行以下步骤：从控制面板使用服务小程序，或在“集成运行时配置管理器”中依次单击“停止服务”按钮和“启动服务”使用服务小程序。 如果服务未启动，很可能是将错误的 XML 标记语法添加到了编辑过的应用程序配置文件中。

> [!IMPORTANT]
> 不要忘记同时更新 diahost.exe.config 和 diawp.exe.config。

除了这几点，还需要确保 Microsoft Azure 列于公司的允许列表中。 可以从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=41653)下载有效的 Microsoft Azure IP 地址列表。

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>防火墙和代理服务器相关问题的可能症状
如果遇到类似于以下的错误，可能是由于防火墙或代理服务器配置错误，阻止了自承载集成运行时连接到数据工厂进行自身身份验证。 请参考上一部分，确保防火墙和代理服务器已正确配置。

1.  尝试注册自承载集成运行时的时候，会收到以下错误：“无法注册此集成运行时节点！ 请确认身份验证密钥有效，且集成服务主机服务在此计算机上运行。 "
2.  打开集成运行时配置管理器时，将看到状态为“已断开连接”或“正在连接”。 查看 Windows 事件日志时，在“事件查看器”>“应用程序和服务日志”>“Microsoft 集成运行时”下，会看到错误消息，例如以下错误：

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-intranet"></a>启用“从 Intranet 进行远程访问”  
如果使用 **PowerShell** 或**凭据管理器应用程序**加密网络中未安装自承载集成运行时的另一台计算机上的凭据，则需要启用“从 Intranet 进行远程访问”选项。 如果运行 **PowerShell** 或**凭据管理器应用程序**加密安装了自承载集成运行时的同一台计算机上的凭据，则可以不启用“从 Intranet 进行远程访问”选项。

在添加另一个节点以实现**高可用性和可伸缩性**之前，应**启用**“从 Intranet 进行远程访问”。  

在自承载集成运行时安装（v 3.3.xxxx.x 及更高版本）期间，默认情况下，自承载集成运行时安装将在自承载集成运行时计算机上禁用“从 Intranet 进行远程访问”。

如果使用的是第三方防火墙，则可以手动打开端口 8060（或用户配置的端口）。 如果在自承载集成运行时安装过程中遇到防火墙问题，可以尝试使用以下命令在不配置防火墙的情况下安装自承载集成运行时。

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> **凭据管理器应用程序**尚不可用于加密 ADFv2 中的凭据。 我们以后会添加此支持。  

如果选择不打开自承载集成运行时计算机上的端口 8060，则使用机制（而不是使用**设置凭据**应用程序）来配置数据存储凭据。 例如，可以使用 New-AzureRmDataFactoryV2LinkedServiceEncryptCredential PowerShell cmdlet。 请参阅“设置凭据和安全”部分，了解如何设置数据存储凭据。


## <a name="next-steps"></a>后续步骤
请参阅以下教程以获取分步说明：[教程：将本地数据复制到云](tutorial-hybrid-copy-powershell.md)。
