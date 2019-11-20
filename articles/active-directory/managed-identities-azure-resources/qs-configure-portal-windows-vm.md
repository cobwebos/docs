---
title: 使用 Azure 门户 Azure AD 配置托管标识
description: 分步说明如何使用 Azure 门户在 Azure VM 上配置 Azure 资源托管标识。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75971dbd35a6b29306bc87988423ab662a343705
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183873"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>使用 Azure 门户在 VM 上配置 Azure 资源托管标识

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文介绍如何使用 Azure 门户为 Azure 虚拟机 (VM) 启用和禁用系统和用户分配的托管标识。 

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识

本部分介绍如何使用 Azure 门户为 VM 启用和禁用系统分配托管标识。

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>在创建 VM 的过程中启用系统分配托管标识

若要在 VM 创建期间在 VM 上启用系统分配的托管标识，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

- 在“标识”部分的“管理”选项卡下，将“托管服务标识”切换到“开”。  

![在创建 VM 期间启用系统分配的标识](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

请参阅以下快速入门以创建 VM： 

- [使用 Azure 门户创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [使用 Azure 门户创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>在现有 VM 上启用系统分配托管标识

若要在最初未预配系统分配的托管标识的 VM 上启用该托管标识，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

1. 使用已与包含 VM 的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。

2. 导航到所需的虚拟机，然后选择“标识”。

3. 在“系统分配”、“状态”下，选择“开启”，然后单击“保存”：

   ![“配置”页屏幕截图](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>从 VM 删除系统分配托管标识

若要从 VM 中删除系统分配的托管标识，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

如果虚拟机不再需要系统分配托管标识，请执行以下操作：

1. 使用已与包含 VM 的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。 

2. 导航到所需的虚拟机，然后选择“标识”。

3. 在“系统分配”、“状态”下，选择“关闭”，然后单击“保存”：

   ![“配置”页屏幕截图](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>用户分配的托管标识

 本部分介绍如何使用 Azure 门户在 VM 中添加和删除用户分配托管标识。

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>在创建 VM 期间分配用户分配标识

若要将用户分配的标识分配给 VM，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)和[托管标识操作员](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色分配。 无需其他 Azure AD 目录角色分配。

当前，Azure 门户不支持在创建 VM 期间分配用户分配托管标识。 请改为参考以下 VM 创建快速入门文章之一来首先创建 VM，然后前进到下一部分来了解有关为 VM 分配用户分配托管标识的详细信息：

- [使用 Azure 门户创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [使用 Azure 门户创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>向现有 VM 分配用户分配托管标识

若要将用户分配的标识分配给 VM，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)和[托管标识操作员](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色分配。 无需其他 Azure AD 目录角色分配。

1. 使用已与包含 VM 的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。
2. 导航到所需的 VM，依次单击“标识”、“用户分配”和“添加”。 **\+**

   ![向 VM 添加用户分配托管标识](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. 单击要添加到 VM 的用户分配的标识，然后单击“添加”。

    ![向 VM 添加用户分配托管标识](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>从 VM 中删除用户分配托管标识

若要从 VM 中删除用户分配的标识，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。 无需其他 Azure AD 目录角色分配。

1. 使用已与包含 VM 的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。
2. 导航到所需的 VM，然后依次单击“标识”、“用户分配”、要删除的用户分配托管标识，然后单击“删除”（在确认窗格中单击“是”）。

   ![从 VM 中删除用户分配托管标识](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>后续步骤

- 使用 Azure 门户，授予 Azure VM 的托管标识[对另一个 Azure 资源的访问权限](howto-assign-access-portal.md)。

