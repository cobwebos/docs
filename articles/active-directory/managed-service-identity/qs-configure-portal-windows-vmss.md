---
title: "使用 Azure 门户在 Azure 虚拟机规模集上配置 MSI"
description: "分步说明如何使用 Azure 门户在 Azure VMSS 上配置托管服务标识 (MSI)。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 4d308d9cdc0405ee0041c877f5678647343631ab
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="configure-an-azure-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>使用 Azure 门户配置 Azure 虚拟机规模集托管服务标识 (MSI)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文介绍如何使用 Azure 门户为 Azure 虚拟机规模集启用和删除 MSI。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-azure-virtual-machine-scale-set"></a>在创建 Azure 虚拟机规模集的过程中启用 MSI

截止本文撰写之时，不支持使用 Azure 门户在虚拟机规模集创建过程中启用 MSI。 而是，请参考以下“Azure 虚拟机规模集创建快速入门”一文首先创建 Azure 虚拟机规模集：

- [在 Azure 门户中创建虚拟机规模集](../../virtual-machine-scale-sets/virtual-machine-scale-sets-create-portal.md)  

然后，转到下一部分，详细了解如何在虚拟机规模集上启用 MSI。

## <a name="enable-msi-on-an-existing-azure-vmms"></a>在现有 Azure VMMS 上启用 MSI

如果虚拟机规模集最初预配时没有启用 MSI，请执行以下操作：

1. 使用与包含虚拟机规模集的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。

2. 导航到所需的虚拟机规模集。

3. 单击“配置”页，通过在“托管服务标识”下选择“是”在虚拟机规模集上启用 MSI，再单击“保存”。 此操作可能需要 60 秒或更长时间才能完成：

   ![“配置”页屏幕截图](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>从 Azure 虚拟机规模集中删除 MSI

如果虚拟机规模集不再需要 MSI，请执行以下操作：

1. 使用与包含虚拟机规模集的 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)。 此外，请确保该帐户属于可授予对虚拟机规模集的写权限的角色。

2. 导航到所需的虚拟机规模集。

3. 单击“配置”页，通过在“托管服务标识”下选择“否”从虚拟机规模集中删除 MSI，再单击“保存”。 此操作可能需要 60 秒或更长时间才能完成：

   ![“配置”页屏幕截图](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="next-steps"></a>后续步骤

- 有关 MSI 的概述，请参阅[托管服务标识概述](overview.md)。
- 使用 Azure 门户，授予 Azure 虚拟机规模集的 MSI [对另一个 Azure 资源的访问权限](howto-assign-access-portal.md)。

使用以下评论部分提供反馈，帮助我们改进内容。
