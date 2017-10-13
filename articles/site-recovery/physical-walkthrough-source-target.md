---
title: "设置源和目标以便使用 Azure Site Recovery 将物理服务器复制到 Azure | Microsoft Docs"
description: "总结了如何设置源和目标，以便使用 Azure Site Recovery 服务将物理服务器复制到 Azure 存储"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 2e3d03bc-4e53-4590-8a01-f702d3cc9500
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: e89bbf5a2c1d71852e49da43d3106a05ebfc28a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-set-up-the-source-and-target-for-physical-server-replication-to-azure"></a>第 7 步：设置源和目标以便将物理服务器复制到 Azure

本文介绍了如何配置源和目标设置，以便在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地物理服务器复制到 Azure。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="set-up-the-source-environment"></a>设置源环境

创建配置服务器，将它注册到保管库中，并发现计算机。

1. 单击“Site Recovery” > “步骤 1: 准备基础结构” > “源”。
2. 如果没有配置服务器，请单击“+ 配置服务器”。
3. 在“添加服务器”中，检查“配置服务器”是否已显示在“服务器类型”中。
4. 下载站点恢复统一安装程序安装文件。
5. 下载保管库注册密钥。 运行统一安装程序时需要用到此密钥。 生成的密钥有效期为 5 天。

   ![设置源](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>在保管库中注册配置服务器

在开始之前，执行以下操作，并运行统一安装程序来安装配置服务器、进程服务器和主目标服务器。 视频介绍了如何设置组件，以便将 VMware VM 复制到 Azure。 不过，此过程对将物理服务器复制到 Azure 同样有效。

- 在配置服务器 VM 上，确保将系统时钟与[时间服务器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)进行同步。 它应与之匹配。 如果它提前或落后 15 分钟，安装程序可能会失败。
- 在配置服务器计算机上以本地管理员身份运行安装程序。
- 确保在 VM 上启用了 TLS 1.0。


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 还可以[通过命令行](http://aka.ms/installconfigsrv)安装配置服务器。




## <a name="set-up-the-target-environment"></a>设置目标环境

设置目标环境前，请确保已创建 Azure 存储帐户和虚拟网络。

1. 单击“准备基础结构” > “目标”，并选择要使用的 Azure 订阅。
2. 指定目标部署模型是基于 Resource Manager 的还是经典的。
3. Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。

   ![目标](./media/physical-walkthrough-source-target/gs-target.png)

4. 如果尚未创建存储帐户或网络，请单击“+ 存储帐户”或“+ 网络”，创建 Resource Manager 帐户或内联网络。

## <a name="next-steps"></a>后续步骤

转到[第 8 步：创建复制策略](physical-walkthrough-replication.md)
