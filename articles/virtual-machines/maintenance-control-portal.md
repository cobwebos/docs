---
title: 使用 Azure 门户对 Azure 虚拟机进行维护控制
description: 了解如何使用维护控制和 Azure 门户来控制何时将维护应用到 Azure Vm。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: b174e2631131e6bf26d7b1cb62442c8a99102e2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397277"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>控制包含维护控制的更新和 Azure 门户

维护控制允许你决定何时向独立 VM 和 Azure 专用主机应用更新。 本主题介绍用于维护控制的 Azure 门户选项。 有关使用维护控制的好处、其限制和其他管理选项的详细信息，请参阅[使用维护控制管理平台更新](maintenance-control.md)。

## <a name="create-a-maintenance-configuration"></a>创建维护配置

1. 登录到 Azure 门户。

1. 搜索 **维护配置**。

   ![显示如何打开维护配置的屏幕截图](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. 单击“添加”。

   ![显示如何添加维护配置的屏幕截图](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. 选择 "订阅" 和 "资源组"，提供配置的名称，然后选择区域。 单击“下一步”。

   ![显示维护配置基础知识的屏幕截图](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. 添加标记和值。 单击“下一步”。

   ![显示如何向维护配置添加标记的屏幕截图](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. 查看摘要。 单击“创建”。

   ![显示如何创建维护配置的屏幕截图](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. 部署完成后，单击 " **前往资源**"。

   ![显示维护配置部署完成的屏幕截图](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>分配此配置

在维护配置的详细信息页上，单击 "分配"，然后单击 " **分配资源**"。 

![显示如何分配资源的屏幕截图](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

选择要为其分配维护配置的资源，然后单击 **"确定"**。 " **类型** " 列显示资源是独立 VM 还是 Azure 专用主机。 要分配配置，需要运行 VM。 如果尝试将配置分配给已停止的 VM，则会出现错误。 

<!---Shantanu to add details about the error case--->

![显示如何选择资源的屏幕截图](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>检查配置

可以验证是否正确应用了配置，或查看当前使用 **维护配置**分配的任何维护配置。 " **类型** " 列显示是否已将配置分配给隔离的 VM 或 Azure 专用主机。 

![显示如何检查维护配置的屏幕截图](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

你还可以在其 "属性" 页上检查特定虚拟机的配置。 单击 " **维护** " 查看分配给该虚拟机的配置。

![显示如何检查主机维护的屏幕截图](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>检查是否有挂起的更新

还可以通过两种方式检查更新是否等待维护配置。 在 " **维护配置**" 中，在 "配置" 的详细信息中，单击 " **分配** "，然后检查 **维护状态**。

![显示如何检查挂起更新的屏幕截图](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

你还可以使用 **虚拟机** 或专用主机的属性来检查特定主机。 

![显示突出显示的维护状态的屏幕截图。](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>应用更新

你可以使用 **虚拟机**按需应用挂起的更新。 在 "VM 详细信息" 中，单击 " **维护** "，然后单击 " **立即应用维护**"。

![显示如何应用挂起更新的屏幕截图](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>检查应用更新的状态 

可以在 **维护配置** 或使用 **虚拟机**中检查更新的进度。 在 "VM 详细信息" 中，单击 " **维护**"。 在下面的示例中， **维护状态** 显示更新处于 **挂起**状态。

![显示如何检查挂起更新的状态的屏幕截图](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>删除维护配置

若要删除配置，请打开配置详细信息，然后单击 " **删除**"。

![演示如何删除配置的屏幕截图。](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅[维护和更新](maintenance-and-updates.md)。
