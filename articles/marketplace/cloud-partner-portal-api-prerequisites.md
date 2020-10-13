---
title: API 先决条件-Azure Marketplace
description: 使用云合作伙伴门户 Api 的先决条件。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: 374cc3398d1037b19ceddcbdaafd333f643e44ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91261152"
---
# <a name="api-prerequisites"></a>API 先决条件

> [!NOTE]
> 云合作伙伴门户 Api 与集成，并将在合作伙伴中心继续工作。 转换引入了少量更改。 查看 [云合作伙伴门户 API 参考](cloud-partner-portal-api-overview.md) 中列出的更改，确保你的代码在转换到合作伙伴中心后继续工作。 仅将 CPP Api 用于已集成的现有产品，然后过渡到合作伙伴中心;新产品应使用合作伙伴中心提交 Api。

需要两个必需的编程资产才能使用云合作伙伴门户 Api：服务主体和 Azure Active Directory (Azure AD) 访问令牌。

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>在 Azure Active Directory 租户中创建服务主体

首先，需要在你的 Azure AD 租户中创建一个服务主体。 将在云合作伙伴门户中为此租户分配其自己的一组权限。 你的代码将使用此租户而不是个人凭据调用 Api。 有关创建服务主体的完整说明，请参阅 [如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="add-service-principal-to-your-account"></a>将服务主体添加到帐户

现在，你已在租户中创建服务主体，你可以将其作为用户添加到合作伙伴中心门户帐户中。 就像用户一样，服务主体可以成为门户的所有者或参与者。 有关详细信息，请参阅下面的 **后续步骤** 。

## <a name="next-steps"></a>后续步骤

请参阅 [管理 Azure AD 应用程序](partner-center-portal/manage-account.md#manage-azure-ad-applications)。
