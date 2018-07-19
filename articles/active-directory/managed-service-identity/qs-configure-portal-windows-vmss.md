---
title: 使用 Azure 门户在 Azure 虚拟机规模集上配置 MSI
description: 分步说明如何使用 Azure 门户在 Azure VMSS 上配置托管服务标识 (MSI)。
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
ms.openlocfilehash: 8779600f2c85a8bb309f7b2a8874608170de8877
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035235"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>使用 Azure 门户配置虚拟机规模集托管服务标识 (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文将介绍如何使用 Azure 门户为虚拟机规模集启用和禁用系统分配的标识。 当前不支持通过 Azure 门户在 Azure 虚拟机规模集中分配和移除用户分配的标识。

> [!NOTE]
> 目前，不支持通过 Azure 门户对用户分配的标识进行操作。 请关注后续更新。

## <a name="prerequisites"></a>先决条件


- 如果不熟悉托管服务标识，请查阅[概述部分](overview.md)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>创建 Azure 虚拟机规模集过程中的托管服务标识

目前，通过 Azure 门户创建 VM 不支持托管服务标识操作。 而是，请参考以下“Azure 虚拟机规模集创建快速入门”一文首先创建 Azure 虚拟机规模集：

- [在 Azure 门户中创建虚拟机规模集](../../virtual-machine-scale-sets/quick-create-portal.md)  

然后，转到下一部分，详细了解如何在虚拟机规模集上启用 MSI。

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>在现有 Azure VMSS 上启用托管服务标识

若要在初始预配时没有系统分配标识的 VM 上启用系统分配标识：

1. 使用与包含虚拟机规模集的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。

2. 导航到所需的虚拟机规模集。

3. 通过选择“是”在 VM 上启用系统分配标识，然后在“托管服务标识”下单击“保存”。 此操作可能需要 60 秒或更长时间才能完成：

   [![“配置”页屏幕截图](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>从 Azure 虚拟机规模集移除托管服务标识

如果虚拟机规模集不再需要 MSI，请执行以下操作：

1. 使用与包含虚拟机规模集的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。 此外，请确保该帐户属于可授予对虚拟机规模集的写权限的角色。

2. 导航到所需的虚拟机规模集。

3. 通过选择“否”在 VM 上禁用系统分配标识，然后在“托管服务标识”下单击“保存”。 此操作可能需要 60 秒或更长时间才能完成：

   ![“配置”页屏幕截图](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>相关内容

- 有关托管服务标识的概述，请参阅[概述](overview.md)。

## <a name="next-steps"></a>后续步骤

- 使用 Azure 门户，授予 Azure 虚拟机规模集的 MSI [对另一个 Azure 资源的访问权限](howto-assign-access-portal.md)。

使用以下评论部分提供反馈，帮助我们改进内容。
