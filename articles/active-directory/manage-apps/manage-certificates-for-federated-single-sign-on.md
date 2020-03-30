---
title: 在 Azure AD 中管理联合身份验证证书 | Microsoft Docs
description: 了解如何自定义联合证书的过期日期，以及如何续订即将过期的证书。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de60dc5095ce4ab4d0219a388c445b08f544e1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159023"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>在 Azure Active Directory 中管理用于联合单一登录的证书

在本文中，我们将介绍与 Azure 活动目录 （Azure AD） 为将软件作为服务 （SaaS） 应用程序建立联合单一登录 （SSO） 而创建的证书相关的常见问题和信息。 从 Azure AD 应用库或使用非库应用程序模板添加应用程序。 使用联合 SSO 选项配置应用程序。

本文仅与配置为通过[安全断言标记语言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)（SAML） 联合使用 Azure AD SSO 的应用相关。

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>为库和非库应用程序自动生成的证书

从库中添加新应用程序并配置基于 SAML 的登录时（通过从应用程序概述页中选择**单一登录** > **SAML），Azure** AD 会为该应用程序生成有效期为三年的应用程序的证书。 要将活动证书下载为安全证书 **（.cer**） 文件，请返回到该页面 （**基于 SAML 的登录**），并在**SAML 签名证书**标题中选择下载链接。 您可以选择原始（二进制）证书或 Base64（基本 64 编码文本）证书。 对于库应用程序，本节可能还会显示一个链接，用于下载证书作为联合元数据**XML（.xml**文件），具体取决于应用程序的要求。

