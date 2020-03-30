---
title: 使用 Azure 迁移设置 VMware VM 发现的范围
description: 介绍如何使用 Azure 迁移为 VMware VM 评估和迁移设置发现范围。
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337631"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>为 VMware VM 设置发现范围

本文介绍如何限制[Azure 迁移设备](migrate-appliance-architecture.md)发现的 VMware VM 的发现范围。

当您： 

- 使用[Azure 迁移：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具评估 VMware VM 以迁移到 Azure。
- 使用[Azure 迁移：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具将 VMware VM[无代理迁移到](server-migrate-overview.md)Azure。

## <a name="set-discovery-scope"></a>设置发现范围


为 VMware VM 评估或迁移设置 Azure 迁移设备后，设备将开始发现 VM，并将 VM 元数据发送到 Azure。 在将设备连接到 vCenter Server 进行发现之前，可以将发现范围设置为将发现限制为 vCenter Server 数据中心、群集、群集文件夹、主机、主机文件夹或单个 VM。

要设置范围，请对 Azure 迁移用于访问 vCenter 服务器的帐户分配权限。

## <a name="create-a-vcenter-user-account"></a>创建 vCenter 用户帐户

如果尚未设置 Azure 迁移设备用于发现、评估和迁移 VMware VM 的 vCenter 用户帐户，请先执行此操作。

1.    以 vCenter Server 管理员身份登录到 vSphere Web 客户端。
2.    选择 **"管理** > **SSO 用户和组**"，然后单击"**用户**"选项卡。
3.    选择“新建用户”图标。****
4.    填写新的用户信息>**确定**。

帐户权限取决于要部署的内容。

**功能** | **帐户权限**
--- | ---
[评估](tutorial-assess-vmware.md)| 该帐户需要只读访问。
[发现应用/角色/功能](how-to-discover-applications.md) | 该帐户需要只读访问，为虚拟机启用了特权>来宾操作。
[分析依赖项（无代理）](how-to-create-group-machine-dependencies-agentless.md) | 该帐户需要只读访问，为虚拟机启用了特权>来宾操作。
[迁移（无代理）](tutorial-migrate-vmware.md) | 您需要分配正确权限的角色。<br/><br/> 要创建角色，请以 vCenter 服务器管理员的身份登录到 vSphere Web 客户端。 选择 vCenter 服务器实例>**创建角色**。 指定角色名称（例如<em>Azure_Migrate</em>，并将[所需的权限](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)分配给角色。


## <a name="assign-permissions-on-vcenter-objects"></a>在 vCenter 对象上分配权限

您可以使用以下两种方法之一对清单对象分配权限：

- 为要发现/迁移的所有父对象分配具有所需权限的角色、要使用的帐户。
- 或者，您可以在数据中心级别分配角色和用户帐户，并将它们传播到子对象。 然后，为帐户提供 **"不访问**"角色，用于您不想发现/迁移的每个对象。 我们不建议使用此方法，因为它很麻烦，并且可能会公开访问控件，因为每个新的子对象都会自动授予从父对象继承的访问权限。

要将角色分配给用于所有相关对象的帐户，请执行以下操作：

- **对于评估**：对于 VM 评估，将**只读**角色应用于要发现的所有承载 VM 的父对象 vCenter 用户帐户。 父对象包括：主机、主机文件夹、群集和层次结构中群集的文件夹，到数据中心。 将这些权限传播到层次结构中的子对象。

    ![分配权限](./media/tutorial-assess-vmware/assign-perms.png)

- **对于无代理迁移**：对于无代理迁移，对要发现的所有承载 VM 的父对象应用具有[所需权限](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)的用户定义角色。 您可以Azure_Migrate<em>命名角色。</em>

### <a name="scope-support"></a>范围支持

目前，如果 vCenter 帐户在 vCenter VM 文件夹级别授予访问权限，则服务器评估工具无法发现 VM。 支持主机和群集的文件夹。

如果要按 VM 文件夹对发现进行限定，请完成下一个过程，以确保 vCenter 帐户具有在 VM 级别分配的只读访问权限。

1. 对要作用域进行发现的 VM 文件夹中的所有 VM 分配只读权限。
2. 授予对承载 VM 的所有父对象的只读访问权限。
    - 包括层次结构中所有父对象（主机、主机文件夹、群集文件夹、群集文件夹）。
    - 不需将权限传播到所有子对象。
3. 选择数据中心作为“收集范围”即可使用凭据进行发现。**** 基于角色的访问控制设置可确保相应的 vCenter 用户帐户仅有权访问特定于租户的 VM。


## <a name="next-steps"></a>后续步骤

[设置设备](how-to-set-up-appliance-vmware.md)并开始[连续发现](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential)。
