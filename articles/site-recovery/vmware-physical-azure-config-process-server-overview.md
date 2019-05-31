---
title: 有关 Azure Site Recovery 配置、 流程和主目标服务器 |Microsoft Docs
description: 本文概述的配置、 流程和设置的本地 VMware Vm 到 Azure 中使用 Azure Site Recovery 的灾难恢复时使用的主目标服务器
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 8b3815fc9dc44484779a70b51ebff4802265d53a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417740"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>有关 Site Recovery 组件 （配置、 进程、 主目标）

本文介绍配置、 进程和主目标服务器将 VMware Vm 和物理服务器复制到 Azure 中使用时，使用[Site Recovery](site-recovery-overview.md)服务。

## <a name="configuration-server"></a>配置服务器

对于在本地 VMware Vm 和物理服务器灾难恢复，需要 Site Recovery 配置服务器部署在本地。

**设置** | **详细信息** | **Links**
--- | --- | ---
**组件**  | 配置服务器计算机运行所有本地 Site Recovery 组件，包括配置服务器、 进程服务器和主目标服务器。<br/><br/> 如果设置了配置服务器时，自动安装所有组件。 | [读取](vmware-azure-common-questions.md#configuration-server)配置服务器的常见问题解答。
**角色** | 配置服务器在本地和 Azure 之间协调通信并管理数据复制。 | 详细了解有关体系结构[VMware](vmware-azure-architecture.md)并[物理服务器](physical-azure-architecture.md)灾难恢复到 Azure。
**VMware 要求** | 对于在本地 VMware Vm 的灾难恢复，必须安装并配置服务器作为在本地，高可用性 VMware VM 来运行。 | [了解有关](vmware-azure-deploy-configuration-server.md#prerequisites)系统必备组件。
**VMware 部署** | 我们建议你部署使用已下载的 OVA 模板在配置服务器。 此方法提供只需设置符合所有要求和先决条件配置服务器的方法。<br/><br/> 如果出于某种原因您不能部署使用 OVA 模板的 VMware VM，你可以手动，如下面的物理计算机灾难恢复所述设置配置服务器计算机。 | [部署](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template)使用 OVA 模板。
**物理服务器要求** | 对于灾难恢复的本地物理服务器上，你将部署配置服务器手动。 | [了解有关](physical-azure-set-up-source.md#prerequisites)系统必备组件。
**物理服务器部署** | 如果为 VMware VM，不能安装它，则可以在物理服务器上安装它。 | [部署](physical-azure-set-up-source.md#set-up-the-source-environment)配置服务器手动。


## <a name="process-server"></a>进程服务器

**设置** | **详细信息** | **Links**
--- | --- | ---
**部署**  | 对于灾难恢复和在本地 VMware Vm 和物理服务器的复制，需要的进程服务器在本地。 默认情况下，进程服务器安装配置服务器上，在部署时。 | [了解详细信息](vmware-azure-architecture.md?#architectural-components)。
**角色 （在本地** | -从启用了复制的计算机接收复制数据。<br/> -优化复制数据通过缓存、 压缩和加密，并将其发送到 Azure 存储。<br/> -在本地 VMware Vm 和想要复制的物理服务器上执行 Site Recovery 移动服务的推送安装。<br/> -执行本地计算机上自动的发现。 | [了解详细信息](vmware-physical-azure-config-process-server-overview.md#process-server)。 
**角色 （从 Azure 故障回复）** | 故障转移后从你的本地站点，您设置进程服务器在 Azure 中，作为 Azure VM，来处理故障回复到本地位置。<br/><br/> Azure 中的进程服务器是临时的。 故障回复完成后，可以删除 Azure VM。 | [了解详细信息](vmware-azure-set-up-process-server-azure.md)。
**缩放** | 对于大型部署，在本地可设置附加的横向扩展进程服务器。 其他服务器横向扩展容量，通过处理更大数量的复制计算机和更大的复制流量。<br/><br/> 可以在两个进程服务器，若要对负载平衡复制流量移动机。 | [了解详细信息](vmware-azure-set-up-process-server-scale.md)，


## <a name="master-target-server"></a>主目标服务器

主目标服务器处理从 Azure 进行故障回复期间产生的复制数据。

- 默认情况下，在配置服务器上安装它。
- 对于大型部署，可以另外添加一个单独的主目标服务器用于故障回复。


## <a name="next-steps"></a>后续步骤
- 审阅[体系结构](vmware-azure-architecture.md)的 VMware Vm 和物理服务器灾难恢复。
- 审阅[要求和先决条件](vmware-physical-azure-support-matrix.md)为 VMware Vm 和物理服务器到 Azure 的灾难恢复。 
