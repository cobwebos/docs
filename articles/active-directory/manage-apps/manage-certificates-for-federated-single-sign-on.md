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
ms.openlocfilehash: 416e72f7e9e8622f044f7bb6430fbb36010b164e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701976"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>在 Azure Active Directory 中管理用于联合单一登录的证书

在本文中，我们将介绍常见问题和与 Azure Active Directory (Azure AD) 创建来建立联合单一登录 (SSO) 到您的软件即服务 (SaaS) 应用程序的证书相关的信息。 从 Azure AD 应用库或使用非库应用程序模板添加应用程序。 使用联合 SSO 选项配置应用程序。

本文是仅与应用程序配置为使用通过 Azure AD SSO[安全断言标记语言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)(SAML) 联合身份验证。

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>为库和非库应用程序自动生成的证书

从库中添加新的应用程序和配置基于 SAML 的单一登录时 (通过选择**单一登录** > **SAML**从应用程序概述页上)，Azure AD 将生成证书有效期为三年的应用程序。 若要下载安全证书形式的活动证书 ( **.cer**) 文件中，返回到该页 (**基于 SAML 的单一登录**)，然后选择中的下载链接**SAML 签名证书**标题。 您可以选择原始 （二进制） 证书或 Base64 （基 64 编码的文本） 证书。 对于库应用程序，本部分还可能会显示将其作为联合身份验证元数据 XML 下载的证书的链接 ( **.xml**文件)，取决于应用程序的要求。

![SAML 活动签名证书下载选项](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

此外可以通过选择下载的活动或非活动证书**SAML 签名证书**标题的**编辑**显示的图标 （铅笔） **SAML 签名证书**页。 选择省略号 ( **...** ) 想要下载，然后选择哪种证书格式的证书旁边。 必须下载隐私增强邮件 (PEM) 格式的证书的其他选项。 此格式都为 Base64 相同但具有 **.pem**文件扩展名，在作为证书格式的 Windows 中无法识别。

![SAML 签名证书下载选项 （活动和非活动）](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>自定义联合身份验证证书的过期日期以及滚动使用新证书

默认情况下，Azure 将配置为在创建时自动在 SAML 单一登录配置过程的三年后过期的证书。 因为不能更改证书的日期，在保存后，您必须：

1. 使用所需的日期创建新证书。
1. 保存新的证书。
1. 下载新证书的正确格式。
1. 将新证书上传到应用程序。
1. Azure Active Directory 门户中激活新证书。

以下两个部分帮助你执行这些步骤。

### <a name="create-a-new-certificate"></a>创建新的证书

首先，创建并使用不同的过期日期保存新的证书：

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com/)。 此时会显示“Azure Active Directory 管理中心”页。 
1. 在左窗格中，选择“企业应用程序”  。 将显示你的帐户中的企业应用程序的列表。
1. 选择受影响的应用程序。 应用程序的概览页会显示。
1. 在应用程序概述页的左窗格中，选择**单一登录**。
1. 如果**选择单一登录方法**页面出现时，选择**SAML**。
1. 在中**设置了单一登录使用 SAML-预览**页上，找到**SAML 签名证书**标题，然后选择**编辑**图标 （铅笔）。 **SAML 签名证书**页将出现，其中显示的状态 (**Active**或**非活动**)，到期日期和每个证书的指纹 （哈希字符串）。
1. 选择**新的证书**。 一个新行如下所示的证书列表中，到期日期位置默认为当前日期后完全三年。 （所做的更改尚未保存，因此您仍可以修改的到期日期。）
1. 在新的证书行中，悬停在到期日期列并选择**选择日期**图标 （日历）。 将出现一个日历控件，显示新行的当前到期日期的月份中的天。
1. 使用日历控件设置新的日期。 您可以设置的当前日期和当前日期后的三年之间的任何日期。
1. 选择**保存**。 现在将显示新证书的状态**非活动**，到期日期选择和指纹。
1. 选择**X**回到**设置了单一登录使用 SAML-预览**页。

### <a name="upload-and-activate-a-certificate"></a>上传和激活证书

接下来，下载正确的格式中的新证书，将其上载到该应用程序，并将其 Azure Active Directory 中处于活动状态：

1. 通过以下任一方法查看应用程序的其他 SAML 单一登录配置说明：

   - 选择**配置指南**链接以查看在单独的浏览器窗口或选项卡上，或
   - 转到**设置**标题，然后选择**查看分步说明**查看侧栏中。

1. 在说明中，请注意所需的证书上传的编码格式。
1. 按照中的说明[自动生成的库和非库应用程序的证书](#auto-generated-certificate-for-gallery-and-non-gallery-applications)前面部分。 此步骤将下载应用程序上传所需的编码格式中的证书。
1. 当您想要为新证书滚动更新时，请返回到**SAML 签名证书**页，然后在新保存的证书行中，选择省略号 ( **...** )，然后选择**使证书处于活动状态**。 新证书的状态将变为**Active**，并将以前的活动证书更改为的状态**非活动**。
1. 继续的以下应用程序的 SAML 单一登录配置说明，以便你可以上传 SAML 签名更早版本，显示的证书中的正确的编码格式。

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>添加证书的截止日期的电子邮件通知地址

Azure AD 会发送 SAML 证书过期之前使用电子邮件通知 60、 30 和 7 天。 您可以添加多个电子邮件地址以接收通知。 若要指定您希望通知发送到的电子邮件地址：

1. 在中**SAML 签名证书**页上，转到**通知电子邮件地址**标题。 默认情况下，此标题使用添加应用程序的管理员的电子邮件地址。
1. 下面的最终电子邮件地址，键入应收到的证书过期通知，然后按 Enter 的电子邮件地址。
1. 为你想要添加每个电子邮件地址重复上一步。
1. 对于每个你想要删除的电子邮件地址，请选择**删除**电子邮件地址旁边的图标 （垃圾回收会）。
1. 选择**保存**。

你将接收来自 aadnotification@microsoft.com 的通知电子邮件。 若要避免电子邮件进入您垃圾邮件的位置，请向联系人添加此电子邮件。

## <a name="renew-a-certificate-that-will-soon-expire"></a>续订即将过期的证书

如果证书即将过期，则可以更新该使用会导致任何严重的停机，为你的用户的过程。 若要续订即将过期的证书：

1. 按照中的说明[创建新证书](#create-a-new-certificate)更早版本，部分重叠的日期使用的现有证书。 该日期限制由证书过期导致的停机时间量。
1. 如果证书对其应用程序可以自动滚动更新，可以通过执行以下步骤将新的证书设置为活动：
   1. 返回到**SAML 签名证书**页。
   1. 在新保存的证书行中，选择省略号 ( **...** )，然后选择**使证书处于活动状态**。
   1. 跳过接下来两个步骤。

1. 如果应用程序一次只能处理一个证书，选择停机时间间隔，以执行下一步。 （否则，如果应用程序不会自动选取新的证书，但可以处理多个签名证书，你可以执行下一步随时。）
1. 在旧证书过期之前，按照中的说明[上传和激活证书](#upload-and-activate-a-certificate)前面部分。
1. 登录到应用程序以确保该证书正常运行。

## <a name="related-articles"></a>相关文章

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](../saas-apps/tutorial-list.md)
- [使用 Azure Active Directory 的应用程序管理](what-is-application-management.md)
- [单一登录到 Azure Active Directory 中的应用程序](what-is-single-sign-on.md)
- [调试基于 SAML 的单一登录到 Azure Active Directory 中的应用程序](../develop/howto-v1-debug-saml-sso-issues.md)
