---
title: 使用 Azure Site Recovery 将 VMware VM 复制到 Azure (CSP) 的多租户支持概述 | Microsoft Docs
description: 介绍通过 CSP 计划，在多租户环境中为租户订阅提供 Azure Site Recovery 支持的概述。
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: manayar
ms.openlocfilehash: 9b4fbb34686a12f992b344ac61420c9ba99ee405
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="overview-of-multi-tenant-support-for-vmware-replication-to-azure-with-csp"></a>使用 CSP 将 VMware 复制到 Azure 的多租户支持的概述

[Azure Site Recovery](site-recovery-overview.md) 支持适用于租户订阅的多租户环境。 它还支持适用于租户订阅的多租户，这些租户订阅通过 Microsoft 云解决方案提供商 (CSP) 计划创建和管理。 

本文提供了实现和管理多租户 VMware 到 Azure 复制的概述。 

## <a name="multi-tenant-environments"></a>多租户环境

有三种主要的多租户模型：

* **共享托管服务提供程序 (HSP)**：合作伙伴拥有物理基础结构，并使用共享资源（vCenter、数据中心、物理存储等）在同一基础结构上托管多个租户的 VM。 合作伙伴可以提供灾难恢复管理作为托管服务，租户也可以拥有灾难恢复作为自助解决方案。

* **专用托管服务提供程序**：合作伙伴拥有物理基础结构，但使用专用资源（多个 vCenter、物理数据存储等）在单独的基础结构上托管每个租户的 VM。 合作伙伴可以提供灾难恢复管理作为托管服务，租户也可以拥有它作为自助解决方案。

* **托管服务提供程序 (MSP)**：客户拥有托管 VM 的物理基础结构，合作伙伴提供灾难恢复启用和管理功能。

## <a name="shared-hosting-services-provider-hsp"></a>共享托管服务提供程序 (HSP)

 另外两种方案也属于共享托管方案，并使用相同的原则。 共享托管指南结尾对其中的差异进行了说明。

在多租户方案中，基本的要求是必须隔离租户。 不允许一个租户观察到另一个租户托管的内容。 在由合作伙伴管理的环境中，此要求不那么重要，而在自助服务环境中，此要求相当重要。 本文假定租户隔离是必需的。

下图显示了此体系结构。

