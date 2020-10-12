---
title: 使用 Azure 门户授予托管标识对资源的访问权限 - Azure AD
description: 分步说明如何使用 Azure 门户授予一个资源上的托管标识对另一个资源的访问权限。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee28addd5f6ac0faaf03d29d712c4e750ed6e293
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89177476"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>使用 Azure 门户授予托管标识对资源的访问权限

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

为 Azure 资源配置托管标识后，便可以授予该托管标识对另一资源的访问权限，这一点与所有安全主体一样。 本文介绍如何使用 Azure 门户授予 Azure 虚拟机或虚拟机规模集的托管标识对 Azure 存储帐户的访问权限。

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#managed-identity-types)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>使用 Azure RBAC 授予托管标识对另一资源的访问权限

在 Azure 资源（如 [Azure VM](qs-configure-portal-windows-vm.md) 或 [Azure 虚拟机规模集](qs-configure-portal-windows-vmss.md)）上启用托管标识后，请执行以下操作：

1. 使用帐户登录 [Azure 门户](https://portal.azure.com)，此帐户与已在其下配置托管标识的 Azure 订阅相关联。

2. 转到要对其修改访问控制的相应资源。 此示例要授予 Azure 虚拟机对存储帐户的访问权限，所以导航到存储帐户。

3. 选择资源的“访问控制(IAM)”页面，然后选择“+ 添加角色分配” 。 然后依次指定“角色”、“将访问权限分配到”和相应的“订阅”  。 在搜索条件区域下，应该会看到该资源。 选择该资源，并选择“保存”。 

   ![“访问控制(IAM)”屏幕截图](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>后续步骤

- [Azure 资源托管标识概述](overview.md)
- 若要启用 Azure 虚拟机上的托管标识，请参阅[使用 Azure 门户在 VM 上配置 Azure 资源托管标识](qs-configure-portal-windows-vm.md)。
- 若要启用 Azure 虚拟机规模集上的托管标识，请参阅[使用 Azure 门户在虚拟机规模集上配置 Azure 资源托管标识](qs-configure-portal-windows-vmss.md)。


