---
title: 混合标识：目录集成工具比较 | Microsoft Docs
description: 本页将提供针对可用于目录集成的各种目录集成工具进行比较的综合表。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 052d99a819aee415d5e7ad6dc00b8c786af0f636
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811102"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>混合标识目录集成工具比较
过去数年以来，集成工具已得到发展和演进。  


- [FIM](https://docs.microsoft.com/previous-versions/windows/desktop/forefront-2010/ff182370%28v%3dvs.100%29)和[MIM](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016)仍然受支持，主要支持本地系统之间的同步。   FIM 和 MIM 都支持[FIM Windows Azure AD 连接器](https://docs.microsoft.com/previous-versions/mim/dn511001(v=ws.10)?redirectedfrom=MSDN)，但不建议用于新部署 - 具有本地源（如 Notes 或 SAP HCM）的客户应使用 MIM 填充活动目录域服务 （AD DS），然后使用 Azure AD 连接同步或 Azure AD 连接云配置从 AD DS 同步到 Azure AD。
- [Azure AD 连接同步](how-to-connect-sync-whatis.md)合并了之前在 DirSync 和 Azure AD 同步中发布的组件和功能，用于在 AD DS 林和 Azure AD 之间进行同步。  
- [Azure AD Connect 云配置](../cloud-provisioning/what-is-cloud-provisioning.md)是一种新的 Microsoft 代理，用于从 AD DS 到 Azure AD 进行同步，适用于合并和获取等方案，其中被收购公司的 AD 林与母公司的 AD 林隔离。

要了解有关 Azure AD 连接同步和 Azure AD 连接云预配之间的区别，请参阅文章["什么是 Azure AD 连接云资源？](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>后续步骤
详细了解[将本地标识与 Azure 活动目录集成](whatis-hybrid-identity.md)。

