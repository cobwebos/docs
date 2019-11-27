---
title: Azure Active Directory 操作参考指南
description: 此操作参考指南介绍了保护和维护标识和访问管理、身份验证、管理和操作应该采取的检查和操作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535309"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Azure Active Directory 操作参考指南

此操作参考指南介绍了保护和维护以下方面应采取的检查和操作：

- **[标识和访问管理](active-directory-ops-guide-iam.md)** -能够管理标识及其权利的生命周期。
- **[身份验证管理](active-directory-ops-guide-auth.md)** -管理凭据、定义身份验证体验、委派分配、衡量使用情况以及基于企业安全状态定义访问策略的能力。
- **[调控](active-directory-ops-guide-govern.md)** 能力，能够评估和证明授予了对环境的非特权和特权标识、审核和控制更改的访问权限。
- **[操作](active-directory-ops-guide-ops.md)** -优化操作 Azure Active Directory （Azure AD）。

此处的某些建议可能不适用于所有客户的环境，例如，如果你的组织使用密码哈希同步，则 AD FS 最佳实践可能不适用。

> [!NOTE]
> 这些建议是发布日期之后的最新建议，但会随时间变化。 组织应持续评估其身份实践，因为 Microsoft 产品和服务随时间推移而发展。 当组织订阅不同 Azure AD Premium 许可证时，建议会发生变化。 例如，Azure AD Premium P2 将包含更多监管建议。

## <a name="stakeholders"></a>利益干系人

本参考指南中的每个部分都建议分配利益干系人成功计划和实现关键任务。 下表概述了本指南中所有利益干系人的列表：

| 利益干系人 | 描述 |
| :- | :- |
| IAM 操作团队 | 此团队处理标识和访问管理系统的日常操作 |
| 生产力团队 | 此团队拥有和管理工作效率应用程序，如电子邮件、文件共享和协作、即时消息和会议。 |
| 应用程序所有者 | 此团队拥有来自企业的特定应用程序，通常是组织的技术观点。 |
| InfoSec 体系结构团队 | 此团队计划并设计组织的信息安全做法。 |
| InfoSec 运营团队 | 此团队运行并监视 InfoSec 体系结构团队实施的信息安全做法。 |

## <a name="next-steps"></a>后续步骤

开始使用[标识和访问管理检查和操作](active-directory-ops-guide-iam.md)。
