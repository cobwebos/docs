---
title: Azure Active Directory B2C 中的用户流版本 | Microsoft Docs
description: 了解 Azure Active Directory B2C 中可用的用户流版本。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67949c31c710d88a05e1e110860fe703caf66d04
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87481319"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的用户流版本

Azure Active Directory B2C (Azure AD B2C) 中的用户流可帮助设置完全描述客户标识体验的常见[策略](user-flow-overview.md)。 这些体验包括注册、登录、密码重置和配置文件编辑。 下表描述了 Azure AD B2C 中可用的用户流。

> [!IMPORTANT]
> 我们改变了引用用户流版本的方式。 以前，我们提供了 V1 （生产就绪）版本、1.1 版和 V2 （预览版）。 现在，我们已将用户流合并为两个版本：
>
>- **建议**用户流是用户流的新预览版本。 它们经过全面测试，并结合了旧版**V2**和**1.1**版本的所有功能。 今后，将维护并更新新的建议用户流。 转到这些新的建议用户流后，就可以在新功能发布后对其进行访问。
>- **标准**用户流（以前称为**V1**）通常是可用的、生产就绪的用户流。 如果你的用户流非常关键并且依赖于高度稳定的版本，你可以继续使用标准用户流，这会认识到这些版本不会进行维护和更新。
>
>所有旧版预览版用户流（1.1 版和 V2）都在**2021 年8月1日**之前的路径上过时。 我们强烈建议您尽可能快[地切换到新的**推荐**版本](#how-to-switch-to-a-new-recommended-user-flow)，以便您始终可以利用最新的功能和更新。 *这些更改仅适用于 Azure 公有云。其他环境将继续使用[旧的用户流版本控制](user-flow-versions-legacy.md)。*

## <a name="recommended-user-flows"></a>推荐的用户流

建议的用户流是预览版本，它将新功能与旧版 V2 和 v1.1 功能组合在一起。 今后，将维护并更新建议的用户流。

| 用户流 | 说明 |
| --------- | ----------- |
| 密码重置（预览） | 允许用户在验证电子邮件后选择新密码。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>令牌兼容性设置</li><li>[年龄限制](basic-age-gating.md)</li><li>[密码复杂性要求](user-flow-password-complexity.md)</li></ul> |
| 配置文件编辑（预览） | 允许用户配置用户特性。 使用此用户流，可配置： <ul><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li></ul> |
| 登录（预览） | 允许用户登录帐户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>[年龄限制](basic-age-gating.md)</li><li>登录页自定义</li></ul> |
| 注册（预览） | 允许用户创建账户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>[年龄限制](basic-age-gating.md)</li><li>[密码复杂性要求](user-flow-password-complexity.md)</li></ul> |
| 注册和登录（预览） | 允许用户创建帐户或登录帐户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[年龄限制](basic-age-gating.md)</li><li>[密码复杂性要求](user-flow-password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>标准用户流

标准用户流（以前称为 V1）已正式发布，已准备就绪的用户流。 标准用户流将不会更新。

| 用户流 | 说明 |
| --------- | ----------- | ----------- |
| 密码重置 | 允许用户在验证电子邮件后选择新密码。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>令牌兼容性设置</li><li>[密码复杂性要求](user-flow-password-complexity.md)</li></ul> |
| 配置文件编辑 | 允许用户配置用户特性。 使用此用户流，可配置： <ul><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li></ul> |
| 登录 | 允许用户登录帐户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>阻止登录</li><li>强制执行密码重置</li><li>使我保持登录状态 (KMSI)</ul><br>无法使用此用户流自定义用户界面。 |
| 注册 | 允许用户创建账户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>[密码复杂性要求](user-flow-password-complexity.md)</li></ul> |
| 注册和登录 | 允许用户创建帐户或登录帐户。 使用此用户流，可配置： <ul><li>[多重身份验证](custom-policy-multi-factor-authentication.md)</li><li>[令牌生存期](tokens-overview.md)</li><li>令牌兼容性设置</li><li>会话行为</li><li>[密码复杂性要求](user-flow-password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>如何切换到新的建议用户流

若要从用户的旧版本切换到新的**建议**预览版本，请执行以下步骤：

1. 按照[教程：在 Azure Active Directory 中创建用户流中](tutorial-create-user-flows.md)的步骤创建新的用户流策略。 创建用户流时，选择**建议**的版本。

3. 用在旧版策略中配置的相同设置来配置新的用户流。

4. 将应用程序登录 URL 更新到新创建的策略。

5. 测试用户流并确认其正常工作后，请按照以下步骤操作，删除旧用户流：
   1. 在 Azure AD B2C 租户概述 "菜单中，选择"**用户流**"。
   2. 查找要删除的用户流。
   3. 在最后一列中，选择上下文菜单（**...**），然后选择 "**删除**"。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>是否仍可创建旧版 V2 和 v1.1 用户流？

你将无法基于旧版 V2 和1.1 版创建新的用户流，但你可以继续读取、更新和删除当前正在使用的任何旧版 V2 和 v1.1 用户流。

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>是否有任何理由继续使用旧版 V2 和 v1.1 用户流？

不完全是。 新**建议**的预览版版本包含与旧版 V2 和1.1 版本相同的功能。 没有删除任何内容，事实上它们现在包含其他功能。

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>如果我不从旧版 V2 和1.1 版策略进行切换，该策略会如何影响应用程序？

如果使用的是旧版 V2 或1.1 用户流，则不会影响应用程序的此版本更改。 但是，若要访问新功能或后续策略更改，你需要切换到新的**建议**版本。

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Microsoft 仍支持旧版 V2 或1.1 用户流策略吗？

旧的 V2 和1.1 版用户流将继续完全受支持。
