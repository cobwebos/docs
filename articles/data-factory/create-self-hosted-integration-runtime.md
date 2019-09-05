---
title: 在 Azure 数据工厂中创建自承载集成运行时 | Microsoft Docs
description: 了解如何在 Azure 数据工厂中创建自承载集成运行时，从而允许数据工厂访问专用网络中存储的数据。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/18/2019
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: be59f5fd34c52397b54146a8aeaf51f4d594452f
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383348"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>创建和配置自承载集成运行时
集成运行时 (IR) 是 Azure 数据工厂用于在不同的网络环境之间提供数据集成功能的计算基础结构。 有关 IR 的详细信息，请参阅[集成运行时概述](concepts-integration-runtime.md)。

自承载集成运行时能够在云数据存储和专用网络中数据存储之间运行复制活动，并针对本地网络或 Azure 虚拟网络中的计算资源调度转换活动。 在本地计算机或专用网络中的虚拟机 (VM) 上安装自承载集成运行时的需求。  

本文档介绍如何创建和配置自承载 IR。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="high-level-steps-to-install-a-self-hosted-ir"></a>安装自承载 IR 的概要步骤
1. 创建自我托管的集成运行时。 可以使用 Azure 数据工厂 UI 完成此任务。 下面是 PowerShell 示例：

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. 在本地计算机上[下载](https://www.microsoft.com/download/details.aspx?id=39717)并安装自承载集成运行时。

3. 检索身份验证密钥并使用密钥注册自承载集成运行时。 下面是 PowerShell 示例：

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

## <a name="setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板在 Azure VM 上设置自承载 IR 
可以使用[此 Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)在 Azure 虚拟机上自动完成自承载 IR 设置。 使用此模板可以轻松地在 Azure 虚拟网络中设置一个完全正常运行的、具有高可用性和可伸缩性功能的自承载 IR（前提是能够将节点计数设置为 2 或以上）。

## <a name="command-flow-and-data-flow"></a>命令流和数据流
在本地和云之间移动数据时，该活动使用自承载集成运行时将数据从本地数据源传输到云，反之亦然。

下面是使用自承载 IR 复制的步骤摘要的高级数据流：

![综合概述](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. 数据开发者使用 PowerShell cmdlet 在 Azure 数据工厂中创建了自承载集成运行时。 目前，Azure 门户不支持此功能。
2. 数据开发者通过指定应用于连接数据存储的自承载集成运行时实例，为本地数据存储创建了链接服务。
3. 自承载集成运行时节点使用 Windows 数据保护应用程序编程接口 (DPAPI) 加密凭据，并将凭据保存在本地。 如果设置多个节点以实现高可用性，则凭据将跨其他节点进一步同步。 每个节点使用 DPAPI 加密凭据并将其存储在本地。 凭据同步对数据开发者透明并由自承载 IR 处理。    
4. 数据工厂服务与自承载集成运行时通信，以通过使用共享 [Azure 服务总线中继](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay)的控制通道调度和管理作业。 当需要运行活动作业时，数据工厂会将请求与任何凭据信息一起加入队列（以防凭证尚未存储在自承载集成运行时）。 自承载集成运行时轮询队列后启动该作业。
5. 自承载集成运行时将数据从本地存储复制到云存储，反之亦然，具体取决于数据管道中复制活动的配置方式。 对于此步骤，自承载集成运行时直接通过安全 (HTTPS) 通道与基于云的存储服务（如 Azure Blob 存储）通信。

## <a name="considerations-for-using-a-self-hosted-ir"></a>使用自承载 IR 的注意事项

- 单个自承载集成运行时可用于多个本地数据源。 单个自承载集成运行时可与同一 Azure Active Directory 租户中的另一个数据工厂共享。 有关详细信息，请参阅[共享自承载集成运行时](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories)。
- 在一台计算机上只能安装一个自承载集成运行时实例。 如果有两个数据工厂需要访问本地数据源，请使用[自托管 IR 共享功能](#sharing-the-self-hosted-integration-runtime-with-multiple-data-factories)共享自承载集成运行时，或者在两台本地计算机上安装自承载集成运行时，每个数据工厂一台。  
- 自承载集成运行时不需要位于数据源所在的计算机上。 但是，使自承载集成运行时更接近于数据源会减少自承载集成运行时连接到数据源的时间。 建议在不同于托管本地数据源的计算机上安装自承载集成运行时。 当自承载集成运行时和数据源位于不同的计算机上时，自承载集成运行时不会与数据源竞争资源。
- 可将不同计算机上的多个自承载集成运行时连接到同一本地数据源。 例如，可以让两个自承载集成运行时服务两个数据工厂，但这两个数据工厂注册了同一个本地数据源。
- 如果已在计算机中安装了为 Power BI 方案提供服务的网关，那么在其他计算机上安装用于 Azure 数据工厂的单独自承载集成运行时。
- 自承载集成运行时必须用于支持 Azure 虚拟网络内的数据集成。
- 即使使用 Azure ExpressRoute，也要将数据源视为本地数据源（位于防火墙之后）。 使用自承载集成运行时在服务和数据源之间建立连接。
- 即使数据存储位于 Azure IaaS 虚拟机上的云中，也必须使用自承载集成运行时。
- Windows Server 上安装的自承载集成运行时中的任务可能会失败，因为 Windows Server 中启用了符合 FIPS 标准的加密。 要解决此问题，请禁用服务器上符合 FIPS 标准的加密。 要禁用符合 FIPS 标准的加密，请将以下注册表值从 1（启用）更改为 0（禁用）：`HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`。

## <a name="prerequisites"></a>先决条件

- 支持的操作系统版本有 Windows 7 Service Pack 1、Windows 8.1、Windows 10、Windows Server 2008 R2 SP1、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 和 Windows Server 2019。 不支持在域控制器上安装自承载集成运行时。
- 需要 .NET Framework 4.6.1 或更高版本。 如果在 Windows 7 计算机上安装自承载集成运行时，请安装 .NET Framework 4.6.1 或更高版本。 有关详细信息，请参阅 [.NET Framework 系统需求](/dotnet/framework/get-started/system-requirements)。
- 推荐的自承载集成运行时计算机配置至少为 2 GHz，4 核，8 GB RAM 和 80 GB 磁盘。
- 如果主机计算机进入休眠状态，则自承载集成运行时不响应数据请求。 安装自承载集成运行时之前，请在计算机上配置相应的电源计划。 如果计算机配置为休眠，则自承载集成运行时安装会提示消息。
- 只有计算机管理员才能成功安装和配置自承载集成运行时。
- 复制活动按特定的频率发生。 计算机上的资源使用率（CPU、内存）也遵循相同的高峰期和空闲期模式。 资源利用率还很大程度上取决于正在移动的数据量。 进行多个复制作业时，会看到资源使用率在高峰期上升。
- 如果提取 Parquet、ORC 或 Avro 格式的数据，则任务可能会失败。 文件创建在自承载集成计算机上运行，并要求以下必备组件按预期工作（请参阅[Azure 数据工厂中的 Parquet 格式](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)）。
    - [Visual C++ 2010 可再发行组件](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe)包（x64）
    - 来自 JRE 提供商的 Java 运行时（JRE）版本8（如[采用 OpenJDK](https://adoptopenjdk.net/)）， `JAVA_HOME`确保设置了环境变量。

## <a name="installation-best-practices"></a>安装最佳做法
可以通过从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=39717)下载 MSI 安装程序包来安装自承载集成运行时。 请参阅文章[在本地和云之间移动数据](tutorial-hybrid-copy-powershell.md)以获取分步说明。

- 在主机上为自承载集成运行时配置电源计划，从而让计算机不要休眠。 如果主机进入休眠状态，则自承载集成运行时将会脱机。
- 定期备份与自承载集成运行时相关的凭据。
- 若要自动完成自承载 IR 设置操作，请参阅[以下部分](#automation-support-for-self-hosted-ir-function)。  

## <a name="install-and-register-self-hosted-ir-from-the-download-center"></a>从下载中心安装并注册自承载 IR

1. 转到 [Microsoft 集成运行时下载页](https://www.microsoft.com/download/details.aspx?id=39717)。
2. 选择“下载”，然后选择 64 位版本（不支持 32 位版本），再选择“下一步”。
3. 直接运行 MSI 文件或将其保存到硬盘再运行。
4. 在“欢迎”页上选择语言，然后选择“下一步”。
5. 接受 Microsoft 软件许可条款，然后选择“下一步”。
6. 选择用于安装自承载集成运行时的**文件夹**，然后选择“下一步”。
7. 在“准备安装”页上，选择“安装”。
8. 单击“完成”，完成安装。
9. 使用 Azure PowerShell 获取身份验证密钥。 下面是检索身份验证密钥的 PowerShell 示例：

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. 在计算机上运行的 Microsoft Integration Runtime Configuration Manager 的“注册集成运行时(自承载)”页上，执行以下步骤：

    a. 将身份验证密钥粘贴到文本区域。

    b. 或者选择“显示身份验证密钥”，以查看密钥文本。

    c. 选择“注册”。

## <a name="automation-support-for-self-hosted-ir-function"></a>对自承载 IR 功能的自动化支持


> [!NOTE]
> 如果计划在 Azure 虚拟机上安装自承载 IR 并想要使用 Azure 资源管理器模板自动进行设置，请参阅[部分](#setting-up-a-self-hosted-ir-on-an-azure-vm-by-using-an-azure-resource-manager-template)。

可以使用命令行设置或管理现有的自承载 IR。 这可以专门用来自动完成安装，以及注册自承载 IR 节点。 

**Dmgcmd.exe** 包含在自承载安装中，通常位于：C:\Program Files\Microsoft Integration Runtime\3.0\Shared\ 文件夹。 这支持各种参数，可以使用用于自动化的批处理脚本通过命令提示来调用。 

*用途：* 

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["password"] -Loglevel <logLevel> ] 
```

 详细信息（参数/属性）： 

| 属性                                                    | 说明                                                  | 必填 |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| RegisterNewNode "`<AuthenticationKey>`"                     | 将集成运行时（自承载）节点注册到指定的身份验证密钥 | 否       |
| EnableRemoteAccess "`<port>`" ["`<thumbprint>`"]            | 在当前节点上启用远程访问，以便设置高可用性群集和/或启用直接针对自承载 IR（不通过 ADF 服务）通过同一网络的远程计算机中的 **New-AzDataFactoryV2LinkedServiceEncryptedCredential** cmdlet 设置凭据的功能。 | 否       |
| EnableRemoteAccessInContainer "`<port>`" ["`<thumbprint>`"] | 启用当节点在容器中运行时以远程方式访问当前节点的功能 | 否       |
| DisableRemoteAccess                                         | 禁用对当前节点的远程访问。 进行多节点设置时，需要远程访问。 New-**AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell cmdlet 在禁用远程访问的情况下仍可使用，只要在自承载 IR 节点所在的计算机上执行它即可。 | 否       |
| Key "`<AuthenticationKey>`"                                 | 覆盖/更新以前的身份验证密钥。 请谨慎操作，因为如果此密钥是新集成运行时的，这可能导致以前的自承载 IR 节点脱机。 | 否       |
| GenerateBackupFile "`<filePath>`" "`<password>`"            | 为当前节点生成备份文件，该备份文件包含节点密钥和数据存储凭据 | 否       |
| ImportBackupFile "`<filePath>`" "`<password>`"              | 从备份文件还原节点                          | 否       |
| 重新启动                                                     | 重启 Integration Runtime（自承载）主机服务   | 否       |
| Start                                                       | 启动 Integration Runtime（自承载）主机服务     | 否       |
| 停止                                                        | 停止 Integration Runtime（自承载）更新服务        | 否       |
| StartUpgradeService                                         | 启动 Integration Runtime（自承载）更新服务       | 否       |
| StopUpgradeService                                          | 停止 Integration Runtime（自承载）更新服务        | 否       |
| TurnOnAutoUpdate                                            | 启用 Integration Runtime（自承载）自动更新        | 否       |
| TurnOffAutoUpdate                                           | 关闭 Integration Runtime（自承载）自动更新       | 否       |
| SwitchServiceAccount "<domain\user>" ["password"]           | 将 DIAHostService 设置为以新帐户的形式运行。 对系统帐户或虚拟帐户使用空密码 ("") | 否       |
| Loglevel `<logLevel>`                                       | 设置 ETW 日志级别（Off、Error、Verbose 或 All）。 通常在调试时由 Microsoft 支持部门使用。 | 否       |

   


## <a name="high-availability-and-scalability"></a>高可用性和可伸缩性
一个自承载集成运行时可以与 Azure 中的多个本地计算机或虚拟机相关联。 这些计算机称为节点。 最多可将 4 个节点与一个自承载集成运行时相关联。 一个逻辑网关配多个节点（已安装网关的本地计算机）的好处如下：
* 更高的自承载集成运行时可用性，使其不再是大数据解决方案或与 Azure 数据工厂集成的云数据中的单点故障，从而确保最多 4 个节点的连续性。
* 在本地和云数据存储之间移动数据期间提高了性能和吞吐量。 获取有关[性能比较](copy-activity-performance.md)的更多信息。

可以通过从[下载中心](https://www.microsoft.com/download/details.aspx?id=39717)安装自承载集成运行时来关联多个节点。 然后，根据此[教程](tutorial-hybrid-copy-powershell.md)中所述，使用通过 **New-AzDataFactoryV2IntegrationRuntimeKey** cmdlet 获取的任一身份验证密钥来注册自承载集成运行时。

> [!NOTE]
> 不需要为关联每个节点而创建新的自承载集成运行时。 可以在另一台计算机上安装自承载集成运行时，并使用同一身份验证密钥注册它。 

> [!NOTE]
> 在添加另一个节点以实现高可用性和可伸缩性之前，请确保已在第 1 个节点上启用“远程访问 Intranet”选项（Microsoft Integration Runtime Configuration Manager > “设置” > “远程访问 Intranet”）。 

### <a name="scale-considerations"></a>扩展注意事项

#### <a name="scale-out"></a>横向扩展

如果自承载 IR 上的可用内存较低并且 CPU 使用量较高，则添加新节点有助于跨计算机横向扩展负载。 如果活动因超时或自承载 IR 节点处于脱机状态而失败，则向网关添加节点会有所作用。

#### <a name="scale-up"></a>纵向扩展

如果可用内存和 CPU 未充分利用，但并发作业执行即将达到限制，应通过增加节点上可运行的并发作业数进行纵向扩展。 此外，活动因自承载 IR 过载而超时时，可能也需要进行扩展。 如下图所示，可以增加节点的最大容量：  

![增加可在节点上运行的并发作业数](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 证书要求

下面是用于保护集成运行时节点间通信的 TLS/SSL 证书的相关要求：

- 证书必须是公共可信的 X509 v3 证书。 建议使用公共（合作伙伴）证书颁发机构 (CA) 颁发的证书。
- 每个集成运行时节点必须信任此证书。
- 不建议使用使用者可选名称 (SAN) 证书，因为鉴于当前限制，只会使用最后一个 SAN 项，并忽略其他所有项。 例如，如果某个 SAN 证书的 SAN 为 **node1.domain.contoso.com** 和 **node2.domain.contoso.com**，则只能在 FQDN 为 **node2.domain.contoso.com** 的计算机上使用此证书。
- 该证书支持 Windows Server 2012 R2 所支持的任何 SSL 证书密钥大小。
- 不支持使用 CNG 密钥的证书。  

> [!NOTE]
> 此证书用于加密自承载 IR 节点上的端口，以实现**节点到节点通信**（用于状态同步，这包括跨节点的链接服务凭据同步），同时从本地网络内部**将 PowerShell cmdlet 用于链接服务凭据设置**。 如果拥有的专用网络环境不安全或同时想要确保专用网络内部节点之间通信的安全性，建议使用此证书。 自承载 IR 至其他数据存储的数据移动始终会使用加密通道，无论是否设置此证书均是如此。 

## <a name="sharing-the-self-hosted-integration-runtime-with-multiple-data-factories"></a>与多个数据工厂共享自承载集成运行时

可以重复使用已在数据工厂中设置的现有自承载集成运行时基础结构。 这样，便可以通过引用现有的自承载 IR（共享），在不同的数据工厂中创建*链接的自承载集成运行时*。

若要使用 PowerShell 共享自承载集成运行时，请参阅[使用 PowerShell 在 Azure 数据工厂中创建共享自承载集成运行时](create-shared-self-hosted-integration-runtime-powershell.md)。

有关此功能的 12 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>术语

- **共享 IR**：在物理基础结构上运行的原始自承载 IR。  
- **链接 IR**：引用另一个共享 IR 的 IR。 这是一个逻辑 IR，它使用另一个自承载 IR（共享）的基础结构。

### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>创建链接自承载 IR 的概要步骤

1. 在要共享的自承载 IR 中，授予要在其中创建链接 IR 的数据工厂的权限。 

   ![“共享”选项卡上的授予权限按钮](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)

   ![分配权限的选项](media/create-self-hosted-integration-runtime/3_rbac_permissions.png)

2. 记下要共享的自承载 IR 的资源 ID。

   ![资源 ID 的位置](media/create-self-hosted-integration-runtime/4_ResourceID_self-hostedIR.png)

3. 在已授予权限的数据工厂中，创建新的自承载 IR（链接），并输入资源 ID。

   ![用于创建链接的自承载集成运行时的按钮](media/create-self-hosted-integration-runtime/6_create-linkedIR_2.png)

   ![用于输入名称和资源 ID 的框](media/create-self-hosted-integration-runtime/6_create-linkedIR_3.png)

### <a name="monitoring"></a>监视 

- **共享 IR**

  ![用于查找共享集成运行时的选项](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

  ![监视选项卡](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

- **链接 IR**

  ![用于查找链接集成运行时的选项](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

  ![监视选项卡](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>自承载 IR 共享的已知限制

* 要在其中创建链接 IR 的数据工厂必须包含一个 [MSI](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)。 默认情况下，在 Azure 门户或 PowerShell cmdlet 中创建的数据工厂已隐式创建 MSI。 但是，如果数据工厂是通过 Azure 资源管理器模板或 SDK 创建的，则必须显式设置 **Identity** 属性，以确保 Azure 资源管理器创建包含 MSI 的数据工厂。 

* 支持此功能的 Azure 数据工厂 .NET SDK 为 1.1.0 或更高版本。

* 若要授予权限，用户在共享 IR 所在的数据工厂中需要“所有者”角色或继承的“所有者”角色。

* 共享功能仅适用于同一 Azure Active Directory 租户中的数据工厂。

* 对于 Active Directory [来宾用户](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)，UI 中的搜索功能（使用搜索关键字列出所有数据工厂）[不起作用](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits)。 但只要来宾用户是数据工厂的“所有者”，则无需搜索功能也能共享 IR，方法是在“分配权限”文本框中直接键入需要共享 IR 的数据工厂的 MSI，然后在 Azure 数据工厂 UI 中选择“添加”。 

  > [!NOTE]
  > 此功能只能在 Azure 数据工厂 V2 中使用。 

## <a name="notification-area-icons-and-notifications"></a>通知区域图标和通知

如果将光标移到通知区域中的图标或消息上，可以查看自承载集成运行时状态的详细信息。

![通知区域中的通知](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewall"></a>端口和防火墙
需要考虑两个防火墙：在组织的中央路由器上运行的企业防火墙和在安装了自承载集成运行时的本地计算机上配置为守护程序的 Windows 防火墙。

![防火墙](media/create-self-hosted-integration-runtime/firewall.png)

在企业防火墙级别，需配置以下域和出站端口：

域名 | 端口 | 描述
------------ | ----- | ------------
*.servicebus.windows.net | 443 | 用来与后端数据移动服务通信
*.core.windows.net | 443 | 用于通过 Azure Blob 存储（如果已配置）进行临时复制
*.frontend.clouddatahub.net | 443 | 用来与后端数据移动服务通信
download.microsoft.com | 443 | 用于下载更新

在 Windows 防火墙级别（计算机级别），通常已启用这些出站端口。 如果没有，可以在自承载集成运行时计算机上相应地配置域和端口。

> [!NOTE]
> 根据源和接收器，可能需要在企业防火墙或 Windows 防火墙中将其他域和出站端口加入允许列表。
>
> 对于部分云数据库（例如 Azure SQL 数据库和 Azure Data Lake），可能需要在其防火墙配置中将自承载集成运行时计算机的 IP 地址列入允许列表。

### <a name="copy-data-from-a-source-to-a-sink"></a>将数据从源复制到接收器
确保在企业防火墙、自承载集成运行时计算机上的 Windows 防火墙和数据存储上正确启用防火墙规则。 启用这些规则可以让自承载集成运行时成功连接到源和接收器。 为复制操作涉及的每个数据存储启用规则。

例如，若要从本地数据存储复制到 Azure SQL 数据库接收器或 Azure SQL 数据仓库接收器，请执行以下步骤：

1. 对于 Windows 防火墙和企业防火墙，允许 1433 端口上的出站 TCP 通信。
2. 配置 Azure SQL 数据库的防火墙设置，以将自承载集成运行时计算机的 IP 地址添加到允许的 IP 地址列表。

> [!NOTE]
> 如果防火墙不允许出站端口 1433，则自承载集成运行时无法直接访问 Azure SQL 数据库。 在这种情况下，可对 Azure SQL 数据库和 Azure SQL 数据仓库使用[临时复制](copy-activity-performance.md)。 对于此方案，只需将 HTTPS（端口 443）用于数据移动。


## <a name="proxy-server-considerations"></a>代理服务器注意事项
如果企业网络环境使用代理服务器访问 Internet，请配置自承载集成运行时以使用合适的代理设置。 可以在初始注册阶段设置代理。

![指定代理](media/create-self-hosted-integration-runtime/specify-proxy.png)

配置后，自承载集成运行时使用代理服务器连接到云服务、源/目标（使用 HTTP/HTTPS 协议的源/目标）。 在初始设置期间选择“更改”链接。 此时会出现代理设置对话框。

![设置代理](media/create-self-hosted-integration-runtime/set-http-proxy.png)

有三个配置选项：

- **不使用代理**：自承载集成运行时不显式使用任何代理来连接到云服务。
- **使用系统代理**：自承载集成运行时使用在 diahost.exe.config 和 diawp.exe.config 中配置的代理设置。如果 diahost.exe.config 和 diawp.exe.config 中未配置代理，则自承载集成运行时无需通过代理，可直接连接到云服务。
- **使用自定义代理**：配置用于自承载集成运行时的 HTTP 代理设置，而不使用 diahost.exe.config 和 diawp.exe.config 中的配置。必须输入“地址”和“端口”。 “用户名”和“密码”是可选的，具体取决于代理的身份验证设置。 所有设置都使用 Windows DPAPI 在自承载集成运行时进行加密，并存储在本地计算机上。

保存更新的代理设置之后，集成运行时主机服务会自动重启。

成功注册自承载集成运行时后，如果想要查看或更新代理设置，请使用 Integration Runtime Configuration Manager。

1. 打开“Microsoft Integration Runtime Configuration Manager”。
2. 切换到“设置”选项卡。
3. 在“HTTP 代理”部分选择“更改”链接，打开“设置 HTTP 代理”对话框。
4. 选择“**下一步**”。 此时会出现警告，询问是否允许保存代理设置和重启集成运行时主机服务。

可以使用 Configuration Manager 工具查看和更新 HTTP 代理。

![查看代理](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> 如果使用 NTLM 身份验证设置代理服务器，集成运行时主机服务会在域帐户下运行。 如果之后更改域帐户密码，请记得更新服务的配置设置，并相应地将其重启。 鉴于此要求，建议使用专用域帐户来访问代理服务器，可以无需经常更新密码。

### <a name="configure-proxy-server-settings"></a>配置代理服务器设置

如果为 HTTP 代理选择“使用系统代理”设置，则自承载集成运行时使用 diahost.exe.config 和 diawp.exe.config 中的代理设置。如果 diahost.exe.config 和 diawp.exe.config 中未指定代理，则自承载集成运行时无需通过代理，可直接连接到云服务。 以下过程说明如何更新 diahost.exe.config 文件：

1. 在文件资源管理器中，生成 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config 的安全副本，以备份原始文件。
2. 打开以管理员身份运行的 Notepad.exe，并打开文本文件 C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config。找到 system.net 的默认标记，如以下代码中所示：

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

    允许在代理标记中使用其他属性，以指定所需设置（如 `scriptLocation`）。 关于语法，请参阅[代理元素（网络设置）](https://msdn.microsoft.com/library/sa91de1e.aspx)。

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. 将配置文件保存在原始位置。 然后重启自承载集成运行时主机服务，以拾取更改。 

   若要重启服务，请从控制面板使用服务小程序。 或在“Integration Runtime Configuration Manager”中依次选择“停止服务”按钮和“启动服务”。 
   
   如果服务未启动，很可能是将错误的 XML 标记语法添加到了编辑过的应用程序配置文件中。

> [!IMPORTANT]
> 不要忘记同时更新 diahost.exe.config 和 diawp.exe.config。

还需要确保 Microsoft Azure 列于公司的白名单中。 可以从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=41653)下载有效的 Microsoft Azure IP 地址列表。

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>防火墙和代理服务器相关问题的可能症状
如果遇到类似于以下的错误，可能是由于防火墙或代理服务器配置错误，阻止了自承载集成运行时连接到数据工厂进行自身身份验证。 若要确保正确配置防火墙和代理服务器，请参阅上一部分。

* 尝试注册自承载集成运行时的时候，会收到以下错误：“无法注册此 Integration Runtime 节点！ 请确认身份验证密钥有效，且集成服务主机服务在此计算机上运行。”
* 打开 Integration Runtime Configuration Manager 时，将看到状态为“已断开连接”或“正在连接”。 查看 Windows 事件日志时，在“事件查看器” > “应用程序和服务日志” > “Microsoft Integration Runtime”下，会看到错误消息，例如以下错误：

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enabling-remote-access-from-an-intranet"></a>从 Intranet 启用远程访问  
如果使用 PowerShell 加密网络中未安装自承载集成运行时的另一台计算机上的凭据，则可以启用“从 Intranet 进行远程访问”选项。 如果运行 PowerShell 来加密已安装自承载集成运行时的同一台计算机上的凭据，则无法启用“从 Intranet 进行远程访问”。

在添加另一个节点以实现高可用性和可伸缩性之前，应启用“从 Intranet 进行远程访问”。  

在自承载集成运行时安装（版本 3.3.xxxx.x 以上）期间，默认情况下，自承载集成运行时安装将在自承载集成运行时计算机上禁用“从 Intranet 进行远程访问”。

如果使用的是第三方防火墙，则可以手动打开端口 8060（或用户配置的端口）。 如果在安装自承载集成运行时期间防火墙出现问题，请尝试使用以下命令在不配置防火墙的情况下安装自承载集成运行时。

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

如果选择不打开自承载集成运行时计算机上的端口 8060，请使用除“设置凭据”应用程序以外的机制来配置数据存储凭据。 例如，可以使用 **New-AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell cmdlet。


## <a name="next-steps"></a>后续步骤
有关分步说明，请参阅以下教程：[教程：将本地数据复制到云中](tutorial-hybrid-copy-powershell.md)。
