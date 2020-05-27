---
title: 用于外部标识的自助注册 - Azure AD
description: 了解如何通过启用自助注册来允许外部用户自行注册应用。 通过自定义自助注册用户流来创建个性化注册体验。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e0325b43b6726f04d5994b60404f218ac58122d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594826"
---
# <a name="self-service-sign-up-preview"></a>自助注册（预览）
|     |
| --- |
| 自助注册是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

与外部用户共享应用时，你可能并不总是提前知道谁需要访问应用。 除了直接向个人发送邀请之外，还可以通过启用自助注册来允许外部用户自行注册特定的应用。 可以通过自定义自助注册用户流来创建个性化注册体验。 例如，可以提供 Azure AD 选项或社交标识提供者选项，并收集用户信息。

## <a name="user-flow-for-self-service-sign-up"></a>自助注册用户流

自助注册用户流通过要共享的应用为外部用户创建注册体验。 用户流可以与一个或多个应用相关联。 首先，为租户启用自助注册，并与要允许外部用户用来登录的任何标识提供者联合。 然后，创建和自定义注册用户流，并将应用分配到此流。
可以配置用户流设置来控制用户注册应用的方式：

- 用于登录的帐户类型，如 Facebook 等社交帐户或 Azure AD 帐户
- 要从注册用户收集的属性，如名字、邮政编码或居住地所在国家/地区

当用户要登录应用时，无论是 Web、移动、桌面还是单页应用 (SPA)，应用都会向用户流提供的终结点发起授权请求。 用户流定义并控制用户体验。 在用户完成注册用户流后，Azure AD 会生成一个令牌，然后将用户重定向回应用。 多个应用可以使用相同的用户流。

## <a name="example-of-self-service-sign-up"></a>自助注册示例

下面的示例展示了如何将社交标识提供者引入为来宾用户提供自助注册功能的 Azure AD。  
Woodgrove 的一个合作伙伴打开了 Woodgrove 应用。 合作伙伴决定要注册一个供应商帐户，因此选择了“申请供应商帐户”，这会启动自助注册流。

![自助注册起始页示例](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

合作伙伴使用所选的电子邮件地址注册。

![展示了选择 Facebook 进行登录的示例](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Azure AD 使用合作伙伴的 Facebook 帐户与 Woodgrove 建立关系，并新建一个帐户。

Woodgrove 希望了解更多关于用户的信息，如姓名、企业名称、企业注册码、电话号码。

![展示了用户注册属性的示例](media/self-service-sign-up-overview/example-enter-user-attributes.png)

用户输入信息，继续注册流，并获取对所需资源的访问权限。

![展示了用户已登录的示例](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>后续步骤

 有关详细信息，请参阅[如何向应用添加自助注册](self-service-sign-up-user-flow.md)。