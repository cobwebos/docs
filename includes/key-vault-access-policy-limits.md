---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: c74f2543e96087378741d6388b7c27dc4d05ddc8
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380694"
---
Key vault 最多支持1024个访问策略条目，每个条目向特定安全主体授予一组不同的权限。 由于此限制，我们建议在可能的情况下，将访问策略分配给用户组，而不是单个用户。 使用组可以更轻松地管理组织中多个人员的权限。 有关详细信息，请参阅 [使用 Azure Active Directory 组管理应用和资源访问](/azure/active-directory/fundamentals/active-directory-manage-groups)

有关 Key Vault 访问控制的完整详细信息，请参阅 [Azure Key Vault 安全性：标识和访问管理](/azure/key-vault/general/overview-security#identity-and-access-management)。 
