---
title: "Azure Active Directory 应用注册 | Microsoft 文档"
description: "本文介绍如何使用 Azure 门户将应用程序注册到 Azure Active Directory"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: 
ms.assetid: 7dc7b89f-653f-405a-b5f4-2c1288720c15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: priyamo
ms.reviewer: elisol
ms.openlocfilehash: 4f32f57d4c8b3e13d2aa31b04ee10bb46a6c71f7
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="register-your-application-with-your-azure-active-directory-tenant"></a>将应用程序注册到 Azure Active Directory 租户

可使用 Azure 门户将应用程序注册到 Azure Active Directory (Azure AD) 租户。 这会为应用程序创建一个应用程序 ID，并使其能够接收令牌。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在页面右上角选择帐户，选择 Azure AD 租户。
3. 在左侧导航窗格中，选择“更多服务”，单击“应用注册”，并单击“添加”。
4. 根据提示创建新的应用程序。 如需 Web 应用程序或本机应用程序的特定示例，请查看[快速入门](active-directory-developers-guide.md)。
  * 对于 Web 应用程序，请在用户登录页面（如 `http://localhost:12345`）提供“登录 URL”，即应用的基 URL。
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * 对于本机应用程序，请提供“重定向 URI”，Azure AD 将用其返回令牌响应。 输入特定于应用程序的值，例如 `http://MyFirstAADApp`
5. 完成注册后，Azure AD 会为应用程序分配唯一客户端标识符，即应用程序 ID。

## <a name="update-application-settings-from-the-azure-portal"></a>在 Azure 门户中更新应用程序设置

可使用 Azure 门户轻松修改现有应用程序的设置。 例如，可配置答复 URL，它是 Azure AD 颁发令牌响应的位置。 还可为其他应用程序配置权限，例如，允许应用程序访问 Microsoft 图形 API。 通过应用程序设置页面，可执行所有这些操作。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在页面右上角选择帐户，选择 Azure AD 租户。
3. 在左侧导航窗格中，选择“更多服务”，单击“应用注册”，然后从列表中选择应用程序。
4. 单击“设置”，打开应用程序的设置页面。
  * 在“属性”页中，可以修改应用程序的常规信息。 这包括应用程序名称、登录 URL 和注销 URL。
  * 在“回复 URL”页中，可以添加回复 URL，即 Azure AD 发送令牌响应的位置。
  * 在“所有者”页中，可以添加应用程序所有者。
  * 在“所需权限”页中，可为应用配置权限。 例如，要访问 Microsoft 图形 API，请单击“添加”，在 API 选择器中选择“Microsoft Graph”，并选择所需的权限（如“读取目录数据”）。
  * 在“密钥”页中，可以添加应用程序机密。 密码会在创建后立即显示，并且只显示一次，因此请复制密码，以便今后使用。

## <a name="use-the-inline-manifest-editor"></a>使用内联清单编辑器

可使用内联清单编辑器修改某些应用程序属性，这些属性不会直接在 Azure 门户中公开。 例如，它可用于修改应用程序的 App ID URI，或启用 OAuth2.0 隐式流（而不是默认授权代码流）。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在页面右上角选择帐户，选择 Azure AD 租户。
3. 在左侧导航窗格中，选择“更多服务”，单击“应用注册”，然后从列表中选择应用程序。
4. 在应用程序页面中，单击“清单”打开内联清单编辑器。
5. 准备就绪后，可直接对清单进行更改和保存。 或者，可下载清单并在喜爱的编辑器中打开，然后上传更新的清单。

## <a name="next-steps"></a>后续步骤

1. 若要深入了解应用程序如何使用 Azure AD 执行身份验证，请查看[快速入门](active-directory-developers-guide.md)。
2. 在 [GitHub](https://github.com/azure-samples) 中查看代码示例的完整列表。
