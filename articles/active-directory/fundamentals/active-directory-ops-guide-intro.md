---
title: Azure 活动目录操作参考指南
description: 此操作参考指南描述为确保和维护标识和访问管理、身份验证、治理和操作而应采取的检查和操作
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535309"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Azure 活动目录操作参考指南

此操作参考指南描述了为确保和维护以下区域而应采取的检查和操作：

- **[身份和访问管理](active-directory-ops-guide-iam.md)**- 管理身份及其权利生命周期的能力。
- **[身份验证管理](active-directory-ops-guide-auth.md)**- 能够管理凭据、定义身份验证体验、委派分配、测量使用情况以及根据企业安全状况定义访问策略。
- **[治理](active-directory-ops-guide-govern.md)**- 评估和证明授予的非特权和特权标识、审核和控制环境更改的访问的能力。
- **[操作](active-directory-ops-guide-ops.md)**- 优化操作 Azure 活动目录 （Azure AD）。

此处的某些建议可能不适用于所有客户的环境，例如，如果您的组织使用密码哈希同步，则 AD FS 最佳实践可能不适用。

> [!NOTE]
> 这些建议自发布之日起是最新的，但可能会随时间而变化。 随着 Microsoft 产品和服务随时间推移而变化，组织应持续评估其身份实践。 当组织订阅其他 Azure AD 高级许可证时，建议可能会更改。 例如，Azure AD 高级 P2 将包含更多治理建议。

## <a name="stakeholders"></a>利益 相关 者

本指南中的每个部分都建议分配利益相关者以成功规划和实施关键任务。 下表概述了本指南中所有利益相关者的列表：

| 利益干系人 | 描述 |
| :- | :- |
| IAM 运营团队 | 此团队负责管理身份和访问管理系统的日常操作 |
| 生产力团队 | 该团队拥有并管理工作效率应用程序，如电子邮件、文件共享和协作、即时消息和会议。 |
| 应用程序所有者 | 此团队从企业拥有特定应用程序，通常从组织中的技术角度拥有该应用程序。 |
| InfoSec 架构团队 | 该团队计划并设计组织的信息安全实践。 |
| InfoSec 运营团队 | 此团队运行并监视 InfoSec 体系结构团队的已实施信息安全实践。 |

## <a name="next-steps"></a>后续步骤

开始使用[标识和访问管理检查和操作](active-directory-ops-guide-iam.md)。
