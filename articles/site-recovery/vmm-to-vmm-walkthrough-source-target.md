---
title: "设置源和目标以使用 Azure Site Recovery 将 Hyper-V 复制到辅助站点 | Microsoft Docs"
description: "介绍如何在使用 Azure Site Recovery 将 Hyper-V VM 复制到辅助 VMM 站点时设置源和目标。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: fa7809f1-7633-425f-b25d-d10d004e8d0b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 07135e9b5e619971a59cc22ec68a0a4e8bcaabe1
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---
# <a name="step-6-set-up-the-replication-source-and-target"></a>步骤 6：设置复制源和目标


为使用 [Azure Site Recovery](site-recovery-overview.md) 将 Hyper-V 复制到辅助 VMM 站点创建恢复服务保管库后，根据本文设置源和目标复制位置。 

阅读本文后，请在底部发布评论，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。




## <a name="set-up-the-source-environment"></a>设置源环境

在 VMM 服务器上安装 Azure Site Recovery 提供程序，并在保管库中发现和注册这些服务器。

1. 单击“步骤 1: 准备基础结构” > “源”。

    ![设置源](./media/vmm-to-vmm-walkthrough-source-target/goals-source.png)
2. 在“准备源”中单击“+VMM”以添加 VMM 服务器。

    ![设置源](./media/vmm-to-vmm-walkthrough-source-target/set-source1.png)
3. 在“添加服务器”中，检查“System Center VMM 服务器”是否出现在“服务器类型”中，以及 VMM 服务器是否符合[先决条件](#prerequisites)。
4. 下载 Azure Site Recovery 提供程序安装文件。
5. 下载注册密钥。 运行安装程序时需要用到此密钥。 生成的密钥有效期为 5 天。

    ![设置源](./media/vmm-to-vmm-walkthrough-source-target/set-source3.png)
6. 在 VMM 服务器上安装 Azure Site Recovery 提供程序。 不需要在 Hyper-V 主机服务器上显式安装任何组件。


## <a name="install-the-azure-site-recovery-provider"></a>安装 Azure Site Recovery 提供程序

1. 在每个 VMM 服务器上运行提供程序安装文件。 如果在群集中部署 VMM，请在首次安装时执行以下操作：
    -  在活动节点上安装提供程序，并完成安装以在保管库中注册 VMM 服务器。
    - 然后在其他节点上安装该提供程序。 所有群集节点应运行相同版本的提供程序。
2. 安装程序将运行几项先决条件检查，并请求停止 VMM 服务的权限。 VMM 服务会在安装程序完成时自动重启。 如果在 VMM 群集上进行安装，系统会提示停止群集角色。
3. 在“Microsoft 更新”中，可选择指定根据 Microsoft 更新策略安装提供程序更新。
4. 在“安装”中接受或修改默认安装位置，并单击“安装”。

    ![安装位置](./media/vmm-to-vmm-walkthrough-source-target/provider-location.png)
5. 安装完成后，单击“注册”，以便在保管库中注册服务器。

    ![安装位置](./media/vmm-to-vmm-walkthrough-source-target/provider-register.png)
6. 在“保管库名称”中，验证将要在其中注册服务器的保管库的名称。 单击“下一步”。

    ![服务器注册](./media/vmm-to-vmm-walkthrough-source-target/vaultcred.png)
7. 在“Internet 连接”中，指定在 VMM 服务器上运行的提供程序如何连接到 Azure。

    ![Internet 设置](./media/vmm-to-vmm-walkthrough-source-target/proxydetails.png)

   - 可指定提供程序应直接连接到 Internet 还是通过代理连接。
   - 必要时可指定代理设置。
   - 如果使用代理，则系统将使用指定的代理凭据自动创建一个 VMM 运行方式帐户 (DRAProxyAccount)。 对代理服务器进行配置以便该帐户可以成功通过身份验证。 可通过 VMM 控制台 >“设置” > “安全” > “运行方式帐户”修改运行方式帐户设置。 重启 VMM 服务以更新更改。
8. 在“注册密钥”中，选择从 Azure Site Recovery 下载并复制到 VMM 服务器的密钥。
9. 仅要将 VMM 云中的 Hyper-V VM 复制到 Azure 时，才使用加密设置。 如果要复制到辅助站点，则不使用加密设置。
10. 在“服务器名称”中，指定一个友好名称以在保管库中标识该 VMM 服务器。 在群集配置中，请指定 VMM 群集角色名称。
11. 在“同步云元数据”中，选择是否要将 VMM 服务器上所有云的元数据与保管库进行同步。 此操作在每个服务器上只需执行一次。 如果不想要同步所有云，可以将此设置保留为未选中状态并在 VMM 控制台中的云属性中分别同步各个云。
12. 单击“下一步”以完成此过程。 注册后，Azure Site Recovery 将检索 VMM 服务器中的元数据。 服务器显示在保管库中“服务器”页上的“VMM 服务器”选项卡中。

    ![服务器](./media/vmm-to-vmm-walkthrough-source-target/provider13.png)
13. 当服务器出现在 Site Recovery 控制台中后，请在“源” > “准备源”中选择 VMM 服务器，并选择 Hyper-V 主机所在的云。 。

还可从命令行安装提供程序：

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>设置目标环境

选择目标 VMM 服务器和云：

1. 单击“准备基础结构” > “目标”，并选择要使用的目标 VMM 服务器。
2. 此时会显示该服务器上已与站点恢复同步的云。 选择目标云。

   ![目标](./media/vmm-to-vmm-walkthrough-source-target/target-vmm.png)



## <a name="next-steps"></a>后续步骤

转到[步骤 7：配置网络映射](vmm-to-vmm-walkthrough-network-mapping.md)。

