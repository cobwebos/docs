---
title: "使用 Azure Site Recovery 为到 Azure 的 Hyper-V 复制（无 System Center VMM）设置源和目标 | Microsoft Docs"
description: "总结了使用 Azure Site Recovery 将 Hyper-V VM 复制到 Azure 存储时用于设置源和目标设置的步骤"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d2010d85-77fd-4dea-84f3-1c960ed4c63f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: b38eb3a011d46f2239891ea1d1bcac2a4059a866
ms.contentlocale: zh-cn
ms.lasthandoff: 06/23/2017

---

# 步骤 8：为到 Azure 的 Hyper-V 复制设置源和目标
<a id="step-8-set-up-the-source-and-target-for-hyper-v-replication-to-azure" class="xliff"></a>

本文介绍了使用 Azure 门户中的 [Azure Site Recovery](site-recovery-overview.md) 服务将本地 Hyper-V 虚拟机（无 System Center VMM）复制到 Azure 时，如何配置源和目标设置。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## 设置源环境
<a id="set-up-the-source-environment" class="xliff"></a>

设置 Hyper-V 站点，在 Hyper-V 主机上安装 Azure Site Recovery 提供程序和 Azure 恢复服务代理，并在保管库中注册站点。

1. 在“准备基础结构”中，单击“源”。 若要添加新的 Hyper-V 站点作为 Hyper-V 主机或群集的容器，请单击“+Hyper-V 站点”。

    ![设置源](./media/hyper-v-site-walkthrough-source-target/set-source1.png)
2. 在“创建 Hyper-V 站点”中指定站点的名称。 。 现在，请选择已创建的站点，然后单击“+Hyper-V 服务器”向站点添加服务器。

    ![设置源](./media/hyper-v-site-walkthrough-source-target/set-source2.png)

3. 在“添加服务器” > “服务器类型”中，确保显示“Hyper-V 服务器”。

    - 确保要添加的 Hyper-V 服务器符合[先决条件](#on-premises-prerequisites)并且能够访问指定的 URL。
    - 下载 Azure Site Recovery 提供程序安装文件。 你将运行此文件，以在每个 Hyper-V 主机上安装提供程序和恢复服务代理。

    ![设置源](./media/hyper-v-site-walkthrough-source-target/set-source3.png)


## 安装提供程序和代理
<a id="install-the-provider-and-agent" class="xliff"></a>

1. 在添加到 Hyper-V 站点的每个主机上运行提供程序安装文件。 如果要在 Hyper-V 群集上进行安装，则在每个群集节点上运行安装程序。 通过安装并注册每个 Hyper-V 群集节点，可确保 VM 即使在节点之间迁移也会受到保护。
2. 在“Microsoft 更新” 中，可以选择进行更新，以便根据 Microsoft 更新策略安装提供程序更新。
3. 在“安装”中接受或修改默认提供程序安装位置，然后单击“安装”。
4. 在“保管库设置”中，单击“浏览”以选择所下载的保管库密钥文件。 指定 Azure Site Recovery 订阅、保管库名称和 Hyper-V 服务器属于的 Hyper-V 站点。

    ![服务器注册](./media/hyper-v-site-walkthrough-source-target/provider3.png)

5. 在“代理设置”中，指定 Hyper-V 主机上运行的提供程序如何通过 Internet 连接到 Azure Site Recovery。

    * 如果希望提供程序直接进行连接，请选择“不使用代理直接连接 Azure Site Recovery”。
    * 如果现有代理要求身份验证，或者你想要使用自定义代理进行填充程序连接，请选择“使用代理服务器连接 Azure Site Recovery”。
    * 如果使用代理，请执行以下操作：
        - 指定地址、端口和凭据
        - 确保允许通过代理访问[先决条件](#prerequisites)中所述的 URL。

    ![Internet](./media/hyper-v-site-walkthrough-source-target/provider7.png)

6. 安装完成后，单击“注册”，在保管库中注册服务器。

    ![安装位置](./media/hyper-v-site-walkthrough-source-target/provider2.png)

7. 完成注册后，Azure Site Recovery 将检索 Hyper-V 服务器中的元数据，该服务器显示在“Site Recovery 基础结构” > “Hyper-V 主机”中。


## 设置目标环境
<a id="set-up-the-target-environment" class="xliff"></a>

指定要用于复制的 Azure 存储帐户，以及 Azure VM 在故障转移后连接到的 Azure 网络。

1. 单击“准备基础结构” > “目标”。
2. 选择故障转移后要在其中创建 Azure VM 的订阅和资源组。 选择要在 Azure 中用于 VM 的部署模型（经典或资源管理）。

3. Site Recovery 将检查是否有一个或多个兼容的 Azure 存储帐户和网络。

    - 如果没有存储帐户，请单击“+存储”创建基于 Resource Manager 的内联帐户。 
    - 如果没有 Azure 网络，请单击“+网络”创建基于 Resource Manager 的内联网络。






## 后续步骤
<a id="next-steps" class="xliff"></a>

转到[步骤 9：设置复制策略](hyper-v-site-walkthrough-replication.md)

