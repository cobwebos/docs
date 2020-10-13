---
title: 通过 Azure 策略为虚拟机启用 Automanage
description: 了解如何通过 Azure 门户中的内置 Azure 策略为 Vm 启用 Azure Automanage。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8f679626b69bd855e86b94cdde51955edd068e8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714906"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>通过 Azure 策略为虚拟机启用 Automanage

如果要为大量 Vm 启用 Automanage，可以使用内置的 [Azure 策略](..\governance\azure-management.md)执行此操作。 本文将指导你查找正确的策略，以及如何对其进行分配，以便在 Azure 门户中启用 Automanage。


## <a name="prerequisites"></a>先决条件

如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)。

> [!NOTE]
> 免费试用帐户无法访问本教程中使用的虚拟机。 请升级到即用即付订阅。

> [!IMPORTANT]
> 若要启用 Automanage： **Owner** 角色或 **参与者** 以及 **用户访问管理员** 角色，需要以下 Azure RBAC 权限。


## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com/)。


## <a name="locate-and-assign-the-policy"></a>找到并分配策略

1. 在 Azure 门户中导航到 " **策略** "
1. 中转到 " **定义** " 窗格
1. 单击 " **类别** " 下拉列表以查看可用选项
1. 选择 " **启用 Automanage – Azure 虚拟机最佳实践** " 选项
1. 现在，列表将更新以显示名称以*Enable Automanage*开头的内置策略
1. 单击 " *启用 Automanage-Azure 虚拟机最佳实践* " 内置策略名称
1. 单击该策略后，可以看到 " **定义** " 选项卡

    > [!NOTE]
    > Azure 策略定义用于设置 Automanage 参数，例如配置文件或帐户。 它还设置筛选器，以确保策略仅适用于正确的 Vm。

1. 单击 " **分配** " 按钮创建分配
1. 在 "**基本**信息" 选项卡下，通过设置*订阅*和*资源组*填写**范围**

    > [!NOTE]
    > 作用域允许您定义此策略应用到的 Vm。 可以在订阅级别或资源组级别设置应用程序。 如果设置了资源组，则当前在该资源组中的所有 Vm 或添加到该资源组中的任何未来 Vm 都将自动启用 Automanage。 

1. 单击 " **参数** " 选项卡，并设置 **Automanage 帐户** 和所需的 **配置配置文件** 
1. 在 " **查看** " 和 "创建" 选项卡下，查看设置
1. 单击 "**创建**" 以应用分配
1. 在 "**定义**" 旁边的 "**分配**" 选项卡中查看分配

> [!NOTE]
> 该策略开始对当前在资源组或订阅中的 Vm 生效，需要一段时间。


## <a name="next-steps"></a>后续步骤 

了解通过 Azure 门户为虚拟机启用 Azure Automanage 的另一种方法。 

> [!div class="nextstepaction"]
> [在 Azure 门户中为虚拟机启用 Automanage](quick-create-virtual-machines-portal.md)