![SAML 活动签名证书下载选项](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

您还可以通过选择**SAML 签名证书**标题的 **"编辑"** 图标（铅笔）来下载活动或非活动证书，该图标显示**SAML 签名证书**页面。 选择要下载的证书旁边的省略号 （**...），** 然后选择所需的证书格式。 您可以选择以隐私增强邮件 （PEM） 格式下载证书。 此格式与 Base64 相同，但具有 **.pem**文件名扩展名，这在 Windows 中无法识别为证书格式。

![SAML 签名证书下载选项（活动和非活动）](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>自定义联合身份验证证书的过期日期以及滚动使用新证书

默认情况下，Azure 将证书配置为在 SAML 单一登录配置期间自动创建证书三年后过期。 由于在保存证书后无法更改证书的日期，因此必须：

1. 创建具有所需日期的新证书。
1. 保存新证书。
1. 以正确的格式下载新证书。
1. 将新证书上载到应用程序。
1. 使新证书在 Azure 活动目录门户中处于活动状态。

以下两个部分可帮助您执行这些步骤。

### <a name="create-a-new-certificate"></a>创建新证书

首先，创建并保存具有不同到期日期的新证书：

1. 登录到 Azure[活动目录门户](https://aad.portal.azure.com/)。 此时会显示“Azure Active Directory 管理中心”页。****
1. 在左窗格中，选择“企业应用程序”****。 将显示帐户中的企业应用程序列表。
1. 选择受影响的应用程序。 将显示应用程序的概述页。
1. 在应用程序概述页的左侧窗格中，选择 **"单一登录**"。
1. 如果出现 **"选择单个登录方法**页"，请选择**SAML**。
1. 在 **"使用 SAML 设置单个登录 - 预览**"页中，找到**SAML 签名证书**标题并选择 **"编辑"** 图标（铅笔）。 将显示**SAML 签名证书**页，该页显示每个证书的状态（**活动**或**非活动**）、到期日期和指纹（哈希字符串）。
1. 选择**新证书**。 证书列表下方有一个新行，到期日期默认为当前日期之后的三年。 （您的更改尚未保存，因此您仍然可以修改到期日期。
1. 在新的证书行中，将鼠标悬停在到期日期列上，然后选择 **"选择日期"** 图标（日历）。 将显示日历控件，显示新行当前到期日期的一个月的天数。
1. 使用日历控件设置新日期。 您可以设置当前日期与当前日期后三年之间的任何日期。
1. 选择“保存”。**** 新证书现在显示的状态为 **"非活动"、** 您选择的到期日期和指纹。
1. 选择**X**以返回到 **"使用 SAML 设置单一登录 - 预览**"页。

### <a name="upload-and-activate-a-certificate"></a>上传并激活证书

接下来，以正确的格式下载新证书，将其上载到应用程序，并使它在 Azure 活动目录中处于活动状态：

1. 查看应用程序的其他 SAML 登录配置说明，请执行以下任一操作：

   - 选择要在单独的浏览器窗口或选项卡中查看的**配置指南**链接，或
   - 前往**设置**标题并选择 **"查看分步说明**"，在边栏中查看。

1. 在说明中，请注意证书上载所需的编码格式。
1. 请按照前面有关[库和非库应用程序自动生成的证书](#auto-generated-certificate-for-gallery-and-non-gallery-applications)部分的说明进行操作。 此步骤以应用程序上载所需的编码格式下载证书。
1. 如果要滚动到新证书，请返回**SAML 签名证书**页面，在新保存的证书行中，选择省略号 **（...），** 然后选择 **"使证书处于活动状态**"。 新证书的状态将更改为**活动**，以前处于活动状态的证书将更改为**非活动**状态。
1. 继续遵循您之前显示的应用程序 SAML 登录配置说明，以便您可以以正确的编码格式上载 SAML 签名证书。

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>为证书过期添加电子邮件通知地址

Azure AD 将在 SAML 证书过期前 60、30 和 7 天发送电子邮件通知。 您可以添加多个电子邮件地址来接收通知。 要指定电子邮件地址，您希望将通知发送到：

1. 在**SAML 签名证书**页中，转到**通知电子邮件地址**标题。 默认情况下，此标题仅使用添加应用程序的管理员的电子邮件地址。
1. 在最终电子邮件地址下方，键入应接收证书过期通知的电子邮件地址，然后按 Enter。
1. 对要添加的每个电子邮件地址重复上一步。
1. 对于要删除的每个电子邮件地址，请选择电子邮件地址旁边的 **"删除**"图标（垃圾桶）。
1. 选择“保存”。****

你将接收来自 aadnotification@microsoft.com 的通知电子邮件。 为避免电子邮件发送到您的垃圾邮件位置，请将此电子邮件添加到您的联系人。

## <a name="renew-a-certificate-that-will-soon-expire"></a>续订即将过期的证书

如果证书即将过期，则可以使用不会导致用户严重停机的过程续订证书。 要续订过期证书：

1. 使用与现有证书重叠的日期，请先按照"[创建新证书](#create-a-new-certificate)"部分中的说明进行操作。 该日期限制证书过期造成的停机时间量。
1. 如果应用程序可以自动滚动证书，请按照以下步骤将新证书设置为活动：
   1. 返回**SAML 签名证书**页面。
   1. 在新保存的证书行中，选择省略号 **（...），** 然后选择 **"使证书处于活动状态**"。
   1. 跳过接下来的两个步骤。

1. 如果应用一次只能处理一个证书，则选择停机时间间隔以执行下一步。 （否则，如果应用程序未自动获取新证书，但可以处理多个签名证书，则可以随时执行下一步。
1. 在旧证书过期之前，请按照["上传"中的说明操作，并提前激活证书](#upload-and-activate-a-certificate)部分。
1. 登录到应用程序以确保证书正常工作。

## <a name="related-articles"></a>相关文章

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](../saas-apps/tutorial-list.md)
- [使用 Azure Active Directory 进行应用程序管理](what-is-application-management.md)
- [单一登录到 Azure Active Directory 中的应用程序](what-is-single-sign-on.md)
- [调试 Azure 活动目录中的应用程序基于 SAML 的单一登录](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
