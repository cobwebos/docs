---
title: 创建自承载 Integration Runtime
description: 了解如何在 Azure 数据工厂中创建自承载集成运行时，使数据工厂能够访问专用网络中存储的数据。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 03/13/2020
ms.openlocfilehash: 6d18a8d09749b832984872b57eec8a36abc1b2e2
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857686"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>创建和配置自承载集成运行时

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

集成运行时 (IR) 是 Azure 数据工厂用于在不同的网络环境之间提供数据集成功能的计算基础结构。 有关 IR 的详细信息，请参阅[集成运行时概述](concepts-integration-runtime.md)。

自承载集成运行时能够在云数据存储和专用网络中数据存储之间运行复制活动。 它还可以针对本地网络或 Azure 虚拟网络中的计算资源调度转换活动。 安装自承载集成运行时需要在专用网络中提供一台本地计算机或虚拟机。  

本文介绍如何创建和配置自承载 IR。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>设置自承载集成运行时

若要创建和设置自承载集成运行时，请使用以下过程。

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>通过 Azure PowerShell 创建自承载 IR

1. 可以使用 Azure PowerShell 来完成此任务。 以下是示例：

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. 在本地计算机上[下载](https://www.microsoft.com/download/details.aspx?id=39717)并安装自承载集成运行时。

3. 检索身份验证密钥并使用密钥注册自承载集成运行时。 下面是 PowerShell 示例：

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>通过 Azure 数据工厂 UI 创建自承载 IR

使用以下步骤通过 Azure 数据工厂 UI 创建自承载 IR。

1. 在 Azure 数据工厂 UI 的“让我们开始吧”页上，选择最左侧窗格中的“创作”选项卡。  

   ![主页上的“创作”按钮](media/doc-common-process/get-started-page-author-button.png)

1. 选择最左侧窗格底部的“连接”，然后选择“连接”窗口中的“集成运行时”。    选择“+新建”。 

   ![创建集成运行时](media/create-self-hosted-integration-runtime/new-integration-runtime.png)

1. 在 "**集成运行时安装**" 页上，选择 " **Azure"、"自承载**"，然后选择 "**继续**"。 

1. 在下面的页面上，选择 "**自承载**" 以创建自承载 IR，然后选择 "**继续**"。
   ![创建 selfhosted IR](media/create-self-hosted-integration-runtime/new-selfhosted-integration-runtime.png)

1. 输入 IR 的名称，然后选择“创建”****。

1. 在 "**集成运行时安装**" 页上，选择 "**选项 1** " 下的链接以打开计算机上的快速设置。 或者遵循“选项 2”下的步骤进行手动安装。**** 以下说明基于手动安装：

   ![集成运行时安装](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. 复制并粘贴身份验证密钥。 选择“下载并安装集成运行时”。****

    1. 将自承载集成运行时下载到本地 Windows 计算机上。 运行安装程序。

    1. 在“注册集成运行时(自承载)”页上粘贴前面保存的密钥，然后选择“注册”。********
    
       ![注册 Integration Runtime](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. 在“新建 Integration Runtime (自承载)节点”页上，选择“完成”。********

1. 成功注册自承载集成运行时后，会看到以下窗口：

    ![注册成功](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>通过 Azure 资源管理器模板在 Azure VM 上安装自承载 IR

可以使用[创建自承载 IR 模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)在 Azure 虚拟机上自动完成自承载 IR 的安装。 使用该模板可以轻松地在 Azure 虚拟网络中创建一个完全正常运行的自承载 IR。 该 IR 具有高可用性和可伸缩性功能，前提是能够将节点计数设置为 2 或以上。

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>通过本地 PowerShell 安装现有的自承载 IR

可以使用命令行来安装或管理现有的自承载 IR。 这种用法特别有助于自动完成自承载 IR 节点的安装和注册。

自承载安装程序中包含 Dmgcmd.exe。 该程序通常位于 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\ folder 文件夹中。 此应用程序支持各种参数，可以使用用于自动化的批处理脚本通过命令行来调用。

按如下所示使用该应用程序：

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

下面是应用程序参数和属性的详细信息： 

| properties                                                    | 描述                                                  | 必需 |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **RegisterNewNode**"`<AuthenticationKey>`"                     | 使用指定的身份验证密钥注册自承载集成运行时节点。 | 否       |
| **RegisterNewNode**"`<AuthenticationKey>`" "`<NodeName>`"      | 使用指定的身份验证密钥和节点名称注册自承载集成运行时节点。 | 否       |
| **EnableRemoteAccess**"`<port>`" ["`<thumbprint>`"]            | 在当前节点上启用远程访问以设置高可用性群集。 或者直接对自承载 IR 启用凭据设置，而无需使用 Azure 数据工厂。 如果采用后一种做法，可以在同一网络中的远程计算机上使用 **New-AzDataFactoryV2LinkedServiceEncryptedCredential** cmdlet。 | 否       |
| **EnableRemoteAccessInContainer**"`<port>`" ["`<thumbprint>`"] | 启用当节点在容器中运行时以远程方式访问当前节点。 | 否       |
| **DisableRemoteAccess**                                         | 禁用对当前节点的远程访问。 多节点设置需要远程访问。 即使禁用了远程访问，也仍可正常运行 **New-AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell cmdlet。 只要在自承载 IR 节点所在的同一台计算机上执行该 cmdlet，就可以实现此行为。 | 否       |
| **键**"`<AuthenticationKey>`"                                 | 覆盖或更新以前的身份验证密钥。 请谨慎执行此操作。 如果密钥属于新的集成运行时，以前的自承载 IR 节点可能会脱机。 | 否       |
| **GenerateBackupFile**"`<filePath>`" "`<password>`"            | 为当前节点生成备份文件。 备份文件包含节点密钥和数据存储凭据。 | 否       |
| **ImportBackupFile**"`<filePath>`" "`<password>`"              | 从备份文件还原节点。                          | 否       |
| **重新启动**                                                     | 重启自承载集成运行时主机服务。   | 否       |
| **Start**                                                       | 启动自承载集成运行时主机服务。     | 否       |
| **Stop**                                                        | 停止自承载集成运行时主机服务。        | 否       |
| **StartUpgradeService**                                         | 启动自承载集成运行时升级服务。       | 否       |
| **StopUpgradeService**                                          | 停止自承载集成运行时升级服务。        | 否       |
| **TurnOnAutoUpdate**                                            | 启用自承载集成运行时自动更新。        | 否       |
| **TurnOffAutoUpdate**                                           | 禁用自承载集成运行时自动更新。       | 否       |
| **SwitchServiceAccount**"`<domain\user>`" ["`<password>`"]           | 将 DIAHostService 设置为以新帐户的形式运行。 对系统帐户和虚拟帐户使用空密码 ""。 | 否       |


## <a name="command-flow-and-data-flow"></a>命令流和数据流

在本地与云之间移动数据时，该活动使用自承载集成运行时在本地数据源与云之间传输数据。

下面是使用自承载 IR 进行复制的概要数据流步骤：

![数据流概要](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. 数据开发人员使用 PowerShell cmdlet 在 Azure 数据工厂中创建自承载集成运行时。 目前，Azure 门户不支持此功能。
1. 数据开发人员为本地数据存储创建一个链接服务。 为此，开发人员可以指定服务用来连接数据存储的自承载集成运行时实例。
1. 自承载集成运行时节点使用 Windows 数据保护应用程序编程接口 (DPAPI) 加密凭据，并将凭据保存在本地。 如果设置多个节点以实现高可用性，则凭据将跨其他节点进一步同步。 每个节点使用 DPAPI 加密凭据并将其存储在本地。 凭据同步对数据开发者透明并由自承载 IR 处理。
1. Azure 数据工厂与自承载集成运行时通信，以计划和管理作业。 通信是通过使用共享 [Azure 服务总线中继](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay)连接的控制通道进行的。 需要运行某个活动作业时，数据工厂会将请求以及任何凭据信息排队。 如果凭据尚未存储在自承载集成运行时中，则它就会执行此操作。 自承载集成运行时在轮询队列后启动作业。
1. 自承载集成运行时在本地存储与云存储之间复制数据。 复制方向取决于复制活动在数据管道中的配置方式。 对于此步骤，自承载集成运行时直接通过安全 HTTPS 通道与基于云的存储服务（如 Azure Blob 存储）通信。

## <a name="considerations-for-using-a-self-hosted-ir"></a>使用自承载 IR 的注意事项

- 可将单个自承载集成运行时用于多个本地数据源。 还可以在同一 Azure Active Directory (Azure AD) 租户中将此集成运行时与另一个数据工厂共享。 有关详细信息，请参阅[共享自承载集成运行时](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory)。
- 在一台计算机上只能安装一个自承载集成运行时实例。 如果有两个数据工厂需要访问本地数据源，请使用[自承载 IR 共享功能](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory)共享自承载集成 IR，或者在两台本地计算机（每个数据工厂一台）上安装自承载 IR。  
- 自承载集成运行时不需要位于数据源所在的计算机上。 但是，使自承载集成运行时更接近于数据源会减少自承载集成运行时连接到数据源的时间。 建议在不同于托管本地数据源的计算机上安装自承载集成运行时。 当自承载集成运行时和数据源位于不同的计算机上时，自承载集成运行时不会与数据源争用资源。
- 可将不同计算机上的多个自承载集成运行时连接到同一本地数据源。 例如，如果有两个自承载集成运行时为两个数据工厂提供服务，则可以将同一个本地数据源注册到这两个数据工厂。
- 如果已在计算机中安装了为 Power BI 方案提供服务的网关，那么在其他计算机上安装用于数据工厂的单独自承载集成运行时。
- 使用自承载集成运行时来支持 Azure 虚拟网络中的数据集成。
- 即使使用 Azure ExpressRoute，也要将数据源视为本地数据源（位于防火墙之后）。 使用自承载集成运行时将服务连接到数据源。
- 即使数据存储位于云中的 Azure 基础结构即服务 (IaaS) 虚拟机上，也应该使用自承载集成运行时。
- 在启用了符合 FIPS 标准的加密的 Windows 服务器上安装的自承载集成运行时中的任务可能会失败。 要解决此问题，请禁用服务器上符合 FIPS 标准的加密。 若要禁用符合 FIPS 标准的加密，请将以下注册表子项的值从 1（启用）更改为 0（禁用）：`HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`。

## <a name="prerequisites"></a>必备条件

- 支持的 Windows 版本为：
  + Windows 7 Service Pack 1
  + Windows 8.1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   不支持在域控制器上安装自承载集成运行时。
- 需要 .NET Framework 4.6.1 或更高版本。 如果在 Windows 7 计算机上安装自承载集成运行时，请安装 .NET Framework 4.6.1 或更高版本。 有关详细信息，请参阅 [.NET Framework 系统需求](/dotnet/framework/get-started/system-requirements)。
- 对于自承载集成运行时计算机，建议的最低配置是具有4核的 2 GHz 处理器、8 GB RAM 和 80 GB 可用硬盘空间。
- 如果主机计算机进入休眠状态，则自承载集成运行时不会响应数据请求。 安装自承载集成运行时之前，请在计算机上配置相应的电源计划。 如果计算机配置为休眠，则自承载集成运行时安装程序会通过消息发出提示。
- 只有计算机管理员才能成功安装和配置自承载集成运行时。
- 复制活动按特定的频率运行。 计算机上的处理器和 RAM 使用率遵循相同的高峰期和空闲期模式。 此外，资源使用率在很大程度上取决于移动的数据量。 进行多个复制作业时，会看到资源使用率在高峰期上升。
- 在提取 Parquet、ORC 或 Avro 格式的数据时，任务可能会失败。 有关 Parquet 的详细信息，请参阅 [Azure 数据工厂中的 Parquet 格式](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)。 文件创建活动在自承载集成计算机上运行。 必须满足以下先决条件才能按预期方式运行文件创建活动：
    - [Visual C++ 2010 Redistributable](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) 包 (x64)
    - [Adopt OpenJDK](https://adoptopenjdk.net/) 等 JRE 提供商提供的 Java Runtime (JRE) 版本 8。 确保已设置 `JAVA_HOME` 环境变量。

## <a name="installation-best-practices"></a>安装最佳做法

可以通过从[Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=39717)下载托管标识安装包来安装自承载集成运行时。 有关分步说明，请参阅[在本地与云之间移动数据](tutorial-hybrid-copy-powershell.md)一文。

- 在主机上为自承载集成运行时配置电源计划，使计算机不会休眠。 如果主机进入休眠状态，则自承载集成运行时将会脱机。
- 定期备份与自承载集成运行时相关的凭据。
- 若要自动完成自承载 IR 安装操作，请参阅[通过 PowerShell 安装现有的自承载 IR](#setting-up-a-self-hosted-integration-runtime)。  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>从 Microsoft 下载中心安装并注册自承载 IR

1. 转到 [Microsoft 集成运行时下载页](https://www.microsoft.com/download/details.aspx?id=39717)。
1. 选择“下载”，选择 64 位版本，然后选择“下一步”。******** 不支持 32 位版本。
1. 直接运行托管标识文件，或者将其保存到硬盘上并运行。
1. 在“欢迎”窗口中选择语言，然后选择“下一步”********。
1. 接受 Microsoft 软件许可条款，然后选择“下一步”。****
1. 选择用于安装自承载集成运行时的**文件夹**，然后选择“下一步”****。
1. 在 "**准备安装**" 页上，选择 "**安装**"。
1. 选择“完成”以完成安装。****
1. 使用 PowerShell 获取身份验证密钥。 下面是检索身份验证密钥的 PowerShell 示例：

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. 在计算机上运行的 Microsoft Integration Runtime Configuration Manager 的“注册集成运行时(自承载)”窗口中执行以下步骤：****

    1. 将身份验证密钥粘贴到文本区域。

    1. 或者选择“显示身份验证密钥”****，以查看密钥文本。

    1. 选择“注册”  。

## <a name="high-availability-and-scalability"></a>高可用性和可伸缩性

可将一个自承载集成运行时关联到 Azure 中的多个本地计算机或虚拟机。 这些计算机称为节点。 最多可将 4 个节点与一个自承载集成运行时相关联。 在安装了某个网关作为逻辑网关的本地计算机上配置多个节点的好处如下：

* 更高的自承载集成运行时可用性，使其不再是大数据解决方案或与数据工厂集成的云数据中的单一故障点。 这种可用性有助于确保使用最多 4 个节点来实现连续性。
* 在本地和云数据存储之间移动数据期间提高了性能和吞吐量。 获取有关[性能比较](copy-activity-performance.md)的更多信息。

可以通过从[下载中心](https://www.microsoft.com/download/details.aspx?id=39717)安装自承载集成运行时来关联多个节点。 然后，根据[此教程](tutorial-hybrid-copy-powershell.md)中所述，使用通过 **New-AzDataFactoryV2IntegrationRuntimeKey** cmdlet 获取的任一身份验证密钥来注册自承载集成运行时。

> [!NOTE]
> 无需创建新的自承载集成运行时即可关联每个节点。 可以在另一台计算机上安装自承载集成运行时，并使用同一身份验证密钥注册它。

> [!NOTE]
> 在添加另一个用于实现高可用性和可伸缩性的节点之前，请确保已在第一个节点上启用了“远程访问 Intranet”选项。**** 为此，请选择 " **Microsoft Integration Runtime Configuration Manager** > **设置** > " "**远程访问 intranet**"。

### <a name="scale-considerations"></a>扩展注意事项

#### <a name="scale-out"></a>横向扩展

如果自承载 IR 上的处理器使用率较高且可用内存较低，添加新节点有助于跨计算机横向扩展负载。 如果活动因超时或自承载 IR 节点处于脱机状态而失败，则向网关添加节点会有所作用。

#### <a name="scale-up"></a>纵向扩展

如果处理器和可用 RAM 未充分利用，但并发作业的执行达到了节点的限制，可以通过增加节点上可运行的并发作业数进行纵向扩展。 活动因自承载 IR 过载而超时时，也可以进行纵向扩展。 如下图所示，可以增加节点的最大容量：  

![增加节点上可运行的并发作业数](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 证书要求

下面是用于保护集成运行时节点间通信的 TLS/SSL 证书的相关要求：

- 证书必须是公共可信的 X509 v3 证书。 建议使用公共合作伙伴证书颁发机构 (CA) 颁发的证书。
- 每个集成运行时节点必须信任此证书。
- 不建议使用使用者可选名称 (SAN) 证书，因为只会使用最后一个 SAN 项。 其他所有 SAN 项将被忽略。 例如，如果某个 SAN 证书的 SAN 为 **node1.domain.contoso.com** 和 **node2.domain.contoso.com**，则只能在完全限定的域名 (FQDN) 为 **node2.domain.contoso.com** 的计算机上使用此证书。
- 此证书可以使用 Windows Server 2012 R2 支持的任何密钥大小，以实现 TLS/SSL 证书。
- 不支持使用 CNG 密钥的证书。  

> [!NOTE]
> 此证书用于：
>
> - 加密自承载 IR 节点上的端口。
> - 进行节点间的通信以实现状态同步，包括节点间的链接服务的凭据同步。
> - 使用 PowerShell cmdlet 从本地网络内部完成链接服务凭据设置时使用此证书。
>
> 如果你的专用网络环境不安全或者你要确保专用网络内部节点间通信的安全性，则我们建议使用此证书。
>
> 无论是否设置此证书，从自承载 IR 到其他数据存储的数据移动始终在加密的通道中发生。

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>在 Azure 数据工厂中创建共享自承载集成运行时

可以重复使用已在数据工厂中设置的现有自承载集成运行时基础结构。 这种重复使用可以通过引用现有的共享自承载 IR，在不同的数据工厂中创建链接的自承载集成运行时。

有关此功能的介绍和演示，请观看以下时长为 12 分钟的视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>术语

- **共享 IR**：在物理基础结构上运行的原始自承载 ir。  
- **链接 ir**：引用其他共享 IR 的 ir。 链接的 IR 是一个逻辑 IR，它使用另一个共享自承载 IR 的基础结构。

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>共享自承载集成运行时的方法

若要与多个数据工厂共享某个自承载集成运行时，请参阅[创建共享的自承载集成运行时](create-shared-self-hosted-integration-runtime-powershell.md)了解详细信息。

### <a name="monitoring"></a>监视

#### <a name="shared-ir"></a>共享 IR

![用于查找共享集成运行时的选项](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![监视共享的集成运行时](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>链接 IR

![用于查找链接集成运行时的选项](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![监视链接的集成运行时](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>自承载 IR 共享的已知限制

* 在其中创建链接的 IR 的数据工厂必须具有[托管标识](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)。 默认情况下，在 Azure 门户或 PowerShell cmdlet 中创建的数据工厂具有隐式创建的托管标识。 但是，如果数据工厂是通过 Azure 资源管理器模板或 SDK 创建的，则必须显式设置 **Identity** 属性。 此设置可确保资源管理器创建包含托管标识的数据工厂。

* 支持此功能的数据工厂 .NET SDK 必须是 1.1.0 或更高版本。

* 若要授予权限，需要在共享 IR 所在的数据工厂中拥有“所有者”角色或继承的“所有者”角色。

* 共享功能仅适用于同一 Azure AD 租户中的数据工厂。

* 对于 Azure AD [guest 用户](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)，UI 中的搜索功能（使用搜索关键字列出所有数据工厂）[不起作用](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits)。 但只要来宾用户是数据工厂的所有者，你就可以在不包含搜索功能的情况下共享 IR。 对于需要共享 IR 的数据工厂的托管标识，请在 "**分配权限**" 框中输入该托管标识，并在数据工厂 UI 中选择 "**添加**"。

  > [!NOTE]
  > 此功能只能在数据工厂 V2 中使用。

## <a name="notification-area-icons-and-notifications"></a>通知区域图标和通知

如果将光标移到通知区域中的图标或消息上，可以查看自承载集成运行时状态的详细信息。

![通知区域中的通知](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>端口和防火墙

需要考虑两个防火墙：

- 在组织的中央路由器上运行的企业防火墙。**
- 在安装自承载集成运行时的本地计算机上作为守护程序配置的 Windows 防火墙。**

![防火墙](media/create-self-hosted-integration-runtime/firewall.png)

在企业防火墙级别，需配置以下域和出站端口：

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

在 Windows 防火墙级别或计算机级别，通常已启用这些出站端口。 如果未启用，可以在自承载集成运行时计算机上配置域和端口。

> [!NOTE]
> 根据源和接收器，可能需要在企业防火墙或 Windows 防火墙中允许其他域和出站端口。
>
> 对于某些云数据库（例如 Azure SQL 数据库和 Azure Data Lake），可能需要在其防火墙配置中允许自承载集成运行时计算机的 IP 地址。

### <a name="copy-data-from-a-source-to-a-sink"></a>将数据从源复制到接收器

确保在企业防火墙、自承载集成运行时计算机上的 Windows 防火墙和数据存储上正确启用防火墙规则。 启用这些规则可以让自承载集成运行时成功连接到源和接收器。 为复制操作涉及的每个数据存储启用规则。

例如，若要从本地数据存储复制到 SQL 数据库接收器或 Azure SQL 数据仓库接收器，请执行以下步骤：

1. 对于 Windows 防火墙和企业防火墙，允许 1433 端口上的出站 TCP 通信。
1. 配置 SQL 数据库的防火墙设置，以将自承载集成运行时计算机的 IP 地址添加到允许的 IP 地址列表中。

> [!NOTE]
> 如果防火墙不允许出站端口 1433，则自承载集成运行时无法直接访问 SQL 数据库。 在这种情况下，可对 SQL 数据库和 SQL 数据仓库使用[分阶段复制](copy-activity-performance.md)。 对于此方案，只需将 HTTPS（端口 443）用于数据移动。

## <a name="proxy-server-considerations"></a>代理服务器注意事项

如果企业网络环境使用代理服务器访问 Internet，请配置自承载集成运行时以使用合适的代理设置。 可以在初始注册阶段设置代理。

![指定代理](media/create-self-hosted-integration-runtime/specify-proxy.png)

配置后，自承载集成运行时使用代理服务器连接到云服务的源和目标（使用 HTTP/HTTPS 协议的源/目标）。 因此，请在初始设置期间选择“更改链接”。****

![设置代理](media/create-self-hosted-integration-runtime/set-http-proxy.png)

有三个配置选项：

- **不要使用代理**：自承载集成运行时不会显式使用任何代理连接到云服务。
- **使用系统代理**：自承载集成运行时使用在 diahost.exe.config 和 diawp.exe.config 中配置的代理设置。如果这些文件不指定代理配置，则自承载集成运行时无需通过代理即可直接连接到云服务。
- **使用自定义代理**：配置用于自承载集成运行时的 HTTP 代理设置，而不是使用 diahost.exe.config 和 diawp.exe.config 中的配置。**地址**和**端口**值是必需的。 **用户名**和**密码**值是可选的，具体取决于代理的身份验证设置。 所有设置都使用 Windows DPAPI 在自承载集成运行时进行加密，并存储在本地计算机上。

保存更新的代理设置之后，集成运行时主机服务会自动重启。

成功注册自承载集成运行时后，如果想要查看或更新代理设置，请使用 Microsoft Integration Runtime Configuration Manager。

1. 打开“Microsoft Integration Runtime Configuration Manager”。****
1. 选择“设置”选项卡。****
1. 在“HTTP 代理”下，选择“更改”链接打开“设置 HTTP 代理”对话框。************
1. 选择“下一步”  。 此时会出现警告，询问是否允许保存代理设置和重启集成运行时主机服务。

可以使用 Configuration Manager 工具查看和更新 HTTP 代理。

![查看和更新代理](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> 如果使用 NTLM 身份验证设置代理服务器，集成运行时主机服务会在域帐户下运行。 如果之后更改域帐户密码，请记得更新服务的配置设置并重启服务。 鉴于此项要求，我们建议使用专用域帐户来访问代理服务器，这样就无需经常更新密码。

### <a name="configure-proxy-server-settings"></a>配置代理服务器设置

如果为 HTTP 代理选择 "**使用系统代理**" 选项，则自承载集成运行时将使用 diahost.exe.config 和 diawp.exe.config 中的代理设置。当这些文件不指定代理时，自承载集成运行时将直接连接到云服务，无需通过代理。 以下过程说明如何更新 diahost.exe.config 文件：

1. 在文件资源管理器中，生成 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config 的安全副本作为原始文件的备份。
1. 以管理员身份打开记事本。
1. 在记事本中，打开文本文件 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config。
1. 找到默认的 **system.net** 标记，如以下代码中所示：

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

    允许在代理标记中使用其他属性，以指定所需的设置（如 `scriptLocation`）。 有关语法，请参阅[ \<代理\>元素（网络设置）](https://msdn.microsoft.com/library/sa91de1e.aspx) 。

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. 将配置文件保存到其原始位置。 然后重启自承载集成运行时主机服务，以拾取更改。

   若要重启服务，请从控制面板使用“服务”小程序。 或在“Integration Runtime Configuration Manager”中依次选择“停止服务”按钮和“启动服务”。********

   如果服务未启动，原因可能是将错误的 XML 标记语法添加到了编辑的应用程序配置文件中。

> [!IMPORTANT]
> 不要忘记同时更新 diahost.exe.config 和 diawp.exe.config。

还需要确保 Microsoft Azure 在公司的允许列表中。 可以从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=41653)下载有效的 Azure IP 地址列表。

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>防火墙和代理服务器相关问题的可能症状

如果出现如下所示的错误消息，原因可能是防火墙或代理服务器的配置不当。 此类配置会阻止自承载集成运行时连接到数据工厂对自身进行身份验证。 若要确保正确配置防火墙和代理服务器，请参阅上一部分。

* 尝试注册自承载集成运行时时，会收到以下错误消息： "无法注册此 Integration Runtime 节点！ 请确认身份验证密钥有效，且集成服务主机服务在此计算机上运行。”
* 打开 Integration Runtime Configuration Manager 时，将看到状态为“已断开连接”**** 或“正在连接”****。 查看 Windows 事件日志时，在 "**事件查看器** > **应用程序和服务日志** > "**Microsoft Integration Runtime**下，你会看到如下错误消息：

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>启用“从 Intranet 进行远程访问”

如果使用 PowerShell 来加密未安装自承载集成运行时的另一台联网计算机上的凭据，可以启用“从 Intranet 进行远程访问”选项。**** 如果运行 PowerShell 来加密已安装自承载集成运行时的计算机上的凭据，则无法启用“从 Intranet 进行远程访问”。****

在添加另一个用于实现高可用性和可伸缩性的节点之前，请启用“从 Intranet 进行远程访问”。****  

运行自承载集成运行时安装程序版本 3.3 或更高版本时，默认情况下，自承载集成运行时安装程序将在自承载集成运行时计算机上禁用“从 Intranet 进行远程访问”****。

使用合作伙伴的防火墙或其他防火墙时，可以手动打开端口 8060 或用户配置的端口。 如果在安装自承载集成运行时期间防火墙出现问题，请使用以下命令在不配置防火墙的情况下安装自承载集成运行时。

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

如果选择不打开自承载集成运行时计算机上的端口 8060，请使用除“设置凭据”应用程序以外的机制来配置数据存储凭据。 例如，可以使用 **New-AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell cmdlet。

## <a name="next-steps"></a>后续步骤

有关分步说明，请参阅[教程：将本地数据复制到云](tutorial-hybrid-copy-powershell.md)。
