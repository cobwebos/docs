---
title: "如何使用 Azure 门户在 Azure VM 上配置 MSI"
description: "逐步介绍了如何使用 Azure 门户在 Azure VM 上配置托管服务标识 (MSI)。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: bryanla
ms.openlocfilehash: 09ec59f9239e08005732b120ca653dcc833708a8
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>使用 Azure 门户配置 VM 托管服务标识 (MSI)

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文将介绍如何使用 Azure 门户为 Azure VM 启用和删除 MSI。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>在 Azure VM 创建过程中启用 MSI

截止本文撰写之时，不支持使用 Azure 门户在 VM 创建过程中启用 MSI。 而是请参阅以下 VM 创建快速入门文章之一，先创建一个 VM：

- [使用 Azure 门户创建 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [使用 Azure 门户创建 Linux 虚拟机](../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

然后，转到下一部分，详细了解如何在 VM 上启用 MSI。

## <a name="enable-msi-on-an-existing-azure-vm"></a>在现有 Azure VM 上启用 MSI

如果 VM 最初预配时没有启用 MSI，请执行以下操作：

1. 使用已与包含 VM 的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。 此外，请确保该帐户属于可授予对 VM 的写权限的角色，如“虚拟机参与者”。

2. 转到相应虚拟机。

2. 单击“配置”页，在“托管服务标识”下选择“是”，在 VM 上启用 MSI，再单击“保存”。 此操作可能需要 60 秒或更长时间才能完成：

   ![“配置”页屏幕截图](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>在 Azure VM 上禁用 MSI

如果虚拟机不再需要 MSI，请执行以下操作：

1. 使用已与包含 VM 的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。 此外，请确保该帐户属于可授予对 VM 的写权限的角色，如“虚拟机参与者”。

2. 转到相应虚拟机。

3. 单击“配置”页，在“托管服务标识”下选择“否”，在 VM 上禁用 MSI，再单击“保存”。 此操作可能需要 60 秒或更长时间才能完成：

   ![“配置”页屏幕截图](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>相关内容

- 有关 MSI 的概述，请参阅[托管服务标识概述](msi-overview.md)。

## <a name="next-steps"></a>后续步骤

- 使用 Azure 门户，授予 Azure VM 的 MSI [对另一个 Azure 资源的访问权限](msi-howto-assign-access-portal.md)。

使用以下评论部分提供反馈，帮助我们改进内容。