![使用一个 vCenter 的共享 HSP](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**使用一个 vCenter 服务器的共享托管**

在该图中，每个客户都有一个单独的管理服务器。 此配置限制租户访问特定于租户的 VM，实现了租户隔离。 VMware VM 复制使用配置服务器以发现 VM 和安装代理。 相同原则也适用于多租户环境，只是增加了“使用 vCenter 访问控制限制 VM 发现”这一规定。

数据隔离要求表示所有敏感的基础结构信息（例如访问凭据）始终不能披露给租户。 因此，建议将管理服务器的所有组件交由合作伙伴全权控制。 管理服务器组件包括：

* 配置服务器）
* 进程服务器
* 主目标服务器

单独的横向扩展进程服务器也受合作伙伴的控制。

## <a name="configuration-server-accounts"></a>配置服务器帐户

在多租户方案中，每个配置服务器使用两个帐户：

- **vCenter 访问帐户**：此帐户用于发现租户 VM。 它分配有 vCenter 访问权限。 为了避免访问泄漏，建议合作伙伴在配置工具中自行输入这些凭据。

- **虚拟机访问帐户**：此帐户用于通过自动推送在租户 VM 上安装移动服务代理。 它通常为域帐户，可以由租户提供给合作伙伴，也可以是由合作伙伴直接管理的帐户。 如果不希望将详细信息直接与合作伙伴共享，租户可以通过在有限时间访问配置服务器时输入凭据。 或者，在合作伙伴的帮助下，手动安装移动服务代理。

## <a name="vcenter-account-requirements"></a>vCenter 帐户要求

必须使用已向其分配了特定角色的帐户来配置配置服务器。 

- 角色分配需应用到每个 vCenter 对象的 vCenter 访问帐户，不能传播到子对象。 此配置可确保租户隔离，因为传播访问权限可能会导致意外访问其他对象。

    ![“传播到子对象”选项](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- 替代方法是将用户帐户和角色分配给数据中心对象，再将其传播到子对象。 然后，针对每个不允许特定租户访问的对象（例如属于其他租户的 VM），为帐户分配“无访问权限”角色。 此配置很繁琐。 它会意外公开访问控制，因为系统会自动为每个新建的子对象授予从父对象继承的访问权限。 因此，建议使用第一种方法。

### <a name="create-a-vcenter-account"></a>创建 vCenter 帐户

1. 通过克隆预定义的“只读”角色创建新角色，然后为其提供一个方便的名称（例如此示例中显示的 Azure_Site_Recovery）。
2. 将以下权限分配给该角色：

    * **数据存储**：分配空间、浏览数据存储、降低文件操作级别、删除文件、更新虚拟机文件
    * **网络**：网络分配
    * **资源**：将 VM 分配到资源池、迁移关闭的 VM、迁移打开的 VM
    * **任务**：创建任务、更新任务
    * **VM - 配置**：全部
    - **VM - 交互**>回答问题、设备连接、配置 CD 介质、配置软盘介质、关机、开机、VMware 工具安装
    - **VM - 清单**> 从现有项创建、新建、注册、注销
    - **VM - 预配**>允许虚拟机下载、允许虚拟机文件上传
    - **VM - 快照管理**>删除快照

        ![“编辑角色”对话框](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. 将访问级别分配给各种对象的 vCenter 帐户（在租户配置服务器中使用），如下所示：

>| 对象 | 角色 | 备注 |
>| --- | --- | --- |
>| vCenter | 只读 | 需要获取 vCenter 访问权限来管理不同对象时，才需要此项。 如果不需要将此帐户提供给租户，或者不需要使用此帐户在 vCenter 进行任何管理操作，则可删除此权限。 |
>| 数据中心 | Azure_Site_Recovery |  |
>| 主机和主机群集 | Azure_Site_Recovery | 请再次确保访问权限为对象级别，使得在故障转移之前和故障回复之后，只有那些可供访问的主机才有租户 VM。 |
>| 数据存储和数据存储群集 | Azure_Site_Recovery | 与前相同。 |
>| 网络 | Azure_Site_Recovery |  |
>| 管理服务器 | Azure_Site_Recovery | 包括对 CS 计算机外部的所有组件（CS、PS 和 MT）的访问权限。 |
>| 租户 VM | Azure_Site_Recovery | 确保特定租户的任何新租户 VM 也会获得此访问权限，否则无法通过 Azure 门户发现这些 VM。 |

vCenter 帐户访问现已完成。 此步骤可满足完成故障回复操作的最低权限要求。 这些访问权限也可与现有策略结合使用。 只需修改现有权限集，包括前面详述的步骤 2 中的角色权限即可。

### <a name="failover-only"></a>仅故障转移
要将灾难恢复操作限制到仅故障转移（即没有故障回复功能），可使用之前的过程，但会出现以下异常：

- 仅将 Read-Only 角色分配给该账户，而没有将 Azure_Site_Recovery 角色分配给 vCenter 访问帐户。 此权限集允许 VM 复制和故障转移，不允许故障回复。
- 前述进程中的所有其他内容保留原样。 每个权限仍然只在对象级别分配，不传播到子对象，以便确保租户隔离并限制 VM 发现。


## <a name="dedicated-hosting-solution"></a>专用托管解决方案

如下图所示，专用托管解决方案中的体系结构差异在于，每个租户的基础结构是专为该租户设置的。 由于租户是通过单独的 vCenter 隔离的，因此托管提供者仍需遵循为共享托管提供的 CSP 步骤，但不需担心租户隔离。 CSP 安装程序保持不变。

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**多个 vCenter 的专用托管方案**

## <a name="managed-service-solution"></a>托管服务解决方案

如下图所示，托管服务解决方案中的体系结构差异在于，每个租户的基础结构在物理上也是与其他租户的基础结构分隔开的。 当租户拥有基础结构但需解决方案提供商管理灾难恢复时，通常使用此方案。 由于租户是通过不同的基础结构进行物理隔离的，因此合作伙伴仍需遵循为共享托管提供的 CSP 步骤，但不需担心租户隔离。 CSP 预配保持不变。

![architecture-shared-hsp](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**多个 vCenter 的托管服务方案**

## <a name="next-steps"></a>后续步骤
[详细了解](site-recovery-role-based-linked-access-control.md) Site Recovery 中基于角色的访问控制。
了解如何[将 VMware VM 的灾难恢复设置到 Azure](vmware-azure-tutorial.md)
[通过具有 CSP 的多租户为 VMWare VM 设置灾难恢复](vmware-azure-multi-tenant-csp-disaster-recovery.md)
