---
title: 如何填写自定义开发应用程序的特定字段 | Microsoft Docs
description: 有关使用 Azure AD 注册自定义开发的应用程序时如何填写特定字段的指南
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: c6f6b0685c83d9305c62f121aec562d2afedae21
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "36335708"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>如何填写自定义开发应用程序的特定字段

本文简要介绍 [Azure 门户](https://portal.azure.com)中应用程序注册表单中的所有可用字段。

## <a name="register-a-new-application"></a>注册新应用程序

-   若要注册新应用程序，请导航到 [Azure 门户](https://portal.azure.com)。

-   在左侧导航窗格中，单击“Azure Active Directory”

-   选择“应用注册”，并单击“添加”。

-   应用程序注册表单将打开。

## <a name="fields-in-the-application-registration-form"></a>应用程序注册表单中的字段


| 字段            | 说明                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| 名称             | 应用程序的名称。 它应至少具有四个字符。                |
| 应用程序类型 | **Web 应用/Web API**：表示 Web 应用程序和/或 Web API 的应用程序 
| |**本机**：可安装在用户的设备或计算机上的应用程序           |
| 登录 URL      | 用户可在其中登录以使用应用程序的 URL                                  |

填写上述字段后，会在 Azure 门户中注册应用程序，并重定向到应用程序页面。 应用程序窗格上的“设置”按钮将打开“设置”页，该页面包含多个用于自定义应用程序的字段。 下表介绍了“设置”页中的所有字段。 请注意，将只能看到这些字段的一个子集，具体取决于创建的是 Web 应用程序还是本机应用程序。

| 字段           | 说明                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 应用程序 ID  | 注册应用程序时，Azure AD 会为应用程序分配一个应用程序 ID。 应用程序 ID 可用于在对 Azure AD 的身份验证请求中唯一标识应用程序，以及访问图形 API 等资源。                                                          |
| 应用 ID URI      | 这应是唯一的 URI，通常形式为：**https://&lt;tenant\_name&gt;/&lt;application\_name&gt;。** 这在权限授予流程中作为指定应对其颁发令牌的资源的唯一标识符使用。 也将成为已颁发访问令牌中的“aud”声明。 |
| 上传新徽标 | 可使用此字段上传应用程序的徽标。 徽标必须采用 .bmp、.jpg 或 .png 格式，并且文件大小应小于 100 KB。 图像大小应为 215x215 像素，中心图像大小应为 94x94 像素。                                                       |
| 主页 URL   | 这是在应用程序注册过程中指定的登录 URL。                                                                                                                                                                                                                                              |
| 注销 URL      | 这是单一注销的注销 URL。 用户使用任何其他注册的应用程序清除与 Azure AD 的会话时，Azure AD 将向此 URL 发送注销请求。                                                                                                                                       |
| 多租户  | 此开关指定多个租户是否可以使用应用程序。 通常情况下，这意味着外部组织可以通过在其租户中注册，并授予对其组织数据的访问权限来使用应用程序。                                                                   |
| 回复 URL      | 回复 URL 是 Azure AD 会在其中返回应用程序请求的任何令牌的终结点。                                                                                                                                                                                                          |
| 重定向 URI   | 对于本机应用程序，会在成功授权后向其中发送用户。 Azure AD 会检查应用程序在 OAuth 2.0 请求中提供的重定向 URI 是否与门户中的某个注册值相匹配。                                                            |
| 密钥            | 可以创建密钥以编程方式访问由 Azure AD 保护的 Web API，而无需任何用户交互。 在\*\*密钥\*\*页中，输入密钥说明和到期日期并保存以生成密钥。 请确保将其保存在安全位置，不然在以后将无法访问它。             |

## <a name="next-steps"></a>后续步骤
[使用 Azure Active Directory 管理应用程序](manage-apps/what-is-application-management.md)
