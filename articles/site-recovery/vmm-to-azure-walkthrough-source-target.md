---
title: "设置源和目标以使用 Azure Site Recovery 将 Hyper-V 复制到 Azure（包含 System Center VMM）| Microsoft Docs"
description: "概述设置源和目标的步骤，这些设置用于通过 Azure Site Recovery 将 VMM 云中 Hyper-V VM 复制到 Azure 存储"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5edb6d87-25a5-40fe-b6f1-ddf7b55a6b31
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.openlocfilehash: c72f839d0a1288dccb7deb3e44fc2b20d64818f0
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="step-8-set-up-the-source-and-target-for-hyper-v-with-vmm-replication-to-azure"></a>步骤 8：复制源和目标以将 Hyper-V（包含 VMM）复制到 Azure

[创建保管库](vmm-to-azure-walkthrough-create-vault.md)并指定要复制的内容之后，请使用本文配置源和目标设置，在 Azure 门户中通过 [Azure Site Recovery](site-recovery-overview.md) 服务将 System Center Virtual Machine Manager (VMM) 云中的本地 Hyper-V 虚拟机复制到 Azure 时需使用这些设置。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="set-up-the-source-environment"></a>设置源环境

S1. 单击“准备基础结构” > “源”。

    ![Set up source](./media/vmm-to-azure-walkthrough-source-target/set-source1.png)

2. 在“准备源”中单击“+VMM”以添加 VMM 服务器。

    ![设置源](./media/vmm-to-azure-walkthrough-source-target/set-source2.png)

