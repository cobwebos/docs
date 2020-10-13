---
author: mattchenderson
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: mahender
ms.openlocfilehash: 3b282a99bb7f6f107717d9c265a46d285d03b849
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83649103"
---
借助 Azure Active Directory (Azure AD) 的托管标识，应用可以轻松访问其他受 Azure AD 保护的资源（如 Azure Key Vault）。 标识由 Azure 平台托管，无需设置或转交任何机密。 有关 Azure AD 中的托管标识的详细信息，请参阅 [Azure 资源的托管标识](../articles/active-directory/managed-identities-azure-resources/overview.md)。

你的应用程序可以被授予两种类型的标识：

- 系统分配的标识与你的应用程序相绑定，如果删除应用，标识也会被删除。 一个应用只能具有一个系统分配的标识。
- 用户分配的标识是可以分配给应用的独立 Azure 资源。 一个应用可以具有多个用户分配的标识。