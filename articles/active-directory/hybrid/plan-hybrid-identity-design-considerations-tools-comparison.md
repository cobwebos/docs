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
ms.openlocfilehash: d9c8f5a75fea6ddd85026407fe4784c8a4589e33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89278303"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>混合标识目录集成工具比较
过去数年以来，集成工具已得到发展和演进。  


- [FIM](/previous-versions/windows/desktop/forefront-2010/ff182370(v=vs.100)) 和 [MIM](/microsoft-identity-manager/microsoft-identity-manager-2016) 仍受支持，并且主要启用本地系统之间的同步。   Fim 和 MIM 都支持 [Fim Windows Azure AD 连接器](/previous-versions/mim/dn511001(v=ws.10)) ，但建议不要用于新部署-具有本地源（如 NOTES 或 SAP HCM）的客户应使用 MIM 填充 Active Directory 域服务 (AD DS) ，然后使用 Azure AD Connect 同步或 Azure AD Connect 云预配从 AD DS 同步到 Azure AD。
- [Azure AD Connect 同步](how-to-connect-sync-whatis.md) 合并了以前在 DirSync 和 Azure AD Sync 中发布的组件和功能，以便在 AD DS 林和 Azure AD 之间进行同步。  
- [Azure AD Connect 云预配](../cloud-provisioning/what-is-cloud-provisioning.md) 是用于从 AD DS 同步到 Azure AD 的新 Microsoft 代理，适用于合并和收购等方案，其中获取的公司的 ad 林与父公司的 ad 林相隔离。

若要详细了解 Azure AD Connect 同步和 Azure AD Connect 云预配之间的差异，请参阅 [什么是 Azure AD Connect 云预配？](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。