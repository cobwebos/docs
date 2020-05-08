---
title: 保存管理员凭据时出现问题配置 Azure AD 库应用
description: 当为在 Azure AD 应用程序库中列出的某个应用程序配置用户预配时，如何解决面对的常见问题
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: dc88eed4fe0fb727e44e1a87b9a681a46dcc58ac
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593975"
---
# <a name="problem-saving-administrator-credentials-while-configuring-user-provisioning-to-an-azure-active-directory-gallery-application"></a>配置 Azure Active Directory 库应用程序的用户预配时保存管理员凭据时遇到问题 

使用 Azure 门户为企业应用程序配置[自动化用户预配](user-provisioning.md)时，可能会遇到以下情况：

* 为应用程序输入的**管理员凭据**有效，并且“测试连接”**** 按钮起作用。 但是，无法保存凭据，并且 Azure 门户返回一条一般错误消息。

如果还为同一应用程序配置了基于 SAML 的单一登录，则发生此错误的最可能原因是已超出了 Azure AD 内部针对证书和凭据设定的每应用程序存储限制。

Azure AD 当前对与应用程序的单个实例关联的所有证书、机密令牌、凭据以及相关配置数据（在 Azure AD 中也称为服务主体记录）具有最大存储容量为 1024 字节的限制。

当配置了基于 SAML 的单一登录时，用来对 SAML 令牌进行签名的证书存储在这里，并且通常会占用该空间的 50% 以上。

在设置用户预配时输入的任何机密令牌、URI、通知电子邮件地址、用户名和密码可能会导致超出存储限制。

## <a name="how-to-work-around-this-issue"></a>如何解决此问题 

目前有两种用来解决此问题的可行方法：

1. **使用两个库应用程序实例，一个用于单一登录，一个用于用户预配** - 以库应用程序 [LinkedIn Elevate](../saas-apps/linkedinelevate-tutorial.md) 为例，可以从库中添加 LinkedIn Elevate 并为其配置单一登录。 对于预配，从 Azure AD 应用库中添加 LinkedIn Elevate 的另一个实例，并将其命名为“LinkedIn Elevate (Provisioning)”。 对于这第二个实例，配置[预配](../saas-apps/linkedinelevate-provisioning-tutorial.md)，但不配置单一登录。 使用此解决方法时，需要向两个应用程序[分配](../manage-apps/assign-user-or-group-access-portal.md)相同的用户和组。 

2. **减少所存储的配置数据量** - 在“预配”选项卡的[管理员凭据](user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application)部分中输入的所有数据都与 SAML 证书存储在同一位置。 虽然不可能减小所有这些数据的长度，但是可以删除诸如“通知电子邮件”之类的可选配置字段。****

## <a name="next-steps"></a>后续步骤
[为 SaaS 应用程序配置用户预配和取消预配](user-provisioning.md)