3. 在“添加服务器”中，检查“System Center VMM 服务器”是否出现在“服务器类型”中，以及 VMM 服务器是否符合[先决条件和 URL 要求](#prerequisites)。
4. 下载 Azure Site Recovery 提供程序安装文件。
5. 下载注册密钥。 运行安装程序时需要用到此密钥。 生成的密钥有效期为 5 天。

    ![设置源](./media/vmm-to-azure-walkthrough-source-target/set-source3.png)

## <a name="install-the-provider-on-the-vmm-server"></a>安装 VMM 服务器上的提供程序

1. 在 VMM 服务器上运行提供程序安装文件。
2. 在“Microsoft 更新” 中，可以选择进行更新，以便根据 Microsoft 更新策略安装提供程序更新。
3. 在“安装”中接受或修改默认提供程序安装位置，并单击“安装”。

    ![安装位置](./media/vmm-to-azure-walkthrough-source-target/provider2.png)
4. 安装完成后，单击“注册”在保管库中注册 VMM 服务器。
5. 在“保管库设置”页中，单击“浏览”以选择保管库密钥文件。 指定 Azure Site Recovery 订阅和保管库名称。

    ![服务器注册](./media/vmm-to-azure-walkthrough-source-target/provider10.png)
6. 在“Internet 连接”中，指定在 VMM 服务器上运行的提供程序如何通过 Internet 连接到站点恢复。

   * 如果希望提供程序直接进行连接，请选择“不使用代理直接连接 Azure Site Recovery”。
   * 如果现有代理要求身份验证，或者想要使用自定义代理，请选择“使用代理服务器连接 Azure Site Recovery”。
   * 如果使用自定义代理，请指定地址、端口和凭据。
   * 如果使用代理，应事先允许[先决条件](#on-premises-prerequisites)中所述的 URL。
   * 如果使用自定义代理，则将使用指定的代理凭据自动创建一个 VMM 运行身份帐户 (DRAProxyAccount)。 对代理服务器进行配置以便该帐户可以成功通过身份验证。 可以在 VMM 控制台中修改 VMM 运行身份帐户设置。 在“设置”中，展开“安全性” > “运行方式帐户”，并修改 DRAProxyAccount 的密码。 需要重新启动 VMM 服务以使此设置生效。

     ![Internet](./media/vmm-to-azure-walkthrough-source-target/provider13.png)
7. 接受或修改用于保存为数据加密自动生成的 SSL 证书的位置。 如果在 Azure Site Recovery 门户中为受 Azure 保护的云启用数据加密，则会使用此证书。 请确保该证书安全。 运行故障转移到 Azure 时，如果已启用数据加密，需要使用该证书来解密。
8. 在“服务器名称”中，指定一个友好名称以在保管库中标识该 VMM 服务器。 在群集配置中，指定 VMM 群集角色名称。
9. 如果要将 VMM 服务器上所有云的元数据与保管库同步，请启用“同步云元数据”。 此操作在每个服务器上只需执行一次。 如果不希望同步所有云，可以将此设置保留为未选中状态并在 VMM 控制台中的云属性中分别同步各个云。 单击“注册”完成此过程。

    ![服务器注册](./media/vmm-to-azure-walkthrough-source-target/provider16.png)
10. 注册随即开始。 注册完成后，服务器会显示在“Site Recovery 基础结构” > “VMM 服务器”中。


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>在 Hyper-V 主机上安装 Azure 恢复服务代理

1. 设置提供程序之后，需要下载 Azure 恢复服务代理的安装文件。 在 VMM 云中的每个 Hyper-V 服务器上运行安装程序。

    ![Hyper-V 站点](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent1.png)
2. 在“先决条件检查”中，单击“下一步”。 将自动安装任何缺少的必备组件。

    ![恢复服务代理必备组件](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent2.png)
3. 在“安装设置”中，接受或修改安装位置和缓存位置。 可以在至少有 5 GB 可用存储空间的驱动器上配置缓存，但我们建议缓存驱动器有 600 GB 或更多可用空间。 然后单击“安装”。
4. 安装完成后，单击“关闭”完成操作。

    ![注册 MARS 代理](./media/vmm-to-azure-walkthrough-source-target/hyperv-agent3.png)

### <a name="command-line-installation"></a>命令行安装
可以使用以下命令从命令行安装 Microsoft Azure 恢复服务代理：

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>设置从 Hyper-V 主机到站点恢复的 Internet 代理访问权限

Hyper-V 主机上运行的恢复服务代理需有权通过 Internet 访问 Azure 才能进行 VM 复制。 如果通过代理访问 Internet，请按如下所示进行设置：

1. 在 Hyper-V 主机上打开 Microsoft Azure 备份 MMC 管理单元。 默认情况下，Microsoft Azure 备份的快捷方式位于桌面上或 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin 中。
2. 在管理单元中，单击“更改属性”。
3. 在“代理配置”选项卡上指定代理服务器信息。

    ![注册 MARS 代理](./media/vmm-to-azure-walkthrough-source-target/mars-proxy.png)
4. 检查确认代理可以访问[先决条件](#on-premises-prerequisites)中所述的 URL。

## <a name="set-up-the-target-environment"></a>设置目标环境
指定要用于复制的 Azure 存储帐户，以及 Azure VM 在故障转移后连接到的 Azure 网络。

1. 单击“准备基础结构” > “目标”，选择希望在其中创建故障转移虚拟机的订阅和资源组。 选择希望在 Azure 中为故障转移虚拟机使用的部署模型（经典或资源管理）。

    ![存储](./media/vmm-to-azure-walkthrough-source-target/enablerep3.png)

2. Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。

    ![存储](./media/vmm-to-azure-walkthrough-source-target/compatible-storage.png)

3. 如果尚未创建存储帐户并想使用资源管理器来创建一个，请单击“+存储帐户”以内联方式执行该操作。  在“创建存储帐户”边栏选项卡中，指定帐户名、类型、订阅和位置。 该帐户应位于与恢复服务保管库相同的位置。

   ![存储](./media/vmm-to-azure-walkthrough-source-target/gs-createstorage.png)


   * 如果想要使用经典模型创建存储帐户，请在 Azure 门户中执行该操作。 [了解详细信息](../storage/common/storage-create-storage-account.md)
   * 如果使用高级存储帐户保存复制的数据，请设置其他标准存储帐户来存储复制日志，这些日志将捕获本地数据正在发生的更改。
5. 如果尚未创建 Azure 网络并想使用资源管理器创建一个，请单击“+网络”以内联方式执行该操作。 在“创建虚拟网络”边栏选项卡上，指定网络名称、地址范围、子网详细信息、订阅和位置。 该网络应位于与恢复服务保管库相同的位置。

   ![网络](./media/vmm-to-azure-walkthrough-source-target/gs-createnetwork.png)

   如果想要使用经典模型创建网络，请在 Azure 门户中执行该操作。 [了解详细信息](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。





## <a name="next-steps"></a>后续步骤

转到[步骤 9：配置网络映射](vmm-to-azure-walkthrough-network-mapping.md)
