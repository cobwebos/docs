---
title: "设置源和目标以便使用 Azure Site Recovery 将 VMware 复制到 Azure | Microsoft Docs"
description: "总结了设置源和目标设置以便使用 Azure Site Recovery 将 VMware VM 复制到 Azure 存储的步骤"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99e422e-daf7-4fa8-af3c-af2340340136
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 94b629a62c3a54eee69ee397b2f27e3f20b753d5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="step-8-set-up-the-source-and-target-for-vmware-replication-to-azure"></a>步骤 8：设置源和目标以便将 VMware 复制到 Azure

本文介绍了在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地 VMware 虚拟机复制到 Azure 时，如何配置源和目标设置。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="set-up-the-source-environment"></a>设置源环境

设置配置服务器，将它注册到保管库中，并且发现 VM。

1. 单击“Site Recovery” > “步骤 1: 准备基础结构” > “源”。
2. 如果没有配置服务器，请单击“+ 配置服务器”。
3. 在“添加服务器”中，检查“配置服务器”是否已显示在“服务器类型”中。
4. 下载站点恢复统一安装程序安装文件。
5. 下载保管库注册密钥。 运行统一安装程序时需要用到此密钥。 生成的密钥有效期为 5 天。

   ![设置源](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>在保管库中注册配置服务器

在开始之前，执行以下操作，并运行统一安装程序来安装配置服务器、进程服务器和主目标服务器。
    - 获取短片概述

        > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

    - 在配置服务器 VM 上，确保将系统时钟与[时间服务器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)进行同步。 它应与之匹配。 如果它提前或落后 15 分钟，安装程序可能会失败。
    - 在配置服务器 VM 上以本地管理员身份运行安装程序。
    - 确保在 VM 上启用了 TLS 1.0。


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 还可以[通过命令行](http://aka.ms/installconfigsrv)安装配置服务器。



## <a name="connect-to-vmware-servers"></a>连接到 VMware 服务器

要允许 Azure Site Recovery 发现本地环境中运行的虚拟机，需要将 VMware vCenter 服务器或 vSphere ESXi 主机与 Site Recovery 连接。 开始之前，请注意以下问题：

- 如果在服务器上使用没有管理员权限的帐户将 vCenter 服务器或 vSphere 主机添加到 Site Recovery，该帐户需要启用以下权限：
    - 数据中心、数据存储、文件夹、主机、网络、资源、虚拟机、vSphere 分布式交换机。
    - vCenter 服务器需要“存储视图”权限。
- 将 VMware 服务器添加到 Site Recovery 之后，可能需要 15 分钟或更长时间，这些服务器才会显示在门户中。

### <a name="add-the-account-for-automatic-discovery"></a>添加用于自动发现的帐户

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="set-up-a-connection"></a>设置连接

连接到服务器，如下所示：

1. 选择“+vCenter”开始连接 VMware vCenter 服务器或 VMware vSphere ESXi 主机。
2. 在“添加 vCenter”中，指定 vSphere 主机或 vCenter 服务器的友好名称，并指定服务器的 IP 地址或 FQDN。
3. 除非已将 VMware 服务器配置为在不同的端口上侦听请求，否则请保留 443 作为端口号。 选择要连接到 VMware vCenter 或 vSphere ESXi 服务器的帐户。 单击 **“确定”**。
4. Site Recovery 将使用指定的设置连接到 VMware 服务器，并且将发现 VM。

> [!NOTE]
> 如果在 vCenter 或主机服务器上使用没有管理员权限的帐户添加服务器或主机，请确保为帐户启用了以下权限：数据中心、数据存储、文件夹、主机、网络、资源、虚拟机、vSphere 分布式交换机。 此外，VMware vCenter 服务器需要启用存储视图权限。


## <a name="set-up-the-target-environment"></a>设置目标环境

设置目标环境前，请确保已创建 Azure 存储帐户和虚拟网络。

1. 单击“准备基础结构” > “目标”，然后选择要使用的 Azure 订阅。
2. 指定目标部署模型是基于 Resource Manager 还是基于经典部署。
3. Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。

   ![目标](./media/vmware-walkthrough-source-target/gs-target.png)
4. 如果尚未创建存储帐户或网络，请单击“+ 存储帐户”或“+ 网络”，创建 Resource Manager 帐户或内联网络。

## <a name="next-steps"></a>后续步骤

转到[步骤 9：设置复制策略](vmware-walkthrough-replication.md)
