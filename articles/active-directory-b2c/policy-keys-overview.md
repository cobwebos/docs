---
title: 策略密钥概述 - Azure Active Directory B2C
description: 了解 Azure Active Directory B2C 中可以用于对令牌、客户端机密、证书和密码进行签名和验证的加密策略密钥的类型。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8019c049d830df0c2f3301a450eed60145c8eab3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89570421"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的策略密钥概述

Azure Active Directory B2C (Azure AD B2C) 以策略密钥的形式存储机密和证书，从而与它所集成的服务建立信任。 这些信任包括：

- 外部标识提供者
- 与 [REST API 服务](restful-technical-profile.md)进行连接
- 令牌签名和加密

 本文讨论了对于 Azure AD B2C 使用的策略密钥，你需要了解的内容。

> [!NOTE]
> 目前，策略密钥的配置仅限于 [自定义策略](active-directory-b2c-get-started-custom.md) 。

你可以在 Azure 门户中的“策略密钥”菜单下配置用于在服务之间建立信任的机密和证书。 密钥可以是对称的，也可以是非对称的。 在对称加密或私钥加密中，将使用共享机密来加密和解密数据。 非对称加密或公钥加密这种加密系统使用密钥对，其中包含与信赖方应用程序共享的公钥，以及只有 Azure AD B2C 知道的私钥。

## <a name="policy-keyset-and-keys"></a>策略密钥集和密钥

Azure AD B2C 中的策略密钥的顶层资源是**密钥集**容器。 每个密钥集都包含至少一个**密钥**。 密钥具有以下属性：

| Attribute |  必需 | 备注 |
| --- | --- |--- |
| `use` | 是 | 用法：标识公钥的预期用途。 加密数据 `enc`，或验证数据 `sig` 上的签名。|
| `nbf`| 否 | 激活日期和时间。 |
| `exp`| 否 | 到期日期和时间。 |

建议你根据自己的 PKI 标准设置密钥激活和到期值。 出于安全或策略原因，你可能需要定期轮换这些证书。 例如，某个策略可能每年轮换一次所有证书。

若要创建密钥，可以选择以下方法之一：

- **手动** - 使用你定义的字符串创建机密。 此机密是一种对称密钥。 你可以设置激活日期和到期日期。
- **生成的** - 自动生成密钥。 你可以设置激活日期和到期日期。 共有两个选项：
  - **机密** - 生成对称密钥。
  - **RSA** - 生成密钥对（非对称密钥）。
- **上传** - 上传证书或 PKCS12 密钥。 证书必须包含私钥和公钥（非对称密钥）。

## <a name="key-rollover"></a>密钥滚动更新

出于安全考虑，Azure AD B2C 可以定期滚动更新密钥，或在紧急情况下立即滚动更新密钥。 任何与 Azure AD B2C 集成的应用程序、标识提供者或 REST API 都应准备好处理密钥滚动更新事件，而不管滚动更新可能发生的频率是多少。 否则，如果应用程序或 Azure AD B2C 尝试使用过期密钥来执行加密操作，则登录请求会失败。

如果某个 Azure AD B2C 密钥集包含多个密钥，则每次只有其中一个密钥处于活动状态，具体取决于以下条件：

- 密钥激活基于**激活日期**。
  - 密钥按激活日期以升序排序。 激活日期越靠后，密钥在列表中的位置越靠后。 没有激活日期的密钥位于列表底部。
  - 如果当前日期和时间晚于密钥的激活日期，Azure AD B2C 将激活密钥并停止使用以前的有效密钥。
- 如果当前密钥的到期时间已过，并且密钥容器包含的新密钥具有有效的“不早于”和“到期”时间，则新密钥将自动变为激活状态。 
- 如果当前密钥的到期时间已过，并且密钥容器未包含具有有效的“不早于”和“到期”时间的新密钥，则 Azure AD B2C 将无法使用到期的密钥。   Azure AD B2C 将在你的自定义策略的相关组件中引发错误消息。 为了避免此问题，你可以创建不带激活和到期日期的默认密钥作为保障措施。
- 在 [JwtIssuer 技术配置文件](https://docs.microsoft.com/azure/active-directory-b2c/jwt-issuer-technical-profile)中引用密钥时，OpenId Connect 众所周知的配置终结点的密钥终结点 (JWKS URI) 会反映密钥容器中配置的密钥。 使用 OIDC 库的应用程序将自动提取此元数据，以确保它使用正确的密钥来验证令牌。 有关详细信息，请学习如何使用 [Microsoft 身份验证库](https://docs.microsoft.com/azure/active-directory/develop/msal-b2c-overview)，它会自动提取最新的令牌签名密钥。

## <a name="policy-key-management"></a>策略密钥管理

若要获取密钥容器中的当前有效密钥，请使用 Microsoft Graph API [getActiveKey](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey) 终结点。

若要添加或删除签名和加密密钥，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录  。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在概述页面上的“策略”下，选择“Identity Experience Framework” 。
1. 选择“策略密钥” 
    1. 若要添加新密钥，请选择“添加”。
    1. 若要删除新密钥，请选择该密钥，然后选择“删除”。 若要删除密钥，请键入要删除的密钥容器的名称。 Azure AD B2C 将删除密钥，并创建一个后缀为 .bak 的密钥副本。

## <a name="next-steps"></a>后续步骤

- 了解如何使用 Microsoft Graph 自动执行[密钥集](microsoft-graph-operations.md#trust-framework-policy-keyset)和[策略密钥](microsoft-graph-operations.md#trust-framework-policy-key)部署。







