---
title: 将 VMware VM 发现的作用域设置 Azure Migrate
description: 描述如何在 Azure Migrate 中设置 VMware VM 评估和迁移的发现作用域。
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: dfc9c12edd93fc720ef716fd43b04e0c193d5803
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919720"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>为 VMware Vm 设置发现作用域

本文介绍如何在以下情况下限制你发现的 VMware Vm 的作用域：

- 使用 Azure Migrate： Server 评估工具时，使用 [Azure Migrate 设备](migrate-appliance-architecture.md) 发现 vm。
- 当你使用 Azure Migrate：服务器迁移工具时，使用 [Azure Migrate 设备](migrate-appliance-architecture.md) 发现 vm，以便无代理地将 VMware vm 迁移到 Azure。

设置设备时，它将连接到 vCenter Server 并启动发现。 将设备连接到 vCenter Server 之前，你可以将发现限制为 vCenter Server 数据中心、群集、群集文件夹、主机、主机的文件夹或单个 Vm。 若要设置作用域，请对设备用于访问 vCenter Server 的帐户分配权限。

## <a name="before-you-start"></a>开始之前

如果尚未设置 Azure Migrate 用于发现的 vCenter 用户帐户，请立即执行此操作进行 [评估](tutorial-prepare-vmware.md#set-up-permissions-for-assessment) 或 [无代理迁移](tutorial-prepare-vmware.md#assign-permissions-to-an-account)。


## <a name="assign-permissions-and-roles"></a>分配权限和角色

您可以使用以下两种方法之一分配对 VMware 清单对象的权限：

- 在设备使用的帐户上，为你要确定其作用域的对象分配具有所需权限的角色。
- 或者，将角色分配给数据中心级别的帐户，并传播到子对象。 然后为不在作用域内的每个对象的每个对象提供 " **无访问权限** " 帐户。 我们不建议使用这种方法，因为这种方法很繁琐，可能会公开访问控制，因为每个新的子对象会自动授予从父对象继承的访问权限。

不能在 vCenter VM 文件夹级别确定清单发现的范围。 如果需要将发现范围限制为 VM 文件夹中的 Vm，请创建一个用户，并将访问权限分别授予每个所需的 VM。 支持主机和群集文件夹。


### <a name="assign-a-role-for-assessment"></a>为评估分配角色

1. 在用于发现的设备 vCenter 帐户上，将所有托管 Vm 的 " **只读** " 角色应用于要发现的 vm，并评估 (主机、群集、主机文件夹、群集文件夹、最多 datacenter) 的所有父对象。
2. 将这些权限传播到层次结构中的子对象。

    ![分配权限](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>为无代理迁移分配角色

1. 在用于迁移的设备 vCenter 帐户上，将具有 [所需权限](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)的用户定义角色应用到托管要发现和迁移的 vm 的所有父对象。
2. 可以将角色命名为更易于识别的内容。 例如， <em>Azure_Migrate</em>。

## <a name="work-around-vm-folder-restriction"></a>解决 VM 文件夹限制

目前，如果在 vCenter VM 文件夹级别授予访问权限，则服务器评估工具无法发现 Vm。 如果你确实想要按 VM 文件夹确定发现和评估的范围，请使用此解决方法。

1. 为要确定其作用域的 VM 文件夹中的所有 Vm 分配只读权限。
2. 授予对承载 Vm 主机、群集、主机文件夹、群集文件夹、最多 datacenter) 的所有父对象的只读访问权限。 不需将权限传播到所有子对象。
3. 若要将凭据用于发现，请选择数据中心作为 **收集作用域**。


基于角色的访问控制设置可确保相应的 vCenter 用户帐户仅有权访问特定于租户的 Vm。


## <a name="next-steps"></a>后续步骤

[设置设备](how-to-set-up-appliance-vmware.md)
