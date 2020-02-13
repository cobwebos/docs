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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159023"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>在 Azure Active Directory 中管理用于联合单一登录的证书

本文介绍与 Azure Active Directory （Azure AD）创建的证书相关的常见问题和信息，以建立对你的软件即服务（SaaS）应用程序的联合单一登录（SSO）。 从 Azure AD 应用库或使用非库应用程序模板添加应用程序。 使用联合 SSO 选项配置应用程序。

本文仅与配置为使用 Azure AD SSO 到[安全断言标记语言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)（SAML）联合的应用相关。

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>为库和非库应用程序自动生成的证书

从库中添加新的应用程序并配置基于 SAML 的登录（从 "应用程序概述" 页中选择 "**单一登录"**  > **saml** ）时，Azure AD 为有效时间为三年的应用程序生成一个证书。 若要将活动证书下载为安全证书（ **.cer**）文件，请返回到该页面（**基于 SAML 的登录**），然后在 " **SAML 签名证书**" 标题中选择下载链接。 可以在原始（二进制）证书或 Base64 （基本64编码的文本）证书之间进行选择。 对于库应用程序，本部分还可能显示一个链接，用于根据应用程序的要求，将证书下载为联合元数据 XML （ **.xml**文件）。

![SAML 活动签名证书下载选项](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

还可以通过选择 " **Saml 签名证书**" 标题的**编辑**图标（铅笔）来下载活动或非活动证书，该图标显示 " **saml 签名证书**" 页。 选择要下载的证书旁边的省略号（ **...** ），然后选择所需的证书格式。 你可以使用额外的选项，以隐私增强邮件（PEM）格式下载证书。 此格式等同于 Base64，但文件扩展名为**pem** ，在 Windows 中无法识别为证书格式。

![SAML 签名证书下载选项（活动和非活动）](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>自定义联合身份验证证书的过期日期以及滚动使用新证书

默认情况下，当证书在 SAML 单一登录配置期间自动创建时，Azure 会将证书配置为三年后过期。 由于在保存证书后无法更改其日期，因此必须：

1. 使用所需的日期创建新证书。
1. 保存新证书。
1. 下载采用正确格式的新证书。
1. 将新证书上传到应用程序。
1. 在 Azure Active Directory 门户中使新证书处于活动状态。

以下两个部分将帮助你执行这些步骤。

### <a name="create-a-new-certificate"></a>创建新证书

首先，创建并保存具有不同到期日期的新证书：

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com/)。 此时将显示 " **Azure Active Directory 管理中心**" 页。
1. 在左窗格中，选择“企业应用程序”。 此时将显示帐户中企业应用程序的列表。
1. 选择受影响的应用程序。 此时将显示应用程序的 "概述" 页。
1. 在应用程序概述页的左窗格中，选择 "**单一登录**"。
1. 如果出现 "**选择单一登录方法**" 页，请选择 " **SAML**"。
1. 在 "**设置 saml 的单一登录**" 页中，找到 " **saml 签名证书**" 标题，并选择 "**编辑**" 图标（铅笔）。 此时将显示 " **SAML 签名证书**" 页，其中显示了每个证书的状态（**活动**或**非活动**）、到期日期和指纹（哈希字符串）。
1. 选择 "**新建证书**"。 "证书列表" 下面会显示一个新行，其中的到期日期默认为当前日期之后的三年。 （你的更改尚未保存，因此你仍可以修改到期日期。）
1. 在 "新证书" 行中，将鼠标悬停在 "到期日期" 列上，然后选择 "**选择日期**" 图标（日历）。 此时将显示一个日历控件，其中显示新行当前到期日期的月份日期。
1. 使用 calendar 控件设置新日期。 您可以在当前日期和当前日期后三年之间设置任何日期。
1. 选择“保存”。 现在，新证书显示状态为 "**非活动**"，所选的到期日期为指纹。
1. 选择**X**以返回到 "**设置 SAML-预览的单一登录**" 页。

### <a name="upload-and-activate-a-certificate"></a>上传和激活证书

接下来，下载正确格式的新证书，将其上传到应用程序，并使其在 Azure Active Directory 中处于活动状态：

1. 查看应用程序的其他 SAML 登录配置说明，方法如下：

   - 选择要在单独的浏览器窗口或选项卡中查看的**配置指南**链接，或
   - 转到 "**设置**" 标题，然后选择 "查看在边栏中查看**分步说明**"。

1. 在说明中，请记下证书上传所需的编码格式。
1. 请按照前面为[库和非库应用程序自动生成的证书](#auto-generated-certificate-for-gallery-and-non-gallery-applications)部分中的说明进行操作。 此步骤以应用程序上传所需的编码格式下载证书。
1. 如果要滚动到新证书，请返回到 " **SAML 签名证书**" 页，并在新保存的 "证书" 行中，选择省略号（ **...** ）并选择 "**使证书处于活动状态**"。 新证书的状态将更改为 "**活动**"，而以前的活动证书将更改为 "**非活动**" 状态。
1. 继续遵循你之前显示的应用程序的 SAML 登录配置说明，以便你可以采用正确的编码格式上传 SAML 签名证书。

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>为证书过期添加电子邮件通知地址

在 SAML 证书过期之前，Azure AD 将发送电子邮件通知60、30和7天。 你可以添加多个电子邮件地址以接收通知。 若要指定要将通知发送到的电子邮件地址，请执行以下操作：

1. 在 " **SAML 签名证书**" 页中，请参阅 "**通知电子邮件地址**" 标题。 默认情况下，此标题仅使用添加了该应用程序的管理员的电子邮件地址。
1. 在最后的电子邮件地址下，键入应收到证书的过期通知的电子邮件地址，然后按 Enter。
1. 对要添加的每个电子邮件地址重复上述步骤。
1. 对于要删除的每个电子邮件地址，请选择电子邮件地址旁边的 "**删除**" 图标（垃圾邮件可以）。
1. 选择“保存”。

你将接收来自 aadnotification@microsoft.com 的通知电子邮件。 若要避免电子邮件进入你的垃圾邮件位置，请将此电子邮件添加到你的联系人。

## <a name="renew-a-certificate-that-will-soon-expire"></a>续订即将过期的证书

如果证书即将过期，则可以使用不会对用户造成明显停机的过程来续订证书。 续订过期证书：

1. 按照前面的使用与现有证书重叠的日期[创建新证书](#create-a-new-certificate)一节中的说明进行操作。 该日期限制了证书过期导致的停机时间。
1. 如果应用程序可以自动滚动更新证书，请按照以下步骤将新证书设置为 "活动"：
   1. 返回到 " **SAML 签名证书**" 页。
   1. 在新保存的证书行中，选择省略号（ **...** ），然后选择 "**使证书处于活动状态**"。
   1. 跳过接下来的两个步骤。

1. 如果应用一次只能处理一个证书，请选择要执行下一步的停机时间间隔。 （否则，如果应用程序不会自动选取新证书，但可以处理多个签名证书，则可以随时执行下一步。）
1. 在旧证书过期之前，请按照前面的[上传和激活证书](#upload-and-activate-a-certificate)部分中的说明进行操作。
1. 登录到应用程序，确保证书正常工作。

## <a name="related-articles"></a>相关文章

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](../saas-apps/tutorial-list.md)
- [Azure Active Directory 的应用程序管理](what-is-application-management.md)
- [单一登录到 Azure Active Directory 中的应用程序](what-is-single-sign-on.md)
- [在 Azure Active Directory 中调试对应用程序进行基于 SAML 的单一登录](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
