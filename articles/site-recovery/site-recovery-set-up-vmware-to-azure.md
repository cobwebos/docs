---
title: "设置源环境（VMware 到 Azure）| Microsoft 文档"
description: "本文介绍如何设置本地环境，以便开始将 VMware 虚拟机复制到 Azure。"
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: 2b6b0e5cc95f28b5596e7e898f5f035e3647d9c5
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>设置源环境（VMware 到 Azure）
> [!div class="op_single_selector"]
> * [VMware 到 Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [物理机到 Azure](./site-recovery-set-up-physical-to-azure.md)

本文介绍如何设置源、本地环境，以便将 VMware 上运行的虚拟机复制到 Azure。 它包括以下相关步骤：选择复制方案、将本地计算机设置为 Site Recovery 配置服务器和自动发现本地 VM。 

## <a name="prerequisites"></a>先决条件

本文假设已完成以下操作：
- 在 [Azure 门户](http://portal.azure.com)中[设置资源](tutorial-prepare-azure.md)。
- [设置本地 VMware](tutorial-prepare-on-premises-vmware.md)，包括用于自动发现的专用帐户。



## <a name="choose-your-protection-goals"></a>选择保护目标

1. 在 Azure 门户中，浏览到“恢复服务保管库”边栏选项卡，并选择保管库。
2. 在保管库的资源菜单中，转到“开始使用” > “Site Recovery” > “步骤 1: 准备基础结构” > “保护目标”。

    ![选择目标](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. 在“保护目标”中选择“到 Azure”，并选择“是，使用 VMware vSphere 虚拟机监控程序”。 然后单击“确定”。

    ![选择目标](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>设置配置服务器

使用开放虚拟化格式 (OVF) 模板将配置服务器设置为本地 VMware VM。 [详细了解](concepts-vmware-to-azure-architecture.md)将在 VMware VM 上安装的组件。 

1. 了解配置服务器部署的[先决条件](how-to-deploy-configuration-server.md#prerequisites)。 [检查容量数字](how-to-deploy-configuration-server.md#capacity-planning)以便进行部署。
2. [下载](how-to-deploy-configuration-server.md#download-the-template)并[导入](how-to-deploy-configuration-server.md#import-the-template-in-vmware) OVF 模板 (how-to-deploy-configuration-server.md)，设置运行配置服务器的本地 VMware VM。
3. 打开 VMware VM，并将其[注册](how-to-deploy-configuration-server.md#register-the-configuration-server)到恢复服务保管库中。


## <a name="add-the-vmware-account-for-automatic-discovery"></a>添加用于自动发现的 VMware 帐户

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>连接到 VMware 服务器

要允许 Azure Site Recovery 发现本地环境中运行的虚拟机，需要将 VMware vCenter 服务器或 vSphere ESXi 主机与 Site Recovery 连接。

选择“+vCenter”开始连接 VMware vCenter 服务器或 VMware vSphere ESXi 主机。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>常见问题
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>后续步骤
在 Azure 中[设置目标环境](./site-recovery-prepare-target-vmware-to-azure.md)。
