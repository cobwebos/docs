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
ms.openlocfilehash: 6ba090065b18a44cc1f01a62eefb5dcf52bcf356
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213259"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>使用 Azure 门户配置 VM 托管服务标识

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文将介绍如何使用 Azure 门户为 Azure VM 启用和禁用系统分配标识。 当前不支持通过 Azure 门户在 Azure VM 中分配和移除用户分配标识。

> [!NOTE]
> 目前，不支持通过 Azure 门户执行用户分配标识操作。 请关注后续更新。 

## <a name="prerequisites"></a>先决条件

- 如果不熟悉托管服务标识，请查阅[概述部分](overview.md)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要执行本文中的管理操作，帐户需要分配以下角色：
    - [虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)，可从 Azure VM 启用和删除系统分配的标识。

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>创建 Azure VM 过程中的托管服务标识

目前，通过 Azure 门户创建 VM 不支持托管服务标识操作。 而是请参阅以下 VM 创建快速入门文章之一，先创建一个 VM：

- [使用 Azure 门户创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [使用 Azure 门户创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

然后，转到下一部分，详细了解如何在 VM 上启用托管服务标识。

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>在现有 Azure VM 上启用托管服务标识

若要在初始预配时没有系统分配标识的 VM 上启用系统分配标识：

1. 使用已与包含 VM 的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。

2. 导航到所需的虚拟机，然后选择“配置”页。

3. 通过选择“是”在 VM 上启用系统分配标识，然后在“托管服务标识”下单击“保存”。 此操作可能需要 60 秒或更长时间才能完成：

   > [!NOTE]
   > 不支持通过 Azure 门户向 VM 添加用户分配标识。

   ![“配置”页屏幕截图](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>从 Azure VM 移除托管服务标识

如果虚拟机不再需要系统分配标识，请执行以下操作：

1. 使用已与包含 VM 的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。 

2. 导航到所需的虚拟机，然后选择“配置”页。

3. 通过选择“否”在 VM 上禁用系统分配标识，然后在“托管服务标识”下单击“保存”。 此操作可能需要 60 秒或更长时间才能完成：

    > [!NOTE]
    > 不支持通过 Azure 门户向 VM 添加用户分配标识。

   ![“配置”页屏幕截图](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>相关内容

- 有关托管服务标识的概述，请参阅[概述](overview.md)。

## <a name="next-steps"></a>后续步骤

- 使用 Azure 门户，授予 Azure VM 的托管服务标识[对另一个 Azure 资源的访问权限](howto-assign-access-portal.md)。

