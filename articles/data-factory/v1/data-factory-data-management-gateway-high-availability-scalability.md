---
title: "Azure 数据工厂中数据管理网关的高可用性 | Microsoft Docs"
description: "本文介绍如何通过添加更多节点向外扩展数据管理网关，以及如何通过增加可在节点上运行的并发作业数纵向扩展数据管理网关。"
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: abnarain
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 1aac856d154724e3dcd282e2d34c27571cd1cb02
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>数据管理网关 - 高可用性和可伸缩性（预览）
本文有助于对数据管理网关配置高可用性和可伸缩性解决方案。    

> [!NOTE]
> 本文假定你已熟悉数据管理网关的基础知识。 如不熟悉，请参阅[数据管理网关](data-factory-data-management-gateway.md)。

>**数据管理网关版本 2.12.xxxx.x 及更高版本正式支持此预览功能**。 请确保你使用的是版本 2.12.xxxx.x 或更高版本。 在[此处](https://www.microsoft.com/download/details.aspx?id=39717)下载最新版本的数据管理网关。

## <a name="overview"></a>概述
可将安装于多台本地计算机上的数据管理网关与门户中的单个逻辑网关相关联。 这些计算机称为节点。 至多可将 4 个节点与一个逻辑网关相关联。 一个逻辑网关配多个节点（已安装网关的本地计算机）的好处如下：  

- 本地和云数据存储之间的数据移动性能提高。  
- 如果某个节点出于某种原因发生故障，则其他节点仍可用于移动数据。 
- 如果某个节点需要脱机进行维护，则其他节点仍可用于移动数据。

还可配置可在节点上运行的并发数据移动作业数，提升本地与云数据存储之间移动数据的能力。 

通过使用 Azure 门户，可以监视这些节点的状态，有助于决定是否要从逻辑网关添加或删除节点。 

## <a name="architecture"></a>体系结构 
下图概述了数据管理网关的可伸缩性和可用性功能的体系结构： 

![数据管理网关 - 高可用性和可伸缩性](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

逻辑网关是向 Azure 门户中数据工厂添加的网关。 之前，只能将逻辑网关与一台已安装数据管理网关的本地 Windows 计算机相关联。 这个本地网关计算机被称为节点。 现在，可至多将 4 个物理节点与一个逻辑网关相关联。 包含多个节点的逻辑网关被称为多节点网关。  

所有这些节点均处于活动状态。 它们均可处理数据移动作业，在本地和云数据存储间移动数据。 其中一个节点同时充当调度程序和辅助角色。 组中的其他节点作为辅助角色节点。 调度程序节点从云服务请求数据移动任务/作业，然后将其分派给辅助角色节点（包括其本身）。 辅助角色节点执行数据移动作业，在本地和云数据存储间移动数据。 所有节点均为辅助角色。 只有一个节点可同时作为调度程序和辅助角色。    

通常以一个节点开始，然后随现有节点无力处理数据移动负载而逐渐扩展添加更多节点。 还可通过增加节点上允许运行的并发作业数来增强网关节点的数据移动能力。 此功能也适用于单节点网关（即便未启用可伸缩性和可用性功能）。 

包含多个节点的网关可在所有节点间使数据存储凭据保持同步。 如果出现节点到节点的连接问题，则凭据可能不同步。为使用网关的本地数据存储设置凭据时，将凭据保存于调度程序/辅助角色节点中。 调度程序节点与其他辅助角色节点同步。 这个过程称为凭据同步。节点间的信道可通过公共 SSL/TLS 证书进行加密。 

## <a name="set-up-a-multi-node-gateway"></a>设置多节点网关
本部分假定你已浏览下面两篇文章或熟悉这些文章中的概念： 

- [数据管理网关](data-factory-data-management-gateway.md) - 提供对网关的详细概述。
- [在本地和云数据存储间移动数据](data-factory-move-data-between-onprem-and-cloud.md) - 包含使用单节点网关的分布说明演练。  

> [!NOTE]
> 在本地 Windows 计算机上安装数据管理网关之前，请参阅[重点文章](data-factory-data-management-gateway.md#prerequisites)部分中列出的先决条件。

1. 在[演练](data-factory-move-data-between-onprem-and-cloud.md#create-gateway)中创建逻辑网关时，请启用高可用性和可伸缩性功能。 

    ![数据管理网关 - 启用高可用性和可伸缩性](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. 在“配置”页中使用“快速安装”或“手动安装”链接，在第一个节点（本地 Windows 计算机）上安装网关。

    ![数据管理网关 - 快速安装或手动安装](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > 如果使用快速安装选项，则在未加密的情况下进行节点到节点通信。 节点名与计算机名相同。 如果需要加密节点到节点的通信或想要指定自己的节点名，请使用手动安装。 稍后不能编辑节点名称。
3. 如果选择快速安装
    1. 网关成功安装后，会看到以下消息：

        ![数据管理网关 - 快速安装成功](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. 按照[这些说明](data-factory-data-management-gateway.md#configuration-manager)启动网关的数据管理配置管理器。 将看到网关名称、节点名称、状态等。

        ![数据管理网关 - 安装成功](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. 如果选择手动安装：
    1. 从 Microsoft 下载中心下载安装包，然后运行它以在计算机上安装网关。
    2. 在“配置”页中使用身份验证密钥注册网关。
    
        ![数据管理网关 - 安装成功](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. 在“新建网关节点”页中，可为网关节点提供自定义名称。 默认情况下，节点名与计算机名相同。    

        ![数据管理网关 - 指定名称](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. 在下一页中，可以选择是否“对节点到节点的通信启用加密”。 单击“跳过”禁用加密（默认）。

        ![数据管理网关 - 启用加密](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > 仅当逻辑网关中只有一个网关节点时支持更改加密模式。 若要在网关包含多个节点时更改加密模式，请执行以下步骤：保留一个节点、删除其他所有节点，然后更改加密模式，继而再次添加这些节点。
        > 
        > 关于使用 TLS/SSL 证书的要求的列表，请参阅 [TLS/SSL 证书要求](#tlsssl-certificate-requirements)。 
    5. 网关安装成功后，单击“启动配置管理器”：
    
        ![手动安装 - 启动配置管理器](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)   
    6. 将在节点（本地 Windows 计算机）中看到数据管理网关配置管理器，它显示连接状态、网关名称和节点名称。  

        ![数据管理网关 - 安装成功](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > 如果要在 Azure VM 上预配网关，可以使用[此 Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway)。 此脚本创建逻辑网关，设置安装有数据管理网关软件的 VM，并将它们注册到逻辑网关。 
6. 在 Azure 门户中启动“网关”页： 
    1. 在门户的数据工厂主页上单击“链接的服务”。
    
        ![数据工厂主页](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. 选择“网关”查看“网关”页：
    
        ![数据工厂主页](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. 将看到“网关”页：   

        ![单节点网关视图](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. 单击工具栏上的“添加节点”，向逻辑网关添加一个节点。 如果计划使用快速安装，请从将作为节点添加到网关的本地计算机上执行此步骤。 

    ![数据管理网关 - 添加节点菜单](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. 步骤与设置第一个节点类似。 如果选择手动安装选项，配置管理器 UI 将允许设置节点名称： 

    ![配置管理器 - 安装第二个网关](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. 在节点上成功安装网关后，配置管理器工具将显示以下屏幕：  

    ![配置管理器 - 成功安装第二个网关](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. 如果在门户中打开“网关”页，现将看到两个网关节点： 

    ![门户中的两节点网关](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. 若要删除网关节点，请单击工具栏上的“删除节点”，选择要删除的节点，然后在工具栏中单击“删除”。 此操作将从组中删除所选节点。 请注意，此操作不会从节点（本地 Windows 计算机）删除数据管理网关软件。 在本地计算机的“控制面板”中通过“添加或删除程序”即可卸载网关。 从节点卸载网关时，将在门户中自动删除它。   

## <a name="upgrade-an-existing-gateway"></a>升级现有网关
可升级现有网关，以使用高可用性和可伸缩性功能。 此功能仅适用于安装有 2.12.xxxx 及更高版本的数据管理网关的节点。 可在数据管理网关配置管理器的“帮助”选项卡中查看计算机上安装的数据管理网关版本。 

1. 可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=39717)下载并运行 MSI 安装包，将本地计算机上的网关升级为最新版。 有关详细信息，请参阅[安装](data-factory-data-management-gateway.md#installation)部分。  
2. 导航到 Azure 门户。 启动数据工厂的“数据工厂”页。 单击“链接的服务”磁贴以启动“链接的服务”页。 选择网关以启动“网关”页。 单击并启用“预览功能”，如下图所示： 

    ![数据管理网关 - 启用预览功能](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. 在门户中启用预览功能后，请关闭所有页面。 重新打开“网关”页即可查看新的预览用户界面 (UI)。
 
    ![数据管理网关 - 成功启用预览功能](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![数据管理网关 - 预览 UI](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > 升级期间，第一个节点的名称为计算机的名称。 
3. 现添加一个节点。 在“网关”页中单击“添加节点”。  

    ![数据管理网关 - 添加节点菜单](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    按照上一节中的说明来设置节点。 

### <a name="installation-best-practices"></a>安装最佳做法

- 在主机上为网关配置电源计划，从而让计算机无法休眠。 如果主机计算机进入休眠状态，则网关不响应数据请求。
- 备份与网关关联的证书。
- 确保所有节点的配置类似（建议），以获得理想性能。 
- 添加至少两个节点来确保高可用性。  

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 证书要求
下面是用于保护网关节点间通信的 TLS/SSL 证书要求：

- 证书必须是公共可信的 X509 v3 证书。
- 所有网关节点必须都信任此证书。 
- 建议使用公共（即第三方）证书颁发机构 (CA) 颁发的证书。
- 针对 SSL 证书支持受 Windows Server 2012 R2 支持的任何密钥大小。
- 不支持使用 CNG 密钥的证书。
- 支持通配符证书。 


## <a name="monitor-a-multi-node-gateway"></a>监视多节点网关
### <a name="multi-node-gateway-monitoring"></a>多节点网关监视
在 Azure 门户中，可以查看每个节点上资源使用情况（CPU、内存、网络（进/出）等）的近乎实时的快照以及网关节点状态。 

![数据管理网关 - 多节点监视](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

可在“网关”页中启用“高级设置”以查看网络（进/出）、角色和凭据状态等高级指标，这有助于调试网关问题以及并发作业数（运行中/上限），可在性能优化期间相应地更改并发作业数。 下表介绍“网关节点”列表中的列：  

监视属性 | 说明
:------------------ | :---------- 
名称 | 逻辑网关和与网关关联的节点的名称。  
状态 | 逻辑网关和网关节点的状态。 示例：联机/脱机/受限等。有关这些状态的信息，请参阅[网关状态](#gateway-status)部分。 
版本 | 显示逻辑网关和每个网关节点的版本。 逻辑网关的版本根据组中多数节点的版本而决定。 如果逻辑网关安装程序中的节点版本不同，只有与逻辑网关的版本号相同的节点能正常运行。 其他节点将处于受限模式，需要手动进行更新（仅当自动更新失败时）。 
可用内存 | 网关节点上的可用内存。 此值为近实时快照。 
CPU 使用率 | 网关节点的 CPU 使用率。 此值为近实时快照。 
网络（进/出） | 网关节点的网络利用率。 此值为近实时快照。 
并发作业数（运行中/上限） | 每个节点上运行的作业或任务数。 此值为近实时快照。 上限表示每个节点的最大并发作业数。 此值根据计算机大小定义而来。 在 CPU/内存/网络未充分利用，但活动即将超时的高级方案中，可提高上限来增强并发作业执行。此功能也适用于单节点网关（即便未启用可伸缩性和可用性功能）。 有关详细信息，请参阅[扩展注意事项](#scale-considerations)部分。 
角色 | 角色有两种类型：调度程序和辅助角色。 所有节点均为辅助角色，表示它们可用于执行作业。 只有一个调度程序节点，用于从云服务中请求任务/作业，并分派到其他辅助节点（包括其本身）。 

![数据管理网关 - 高级多节点监视](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>网关状态

下表提供网关节点可能的状态： 

状态  | 注释/方案
:------- | :------------------
联机 | 节点连接到数据工厂服务。
脱机 | 节点处于脱机状态。
正在升级 | 节点正在进行自动更新。
受限制 | 由于连接问题而受限。 可能由于 HTTP 端口 8050 问题、服务总线连接问题或凭据同步问题而受限。 
非活动 | 节点的配置与其他多数节点的配置不同。<br/><br/> 节点在无法与其他节点连接时可能处于非活动状态。 


下表提供逻辑网关可能的状态。 网关状态取决于网关节点的状态。 

状态 | 注释
:----- | :-------
需注册 | 尚未向此逻辑网关注册任何节点
联机 | 网关节点处于联机状态
脱机 | 没有节点处于联机状态。
受限制 | 此网关中并非所有节点都处于运行正常状态。 此状态是部分节点可能出现故障的警告！ <br/><br/>可能是由于调度程序/辅助角色节点上的凭据同步问题所导致。 

### <a name="pipeline-activities-monitoring"></a>管道/活动监视
Azure 门户提供具有粒度节点级别详情的管道监视体验。 例如显示每个节点上运行的活动。 此信息有助于了解特定节点上的性能问题，例如因网络限制而引起的。 

![数据管理网关 - 针对管道的多节点监视](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![数据管理网关 - 管道详细信息](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>扩展注意事项

### <a name="scale-out"></a>向外扩展
如果“可用内存较低”且“CPU 使用量较高”，添加新节点有助于跨计算机提高负载。 如果活动因超时或网关节点处于脱机状态而失败，则向网关添加节点会有所作用。
 
### <a name="scale-up"></a>纵向扩展
如果可用内存和 CPU 未充分利用，但空闲容量为 0，应通过增加节点上可运行的并发作业数进行纵向扩展。 此外，活动因网关重载而超时时，也建议向上扩展。 如下图所示，可以增加节点的最大容量。 建议从加倍开始。  

![数据管理网关 - 扩展注意事项](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>已知问题/重大更改

- 目前，单个逻辑网关最多可有 4 个物理网关节点。 如果出于性能原因需要 4 个以上节点，请发送电子邮件至 [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com)。
- 不能使用其他逻辑网关中的身份验证密钥重新注册网关节点以从当前逻辑网关进行切换。 若要重新注册，请从节点中卸载网关，重新安装网关，并使用适用于其他逻辑网关的身份验证密钥进行注册。 
- 如果所有网关节点均需 HTTP 代理，请在 diahost.exe.config 和 diawp.exe.config 中设置代理，并使用服务器管理器确保所有节点具有相同的 diahost.exe.config 和 diawip.exe.config。有关详细信息，请参阅[配置代理设置](data-factory-data-management-gateway.md#configure-proxy-server-settings)部分。 
- 若要在网关配置管理器中更改节点到节点通信的加密模式，请在门户中保留一个节点并删除其他所有节点。 然后，在更改加密模式后重新添加节点。
- 如果选择加密节点到节点信道，请使用正式的 SSL 证书。 自签名证书可能导致连接问题，因为相同的证书在其他计算机上的证书验证机构中可能不受信任。 
- 如果节点版本低于逻辑网关版本，则不能向逻辑网关注册网关节点。 从门户中删除逻辑网关的所有节点，以便可以注册较低版本节点（降级）。 如果删除逻辑网关的所有节点，请向该逻辑网关手动安装并注册新节点。 在这种情况下，不支持快速安装。
- 快速安装不能用于向现有逻辑网关安装节点，该网关仍使用云凭据。 可在“设置”选项卡上查看网关配置管理器中存储凭据的位置。
- 快速安装不能用于向现有逻辑网关安装节点，该网关已启用节点到节点加密。 由于设置加密模式涉及手动添加证书，因此不能选择快速安装。 
- 对于本地环境中的文件副本，不得再使用 \\localhost 或 C:\files，因为 localhost 或本地驱动器可能无法通过所有节点进行访问。 请改用 \\ServerName\files 指定文件的位置。


## <a name="rolling-back-from-the-preview"></a>从预览版回退 
若要从预览版回退，请保留一个节点、删除所有其他节点。 删除哪个节点并不重要，但需确保逻辑网关中至少有一个节点。 可通过在计算机上卸载网关或使用 Azure 门户来删除节点。 在 Azure 门户中的“数据工厂”页，单击“链接的服务”以启动“链接的服务”页。 选择网关以启动“网关”页。 在“网关”页中，可以查看与网关关联的节点。 该页面将允许从网关删除节点。
 
删除后，在相同的 Azure 门户页中单击“预览版功能”，然后禁用预览版功能。 已将网关重置为 GA（正式发布）单节点网关。


## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [数据管理网关](data-factory-data-management-gateway.md) - 提供对网关的详细概述。
- [在本地和云数据存储间移动数据](data-factory-move-data-between-onprem-and-cloud.md) - 包含使用单节点网关的分布说明演练。 

