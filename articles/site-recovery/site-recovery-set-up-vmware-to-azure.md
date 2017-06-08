---
title: "设置源环境（VMware 到 Azure）| Microsoft 文档"
description: "本文介绍如何设置本地环境，以便开始将 VMware 虚拟机复制到 Azure。"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/05/2017
ms.author: anoopkv
ms.translationtype: Human Translation
ms.sourcegitcommit: eccc927550aef4c9cd71ffad64d2eddedb74326e
ms.openlocfilehash: 18627223bdb4f0986f07f17233ce7daf29cb7dd9
ms.contentlocale: zh-cn
ms.lasthandoff: 02/13/2017

---

# <a name="set-up-the-source-environment-vmware-to-azure"></a>设置源环境（VMware 到 Azure）
> [!div class="op_single_selector"]
> * [VMware 虚拟机](./site-recovery-set-up-vmware-to-azure.md)
> * [物理服务器](./site-recovery-set-up-physical-to-azure.md)

本文介绍如何设置本地环境，以便开始将 VMware 上运行的虚拟机复制到 Azure。

## <a name="prerequisites"></a>先决条件

本文假设你已创建：
- [Azure 门户](http://portal.azure.com "Azure 门户")中的恢复服务保管库。
- VMware vCenter 中可用于[自动发现](./site-recovery-vmware-to-azure.md#vmware-account-permissions)的专用帐户。
- 用于安装配置服务器的虚拟机。

## <a name="configuration-server-minimum-requirements"></a>配置服务器的最低要求
应将配置服务器软件部署到高度可用的 VMware 虚拟机。 下表列出了配置服务器的最低硬件、软件和网络要求。
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> 配置服务器不支持基于 HTTPS 的代理服务器。

## <a name="choose-your-protection-goals"></a>选择保护目标

1. 在 Azure 门户中，浏览到“恢复服务保管库”边栏选项卡，然后选择保管库。
2. 在保管库的资源菜单中，转到“开始使用” > “Site Recovery” > “步骤 1: 准备基础结构” > “保护目标”。

    ![选择目标](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. 在“保护目标”中选择“到 Azure”，然后选择“是，使用 VMware vSphere 虚拟机监控程序”。 。

    ![选择目标](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>设置源环境
设置源环境涉及两个主要活动：

- 使用 Site Recovery 安装和注册配置服务器。
- 通过将 Site Recovery 连接到本地 VMware vCenter 或 vSphere EXSi 主机来发现本地虚拟机。

### <a name="step-1-install-and-register-a-configuration-server"></a>步骤 1：安装和注册配置服务器

1. 单击“步骤 1: 准备基础结构” > “源”。 如果没有配置服务器，请在“准备源”中单击“+配置服务器”添加一个。

    ![设置源](./media/site-recovery-set-up-vmware-to-azure/set-source1.png)
2. 在“添加服务器”边栏选项卡中，检查“配置服务器”是否已出现在“服务器类型”中。
4. 下载站点恢复统一安装程序安装文件。
5. 下载保管库注册密钥。 运行统一安装程序时，需要注册密钥。 生成的密钥有效期为&5; 天。

    ![设置源](./media/site-recovery-set-up-vmware-to-azure/set-source2.png)
6. 在用作配置服务器的计算机上，运行 **Azure Site Recovery 统一安装程序**以安装配置服务器、进程服务器和主目标服务器。

#### <a name="run-azure-site-recovery-unified-setup"></a>运行 Azure Site Recovery 统一安装程序

> [!TIP]
> 如果计算机上的系统时钟时间与本地时间相差五分钟以上，则配置服务器注册将失败。 开始安装前，请将系统时钟与[时间服务器](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)同步。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 可以通过命令行安装配置服务器。 有关详细信息，请参阅[使用命令行工具安装配置服务器](http://aka.ms/installconfigsrv)。

#### <a name="add-the-vmware-account-for-automatic-discovery"></a>添加用于自动发现的 VMware 帐户

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="step-2-add-a-vcenter"></a>步骤 2：添加 vCenter
若要允许 Azure Site Recovery 发现本地环境中运行的虚拟机，需要将 VMware vCenter 服务器或 vSphere ESXi 主机与 Site Recovery 连接。

选择“+vCenter”开始连接 VMware vCenter 服务器或 VMware vSphere ESXi 主机。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>常见问题
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>后续步骤
在 Azure 中[设置目标环境](./site-recovery-prepare-target-vmware-to-azure.md)。

