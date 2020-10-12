---
title: 通过 Azure 资源移动器将 Azure Vm 移到另一个区域中的可用性区域
description: 了解如何通过 Azure 资源移动器将 Azure Vm 移动到可用性区域。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 315ea9b683ccd583f5c29c7527013f0d924336f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061864"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>将 Azure Vm 移到另一个区域中的可用性区域

本文介绍如何使用 [Azure 资源移动器](overview.md)将 azure vm (和相关的网络/存储资源) 移动到不同 Azure 区域中的可用性区域。

[Azure 可用性区域](../availability-zones/az-overview.md#availability-zones) 可帮助保护 Azure 部署免受数据中心故障的影响。 每个可用性区域都由一个或多个数据中心组成，这些数据中心都配置了独立电源、冷却和网络。 为了确保复原能力，所有 [启用的区域](../availability-zones/az-region.md)中都至少有三个单独的区域。 使用资源移动器，你可以移动：

- 目标区域中的可用性区域/可用性集的单实例 VM。
- 可用性集中的 VM 到目标区域中的可用性区域/可用性集。
- 源区域可用性区域中的 VM 到目标区域中的可用性区域。


> [!IMPORTANT]
> Azure 资源转移器目前提供公共预览版。

如果要将 Vm 移到同一区域中的不同可用性区域，请 [参阅此文](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)。

## <a name="prerequisites"></a>必备条件

- 要移动的资源所在的订阅的*所有者*访问权限。
    - 第一次在 Azure 订阅中添加特定源和目标映射的资源时，资源移动器会创建一个 [系统分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) ， (以前称为托管服务标识订阅所信任的 (MSI) # A3。
    - 若要创建标识，并为其分配所需的角色（来源订阅中的参与者或用户访问管理员），用于添加资源的帐户需要对订阅的“所有者”权限。 [详细了解](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) Azure 角色。
- 订阅需要足够的配额才能在目标区域中创建源资源。 如果不是，则请求额外的限制。 [了解详细信息](/azure/azure-resource-manager/management/azure-subscription-service-limits)。
- 验证与要将 VM 移动到的目标区域关联的定价和费用。 请使用[定价计算器](https://azure.microsoft.com/pricing/calculator/)来帮助你。
    


## <a name="check-vm-requirements"></a>检查 VM 要求

1. 检查要移动的 VM 是否受支持。

    - [验证](support-matrix-move-region-azure-vm.md#windows-vm-support)支持的 Windows VM。
    - [验证](support-matrix-move-region-azure-vm.md#linux-vm-support)支持的 Linux VM 和内核版本。
    - 查看支持的[计算](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)、[存储](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)和[网络](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)设置。
2. 检查要移动的 VM 是否已打开。
3. 请确保 Vm 具有最新的受信任根证书和更新的证书吊销列表 (CRL) 。 
    - 在运行 Windows 的 Azure Vm 上，安装最新的 Windows 更新。
    - 在运行 Linux 的 Vm 上，按照 Linux 分发程序指南确保计算机具有最新的证书和 CRL。 
4. 允许来自 VM 的出站连接：
    - 如果使用基于 URL 的防火墙代理来控制出站连接，请允许访问以下 [URL](support-matrix-move-region-azure-vm.md#url-access)
    - 如果使用网络安全组 (NSG) 规则来控制出站连接，请创建这些[服务标记规则](support-matrix-move-region-azure-vm.md#nsg-rules)。

## <a name="select-resources-to-move"></a>选择要移动的资源

选择要移动的资源。

- 可以在所选源区域中的资源组之间选择任何受支持的资源类型。
- 将资源移动到源区域订阅中的目标区域。 如果要更改订阅，可以在移动资源后进行更改。

1. 在 Azure 门户中，搜索“资源转移器”。 然后，在“服务”下，选择“Azure 资源转移器” 。

    ![搜索资源移动器](./media/move-region-availability-zone/search.png)

2. 在 " **概述**" 中，选择 " **入门**"。

    ![入门按钮](./media/move-region-availability-zone/get-started.png)

3. 在“移动资源” > “源 + 目标”中，选择来源订阅和区域 。
4. 在“目标”中，选择要将 VM 移动到的区域。 然后单击“下一步”  。

     ![用于填写源和目标订阅/区域的页面](./media/move-region-availability-zone/source-target.png)

6. 在“要移动的资源”中，单击“选择资源” 。
7. 在“选择资源”中，选择 VM。 只能添加支持移动的资源。 然后单击“完成”。 在“要移动的资源”中，单击“下一步” 。

    ![用于选择要移动的 VM 的页面](./media/move-region-availability-zone/select-vm.png)
8. 在“查看 + 添加”中，查看源和目标设置。

    ![用于检查设置并继续移动的页面](./media/move-region-availability-zone/review.png)

9. 单击“继续”，开始添加资源。
10. 添加过程成功完成后，单击通知图标中的“添加要移动的资源”。

    ![通知中的消息](./media/move-region-availability-zone/notification.png)

单击通知后，资源会显示在 " **跨区域** " 页上

> [!NOTE]
> 单击通知后，资源会显示在 " **跨区域** " 页上的 " *准备挂起* " 状态。
> - 如果要从移动集合中删除资源，所用的方法取决于你在移动过程中的进度。 [了解详细信息](remove-move-resources.md)。

## <a name="resolve-dependencies"></a>解决依赖项问题

1. 如果资源在“问题”列中显示“验证依赖项”消息，请单击“验证依赖项”按钮 。 验证过程就是这样。
2. 如果找到依赖项，请单击“添加依赖项”。 
3. 在“添加依赖项”中，选择从属资源 >”添加依赖项” 。 在通知中监视进度。

    ![添加依赖项的按钮](./media/move-region-availability-zone/add-dependencies.png)

3. 如果需要，添加其他依赖项，并再次验证依赖项。 

    ![用于添加附加依赖项的页面](./media/move-region-availability-zone/add-additional-dependencies.png)

4. 在“跨区域”页上，验证资源现在是否处于“准备挂起”状态，且没有任何问题。

    ![页面显示处于“准备挂起”状态的资源](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>移动源资源组 

在准备和移动 Vm 之前，源资源组必须存在于目标区域中。 

### <a name="prepare-to-move-the-source-resource-group"></a>准备以移动源资源组

按如下所示准备：

1. 在“跨区域”中，选择源资源组 >“准备” 。
2. 在“准备资源”中，单击“准备” 。

    ![用于准备源资源组的按钮](./media/move-region-availability-zone/prepare-resource-group.png)

    在准备过程中，资源转移器使用资源组设置生成 Azure 资源管理器 (ARM) 模板。 资源组内的资源不受影响。

> [!NOTE]
>  准备资源组后，资源组处于“启动移动挂起”状态。 

![显示启动挂起状态的状态](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>移动源资源组

按如下所示启动移动：

1. 在“跨区域”中，选择资源组 >“启动移动” 
2. 在“移动资源”中，单击“启动移动” 。 资源组移动到“启动移动进行中”状态。
3. 启动移动后，根据生成的 ARM 模板创建目标资源组。 源资源组变为“提交移动挂起”状态。

![显示提交移动的状态](./media/move-region-availability-zone/commit-move-pending.png)

要提交并完成移动过程：

1. 在 " **跨区域**" 中，选择资源组 > **提交移动**
2. 在“移动资源”中，单击“提交” 。

> [!NOTE]
> 提交移动后，源资源组处于“删除源挂起”状态。


## <a name="add-a-target-availability-zone"></a>添加目标可用性区域

在移动资源的其余部分之前，我们将为 VM 设置目标可用性区域。

1. 在 " **跨区域** " 页上，单击要移动的 VM 的 " **目标配置** " 列中的链接。

    ![VM 属性](./media/move-region-availability-zone/select-vm-settings.png)

3. 在 " **配置设置**" 中，指定目标 VM 的设置。 你可以在目标区域中配置 VM，如下所示：
    -  在目标区域中创建新的等效资源。 除了指定的设置，目标资源的设置与源相同。
    - 选择目标区域中的现有 VM。 

4. 在 " **区域**" 中，选择要在其移动时放置 VM 的区域。

    仅对正在编辑的资源进行更改。 需要单独更新所有从属资源。

5. 在 **SKU**中，指定要分配给目标 VM 的 [Azure 层](..//virtual-machines/sizes.md) 。
6. 如果希望目标 VM 在可用性区域中的可用性集内运行，请在 " **可用性集**" 中选择一个可用性集。
7. 选择“保存更改”。

    ![VM 设置](./media/move-region-availability-zone/vm-settings.png)


## <a name="prepare-resources-to-move"></a>准备要移动的资源

现在，源资源组已移动，你可以准备移动其他资源。

1. 在“跨区域”中，选择要准备的资源。 

    ![用于选择准备其他资源的页面](./media/move-region-availability-zone/prepare-other.png)

2. 选择“准备”。 

> [!NOTE]
> - 在准备过程中，Azure Site Recovery 移动代理安装在 Vm 上以进行复制。
> - VM 数据定期复制到目标区域。 这不会影响源 VM。
> - 资源移动会为其他源资源生成 ARM 模板。
> - 准备资源后，它们处于“启动移动挂起”状态。

![显示处于启动移动挂起状态的资源的页面](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>启动移动

准备好资源后，现在可以启动移动。 

1. 在“跨区域”中，选择状态“启动移动挂起”的资源。 然后单击 "**启动移动**"
2. 在“移动资源”中，单击“启动移动” 。

    ![用于启动资源移动的页面](./media/move-region-availability-zone/initiate-move.png)

3. 跟踪通知栏中的移动进度。


> [!NOTE]
> - 对于 VM，在目标区域中创建副本 VM。 源 VM 已关闭，并出现一段停机时间（通常为几分钟）。
> - 资源转移器使用已准备的 ARM 模板重新创建其他资源。 通常不会停机。
> - 准备资源后，它们处于 *提交移动挂起* 状态。


![用于在提交移动挂起状态中显示资源的页面](./media/move-region-availability-zone/resources-commit-move-pending.png)

## <a name="discard-or-commit"></a>放弃还是提交？

初始移动后，你可以决定是提交移动，还是放弃它。 

- **放弃**：如果要测试，并且不想实际移动源资源，则可能需要放弃移动。 放弃移动会将资源返回到“启动移动挂起”状态。
- **提交**：提交即完成移动到目标区域的操作。 提交后，源资源将处于“删除源挂起”状态，你可以决定是否要删除它。

## <a name="discard-the-move"></a>放弃移动 

你可以按如下所示放弃移动：

1. 在“跨区域”中，选择“提交移动挂起”状态的资源，然后单击“放弃移动”  。
2. 在“放弃移动”中，单击“放弃” 。
3. 跟踪通知栏中的移动进度。
 

> [!NOTE]
> 对于 Vm，在丢弃资源后，它们处于 *启动移动挂起* 状态。

## <a name="commit-the-move"></a>提交移动

如果要完成移动过程，请提交移动。 

1. 在“跨区域”中，选择“提交移动挂起”状态的资源，然后单击“提交移动”。
2. 在“提交资源”中，单击“提交” 。

    ![用于提交资源以完成移动的页面](./media/move-region-availability-zone/commit-resources.png)

3. 跟踪通知栏中的提交进度。

> [!NOTE]
> - 提交移动后，VM 将停止复制。 源 VM 不受提交影响。
> - 提交不会影响源网络资源。
> - 提交移动后，资源处于“删除源挂起”状态。

![显示资源处于 *删除源挂起* 状态的页面](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>在移动后配置设置

不会自动从 VM 卸载移动服务。 手动卸载它，或者如果你计划再次移动服务器，则保留它。


## <a name="delete-source-resources-after-commit"></a>提交后删除源资源

移动后，可以选择删除源区域中的资源。

1. 在“跨区域”中，单击要删除的每个源资源的名称。
2. 在每个资源的属性页中，选择“删除”。

## <a name="delete-additional-resources-created-for-move"></a>删除为移动创建的其他资源

移动后，你可以手动删除移动集合和创建的 Site Recovery 资源。

- 默认情况下，移动集合处于隐藏状态。 要查看它，你需要打开隐藏的资源。
- 必须先删除对缓存存储的锁定，才能删除缓存存储。

按如下所示删除： 

1. 找到资源组 ```RegionMoveRG-<sourceregion>-<target-region>``` 中的资源。
2. 查看是否已移动或删除源区域中的所有 VM 和其他源资源。 这可确保没有挂起的资源使用它们。
2. 删除资源：

    - 移动集合名称为 ```movecollection-<sourceregion>-<target-region>```。
    - 缓存存储账户名称为 ```resmovecache<guid>```
    - 保管库名称为 ```ResourceMove-<sourceregion>-<target-region>-GUID```。

## <a name="next-steps"></a>后续步骤

[了解](about-move-process.md) 移动过程。
