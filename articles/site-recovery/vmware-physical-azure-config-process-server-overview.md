---
title: 关于 Azure Site Recovery 配置/进程/主目标服务器
description: 本文概述了在使用 Azure Site Recovery 设置本地 VMware Vm 到 Azure 的灾难恢复时使用的配置、进程和主目标服务器
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: ad816f39dd4182dfa41fca975c99824a5d77f860
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256986"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>关于 Site Recovery 组件（配置、进程、主目标）

本文介绍使用[Site Recovery](site-recovery-overview.md)服务将 VMware vm 和物理服务器复制到 Azure 时使用的配置、进程和主目标服务器。

## <a name="configuration-server"></a>配置服务器

对于本地 VMware Vm 和物理服务器的灾难恢复，需要在本地部署 Site Recovery 配置服务器。

**设置** | **详细信息** | **链接**
--- | --- | ---
**组件**  | 配置服务器计算机运行所有本地 Site Recovery 组件，其中包括配置服务器、进程服务器和主目标服务器。<br/><br/> 设置配置服务器时，会自动安装所有组件。 | [阅读](vmware-azure-common-questions.md#configuration-server)配置服务器常见问题解答。
**角色** | 配置服务器在本地和 Azure 之间协调通信并管理数据复制。 | 详细了解[VMware](vmware-azure-architecture.md)和[物理服务器](physical-azure-architecture.md)到 Azure 的灾难恢复体系结构。
**VMware 要求** | 对于本地 VMware Vm 的灾难恢复，你必须安装并运行配置服务器，作为本地、高度可用的 VMware VM。 | [了解](vmware-azure-deploy-configuration-server.md#prerequisites)先决条件。
**VMware 部署** | 建议使用已下载的 .OVA 模板部署配置服务器。 此方法提供了一种简单的方法来设置符合所有要求和先决条件的配置服务器。<br/><br/> 如果由于某种原因而无法使用 .OVA 模板部署 VMware VM，则可以手动设置配置服务器计算机，如下所述。 | 使用 .OVA 模板进行[部署](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)。
**物理服务器要求** | 对于本地物理服务器上的灾难恢复，你需要手动部署配置服务器。 | [了解](physical-azure-set-up-source.md#prerequisites)先决条件。
**物理服务器部署** | 如果无法将其安装为 VMware VM，则可以将其安装在物理服务器上。 | 手动[部署](physical-azure-set-up-source.md#set-up-the-source-environment)配置服务器。


## <a name="process-server"></a>进程服务器

**设置** | **详细信息** | **链接**
--- | --- | ---
**部署**  | 对于本地 VMware Vm 和物理服务器的灾难恢复和复制，需要一个本地进程服务器。 默认情况下，在部署过程服务器时，它会安装在配置服务器上。 | [了解详细信息](vmware-azure-architecture.md?#architectural-components)。
**角色（本地** | -从启用复制的计算机接收复制数据。<br/> -通过缓存、压缩和加密功能优化复制数据，并将其发送到 Azure 存储。<br/> -在要复制的本地 VMware Vm 和物理服务器上执行 Site Recovery 移动服务的推送安装。<br/> -执行本地计算机的自动发现。 | [了解详细信息](vmware-physical-azure-config-process-server-overview.md#process-server)。 
**角色（从 Azure 故障回复）** | 从本地站点进行故障转移后，将 Azure 中的进程服务器设置为 Azure VM，以处理本地位置的故障回复。<br/><br/> Azure 中的进程服务器是临时服务器。 故障回复完成后，可以删除 Azure VM。 | [了解详细信息](vmware-azure-set-up-process-server-azure.md)。
**缩放** | 对于较大型的部署，你可以在本地设置额外的横向扩展进程服务器。 其他服务器通过处理更多的复制计算机以及更大的复制流量，扩展容量。<br/><br/> 可以在两个进程服务器之间移动计算机，以便对复制流量进行负载均衡。 | [了解详细信息](vmware-azure-set-up-process-server-scale.md)，


## <a name="master-target-server"></a>主目标服务器

主目标服务器处理从 Azure 进行故障回复期间产生的复制数据。

- 它在默认情况下安装在配置服务器上。
- 对于大型部署，可以另外添加一个单独的主目标服务器用于故障回复。


## <a name="next-steps"></a>后续步骤
- 查看 VMware Vm 和物理服务器的灾难恢复[体系结构](vmware-azure-architecture.md)。
- 查看 VMware Vm 和物理服务器到 Azure 的灾难恢复[要求和先决条件](vmware-physical-azure-support-matrix.md)。 
