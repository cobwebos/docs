---
title: 如何在 Azure VM 上使用 Azure 门户配置托管服务标识
description: 逐步介绍了如何使用 Azure 门户在 Azure VM 上配置托管服务标识。
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
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 552fce2ffd8b6bd786010da82e702ee98c3f8647
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888550"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>使用 Azure 门户配置 VM 托管服务标识

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

在本文中，你将学习如何使用 Azure 门户为 Azure 虚拟机 (VM) 启用和禁用系统和用户分配的标识。 

## <a name="prerequisites"></a>先决条件

- 如果不熟悉托管服务标识，请查阅[概述部分](overview.md)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要执行本文中的管理操作，帐户需要分配以下角色：
    - [虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)，可从 Azure VM 启用和删除系统分配的标识。

## <a name="system-assigned-identity"></a>系统分配标识

在本部分中，你将学习如何使用 Azure 门户为 VM 启用和禁用系统分配的标识。

### <a name="enable-system-assigned-identity-during-creation-of-a-vm"></a>在创建 VM 期间启用系统分配的标识

当前，Azure 门户不支持在创建 VM 期间启用系统分配的标识。 请改为参考以下 VM 创建快速入门文章之一来首先创建 VM，然后前进到下一部分来了解有关在 VM 上启用系统分配的标识的详细信息：

- [使用 Azure 门户创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [使用 Azure 门户创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

### <a name="enable-system-assigned-identity-on-an-existing-vm"></a>在现有 VM 上启用系统分配的标识

若要在初始预配时没有系统分配标识的 VM 上启用系统分配标识：

1. 使用已与包含 VM 的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。

2. 导航到所需的虚拟机，然后选择“标识”。

3. 在“系统分配”、“状态”下，选择“开启”，然后单击“保存”：

   ![“配置”页屏幕截图](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-identity-from-a-vm"></a>从 VM 中删除系统分配的标识

如果虚拟机不再需要系统分配标识，请执行以下操作：

1. 使用已与包含 VM 的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。 

2. 导航到所需的虚拟机，然后选择“标识”。

3. 在“系统分配”、“状态”下，选择“关闭”，然后单击“保存”：

   ![“配置”页屏幕截图](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-identity"></a>用户分配标识

 在本部分中，你将学习如何使用 Azure 门户在 VM 中添加和删除用户分配的标识。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>在创建 VM 期间分配用户分配的标识

当前，Azure 门户不支持在创建 VM 期间分配用户分配的标识。 请改为参考以下 VM 创建快速入门文章之一来首先创建 VM，然后前进到下一部分来了解有关为 VM 分配用户分配的标识的详细信息：

- [使用 Azure 门户创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [使用 Azure 门户创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-identity-to-an-existing-vm"></a>向现有 VM 分配用户分配的标识

1. 使用已与包含 VM 的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。
2. 导航到所需的 VM，依次单击“标识”、“用户分配”和“添加”。**\+**

   ![向 VM 添加用户分配的标识](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. 单击要添加到 VM 的用户分配的标识，然后单击“添加”。

    ![向 VM 添加用户分配的标识](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-identity-from-a-vm"></a>从 VM 中删除用户分配的标识

1. 使用已与包含 VM 的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。
2. 导航到所需的 VM，然后依次单击“标识”、“用户分配”、要删除的用户分配标识，然后单击“删除”（在确认窗格中单击“是”）。

   ![从 VM 中删除用户分配的标识](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="related-content"></a>相关内容

- 有关托管服务标识的概述，请参阅[概述](overview.md)。

## <a name="next-steps"></a>后续步骤

- 使用 Azure 门户，授予 Azure VM 的托管服务标识[对另一个 Azure 资源的访问权限](howto-assign-access-portal.md)。

