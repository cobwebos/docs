---
title: 使用 Azure 门户在 Azure 虚拟机规模集上配置托管服务标识
description: 分步说明如何使用 Azure 门户在 Azure VMSS 上配置托管服务标识。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: cbe2e3d9f60ced5c707ce5a701a5aac937ccc072
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887983"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-using-the-azure-portal"></a>使用 Azure 门户配置虚拟机规模集托管服务标识

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

在本文中，你将学习如何使用 Azure 门户为虚拟机规模集启用和禁用系统和用户分配的标识。

## <a name="prerequisites"></a>先决条件

- 如果不熟悉托管服务标识，请查阅[概述部分](overview.md)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要执行本文中的管理操作，帐户需要分配以下角色：
    - [虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)，可从虚拟机规模集启用和删除系统分配的托管标识。

## <a name="system-assigned-identity"></a>系统分配标识 

在本部分中，你将学习如何使用 Azure 门户在虚拟机规模集上启用和禁用系统分配的标识。

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>在创建虚拟机规模集的过程中启用系统分配的标识

当前，Azure 门户不支持在创建虚拟机规模集期间启用系统分配的标识。 请改为参考以下虚拟机规模集创建快速入门文章来首先创建虚拟机规模集，然后，转到下一部分来了解有关在虚拟机规模集上启用系统分配的标识的详细信息：

- [在 Azure 门户中创建虚拟机规模集](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-identity-on-an-existing-virtual-machine-scale-set"></a>在现有虚拟机规模集上启用系统分配的标识

若要在初始预配时没有启用系统分配的标识的虚拟机规模集上启用系统分配的标识，请执行以下操作：

1. 使用与包含虚拟机规模集的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。

2. 导航到所需的虚拟机规模集。

3. 在“系统分配”、“状态”下，选择“开启”，然后单击“保存”：

   [![“配置”页屏幕截图](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

### <a name="remove-system-assigned-identity-from-a-virtual-machine-scale-set"></a>从虚拟机规模集中删除系统分配的标识

如果虚拟机规模集不再需要系统分配的标识，请执行以下操作：

1. 使用与包含虚拟机规模集的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。 此外，请确保该帐户属于可授予对虚拟机规模集的写权限的角色。

2. 导航到所需的虚拟机规模集。

3. 在“系统分配”、“状态”下，选择“关闭”，然后单击“保存”：

   ![“配置”页屏幕截图](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-identity"></a>用户分配标识

在本部分中，你将学习如何使用 Azure 门户从虚拟机规模集中添加和删除用户分配的标识。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>在创建虚拟机规模集的过程中分配用户分配的标识

当前，Azure 门户不支持在创建虚拟机规模集期间分配用户分配的标识。 请改为参考以下虚拟机规模集创建快速入门文章来首先创建虚拟机规模集，然后，转到下一部分来了解有关为其分配用户分配的标识的详细信息：

- [在 Azure 门户中创建虚拟机规模集](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-identity-to-an-existing-virtual-machine-scale-set"></a>将用户分配的标识分配到现有虚拟机规模集

1. 使用与包含虚拟机规模集的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。
2. 导航到所需的虚拟机规模集，依次单击“标识”、“用户分配”和“添加”。**\+**

   ![向 VMSS 添加用户分配的标识](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. 单击要添加到虚拟机规模集的用户分配的标识，然后单击“添加”。
   
   ![向 VMSS 添加用户分配的标识](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>从虚拟机规模集中删除用户分配的标识

1. 使用已与包含 VM 的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。
2. 导航到所需的虚拟机规模集，然后依次单击“标识”、“用户分配”、要删除的用户分配标识，然后单击“删除”（在确认窗格中单击“是”）。

   ![从 VMSS 中删除用户分配的标识](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="related-content"></a>相关内容

- 有关托管服务标识的概述，请参阅[概述](overview.md)。

## <a name="next-steps"></a>后续步骤

- 使用 Azure 门户，授予 Azure 虚拟机规模集托管服务标识[对另一个 Azure 资源的访问权限](howto-assign-access-portal.md)。

使用以下评论部分提供反馈，帮助我们改进内容。
