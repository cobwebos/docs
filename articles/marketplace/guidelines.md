---
title: Azure 市场和 AppSource 发布者准则 | Azure
description: 面向应用和服务发布者的 Azure 市场与 AppSource 准则
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: 135f934cd6b352dad9e4cea5a14406804f31b66b
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825798"
---
# <a name="guidelines"></a>指南  

<!--
## Guidelines for AppSource  
-->
---  

## <a name="guidelines-for-azure-marketplace"></a>Azure 市场准则  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>有关创建 Microsoft ID 来管理 Azure 市场帐户的准则  
如果多个用户需要访问创建市场帐户所用的 Microsoft ID，则你应该遵循本文中的准则来帮助创建公司帐户。 

>[!IMPORTANT]
>若要授权多个用户访问你的 Microsoft 开发人员中心（简称“开发人员中心”）帐户，Microsoft 建议使用 Azure Active Directory (Azure AD) 向各个用户分配角色。 每个用户必须通过使用其个人 Azure AD 凭据登录来访问该帐户。 使用已注册到公司的域中的电子邮件地址创建 Microsoft ID。Microsoft 建议不要将电子邮件分配给个人。 例如 `windowsapps@fabrikam.com`。  
>*   有关详细信息，请访问[问题：Azure AD 联合域中的 Microsoft ID](#issue:-microsoft-id-in-an-azure-ad-federated-domain) 部分。  

*   限制只有尽可能最少数量的开发人员可以访问 Microsoft ID。 
*   设置企业电子邮件通讯组列表 (DL)，并在其中包含必须访问开发人员中心帐户的每个人。 将 DL 电子邮件地址添加到安全信息。 DL 可让列表中的所有员工根据请求接收安全代码，以及管理 Microsoft ID 的安全信息。 如果设置通讯组列表不可行，则个人电子邮件帐户的所有者必须能够根据提示访问和共享安全代码。  
    *   例如，将新的安全信息添加到 Microsoft ID 或者从新设备访问 Microsoft ID 时，将提示所有者。  
*   添加不需要分机且主要团队成员可以访问的公司电话号码。  
*   一般情况下，应该要求开发人员使用受信任的设备登录到你的开发人员中心帐户。 所有重要团队成员应有权访问受信任的设备。 使用受信任的设备进行访问可以减少某人访问开发人员中心帐户时发送安全代码的需要。  
*   如果需要从不受信任的计算机授予开发人员中心帐户的访问权限，应限制最多只有五个开发人员可以进行该访问。 理想情况下，开发人员应从共用同一地理位置和网络位置的计算机访问该帐户。  
*   经常检查并验证安全信息。  
    *   若要查看安全信息，请访问“安全设置”页：[account.live.com/proofs/Manage](https://account.live.com/proofs/Manage)。

应该主要从受信任的计算机访问开发人员中心帐户。 必须从受信任的计算机进行访问，因为每周为每个开发人员中心帐户生成的代码数有限制。 使用受信任的计算机还能提供最安全且一致的登录体验。 
*   有关其他开发人员中心帐户准则和安全性的详细信息，请访问“创建开发人员帐户”页：[docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account)。 

---  

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>问题：Azure AD 联合域中的 Microsoft ID  
可以通过 Azure Active Directory (Azure AD) 来联合企业帐户。 如果尝试使用与 Azure AD 联合的企业电子邮件地址创建 Microsoft ID，则收到错误。 如果收到错误，应咨询 IT 团队，确认帐户是否是通过 Azure AD 联合的。 Azure AD 联合电子邮件存在已知的问题，Microsoft 正在努力解决此问题。  
*   有关 Azure AD 的详细信息，请访问 Azure Active Directory 文档页：[docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory)。

Microsoft 推荐了一种解决方法。 请遵循这些步骤在 `outlook.com` 域中创建一个新的电子邮件地址，并创建一个规则用于转发通讯。  
1.  转到“创建帐户”页，单击“获取新的电子邮件地址”链接。 
    *   若要注册 Microsoft ID，请访问“创建帐户”页：[signup.live.com/signup](https://signup.live.com/signup)。  
2.  创建新电子邮件地址，并输入密码。 将会创建一个新的 Microsoft ID，并在 `outlook.com` 域中创建一个电子邮件邮箱。 继续执行注册流程，直到帐户创建完毕。  

    >[!IMPORTANT]
    >必须使用注册为 Microsoft ID 的电子邮件地址或通讯组列表在开发人员中心注册。 Microsoft 建议使用通讯组列表删除个人的依赖关系。 如果电子邮件地址或通讯组列表未注册，则必须立即注册。    

    >[!Important]
    >如果有任何电子邮件地址位于 `Microsoft` 公司域中，则你无法使用该地址在开发人员中心注册。  

3.  使用 Outlook 电子邮件地址创建 Microsoft ID 之后，登录到 Outlook 邮箱。 创建一个电子邮件转发规则。 该电子邮件转发规则应将 Outlook 邮箱中收到的所有电子邮件，移到域中创建的、用于管理 Azure 市场帐户的电子邮件地址。  
    *   若要登录到 Outlook 邮箱，请访问 Outlook 页：[outlook.live.com/owa](https://outlook.live.com/owa)。  
    *   有关转发规则的详细信息，请访问“使用 Outlook Web 应用中的规则将邮件自动转发到另一帐户”页：[support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed)。  

1.  转发规则将 Outlook 电子邮件帐户中收到的所有电子邮件和通讯发送至已注册到公司的域中的电子邮件地址。 必须使用 `outlook.com` 电子邮件地址在开发人员中心和云合作伙伴门户中进行身份验证。  

## <a name="next-steps"></a>后续步骤
*   访问 [Azure 市场和 AppSource 发布者指南](./marketplace-publishers-guide.md)页。  
 
---  
