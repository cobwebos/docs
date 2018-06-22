---
title: 在 Azure AD 中管理联合身份验证证书 | Microsoft Docs
description: 了解如何自定义联合证书的过期日期，以及如何续订即将过期的证书。
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2017
ms.author: jeedes
ms.openlocfilehash: c4d812db6371a4cd1fcc701f7eae2c913c29fb6b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35302034"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>在 Azure Active Directory 中管理用于联合单一登录的证书
本文包含 Azure Active Directory (Azure AD) 为了与 SaaS 应用程序建立联合单一登录 (SSO) 而创建的证书的相关常见问题和信息。 从 Azure AD 应用库或使用非库应用程序模板添加应用程序。 使用联合 SSO 选项配置应用程序。

本文只与配置为通过 SAML 联合使用 Azure AD SSO 的应用相关，如以下示例中所示：

![Azure AD 单一登录](./media/manage-certificates-for-federated-single-sign-on/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>为库和非库应用程序自动生成的证书
从库中添加新的应用程序并配置基于 SAML 的登录时，Azure AD 将为应用程序生成具有 3 年有效期的证书。 可从“SAML 签名证书”部分下载此证书。 对于库应用程序，本部分可能会显示下载证书或元数据的选项，具体取决于应用程序的要求。

![Azure AD 单一登录](./media/manage-certificates-for-federated-single-sign-on/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>自定义联合身份验证证书的过期日期以及滚动使用新证书
默认情况下，证书设置为 3 年后过期。 可通过完成以下步骤为证书选择不同的过期日期。
屏幕截图使用 Salesforce 作为示例，但这些步骤同样适用于任何联合 SaaS 应用。

1. 在 [Azure 门户](https://aad.portal.azure.com)中，单击左侧窗格中的“企业应用程序”，然后单击“概述”页中的“新建应用程序”：

   ![打开 SSO 配置向导](./media/manage-certificates-for-federated-single-sign-on/enterprise_application_new_application.png)

2. 搜索库应用程序并选择要添加的应用程序。 如果找不到所需的应用程序，请使用“非库应用程序”选项添加应用程序。 此功能仅适用于 Azure AD Premium（P1 和 P2）SKU。

    ![Azure AD 单一登录](./media/manage-certificates-for-federated-single-sign-on/add_gallery_application.png)

3. 单击左侧窗格中的“单一登录”链接，并将“单一登录模式”更改为“基于 SAML 的登录”。 此步骤将为应用程序生成具有 3 年有效期的证书。

4. 若要创建新证书，请单击“SAML 签名证书”部分的“新建证书”链接。

    ![生成新证书](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

5. “新建证书”链接将打开日历控件。 可设置任意日期和时间（自当前日期起三年内）。 所选日期和时间即为新证书的到期日期和时间。 单击“ **保存**”。

    ![下载然后上传证书](./media/manage-certificates-for-federated-single-sign-on/certifcate_date_selection.PNG)

6. 现在有新的证书可供下载。 单击“证书”链接进行下载。 此时，证书处于不活动状态。 如果希望滚动至此证书，请选中“激活新证书”复选框并单击“保存”。 此后，Azure AD 将开始使用新证书对响应进行签名。

7.  若要了解如何将证书上传到特定 SaaS 应用程序，请单击“查看应用程序配置教程”链接。

## <a name="certificate-expiration-notification-email"></a>证书到期通知电子邮件

Azure AD 将在 SAML 证书到期前的 60 天、30 天和 7 天发送电子邮件通知。 若要指定发送通知的电子邮件地址：

- 在 Azure Active Directory 应用程序的“单一登录”页面上，转到“通知电子邮件”字段。
- 输入应接收证书到期通知电子邮件的电子邮件地址。 默认情况下，此字段使用添加应用程序的管理员的电子邮件地址。

你将接收来自 aadnotification@microsoft.com 的通知电子邮件。 若要避免电子邮件进入你的垃圾邮件位置，请确保将此电子邮件添加到你的联系人。 

## <a name="renew-a-certificate-that-will-soon-expire"></a>续订即将过期的证书
以下续订步骤应该不会给用户造成任何严重的停机。 本部分的屏幕截图采用 Salesforce 作为示例，但这些步骤适用于所有联合 SaaS 应用。

1. 在 Azure Active Directory 应用程序的“单一登录”页面上，为应用程序生成新证书。 可单击“SAML 签名证书”部分的“新建证书”链接来完成此操作。

    ![生成新证书](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

2. 为新证书选择所需的到期日期和时间，然后单击“保存”。

3. 通过“SAML 签名证书”选项下载证书。 将新证书上传到 SaaS 应用程序的单一登录配置屏幕。 若要了解如何为特定的 SaaS 应用程序执行此操作，请单击“查看应用程序配置教程”链接。
   
4. 若要在 Azure AD 中激活新证书，请选中“激活新证书”复选框，然后单击页面顶部的“保存”按钮。 这将在 Azure AD 端滚动使用新证书。 证书的状态会从“新”更改为“活动”。 此后，Azure AD 将开始使用新证书对响应进行签名。 
   
    ![生成新证书](./media/manage-certificates-for-federated-single-sign-on/new_certificate_download.png)

## <a name="related-articles"></a>相关文章
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](../active-directory-saas-tutorial-list.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](../active-directory-apps-index.md)
* [Azure Active Directory 的应用程序访问与单一登录](what-is-single-sign-on.md)
* [排查基于 SAML 的单一登录的问题](../develop/active-directory-saml-debugging.md)
