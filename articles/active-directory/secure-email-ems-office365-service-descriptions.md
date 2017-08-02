---
title: "EMS 和 Office 365 服务概述 | Microsoft Docs"
description: "本文概述了 EMS 和 Office 365 服务。"
author: jeffgilb
manager: femila
ms.service: guidance
ms.topic: article
ms.date: 07/12/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
pnp.series.title: Best Practices
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 288ac3f9ec6354d4108461f32618eb95d3b3ab14
ms.contentlocale: zh-cn
ms.lasthandoff: 07/15/2017

---

# <a name="ems-and-office-365-services-overview"></a>EMS 和 Office 365 服务概述

本文概述了在提供的建议中使用的 EMS 和 Office 365 服务，并讨论了理解 Azure 电子邮件保护功能所需的核心概念。 这些功能使 Microsoft 云企业管理员能够保护公司员工的标识和设备，同时还能够控制对公司数据本身（包括传输中的和静止的）的访问。

## <a name="services"></a>服务 

下表介绍了本文档中提到的服务。
 
|服务|说明|
|-------|-----------|
|[Microsoft Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)|Azure AD 提供了整套标识管理功能，例如多重身份验证、设备注册、自助密码管理、自助组管理、基于角色的访问控制、应用程序使用情况监视、多样化审核以及安全监视和警报。|
|[Azure AD Identity Protection](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection)|此服务可用于检测可能会影响组织中的标识的潜在漏洞，还可用于配置通过条件性访问策略对低、中和高登录风险以及用户风险的自动响应。|
|[Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|此服务使组织能够尽量减少对特权操作具有永久访问权限的人数；Azure AD Privileged Identity Management 引入了有资格的管理员这一概念。 有资格的管理员应是不时（但不是每天）需要特权访问的用户。 该角色处于非活动状态，直到用户需要访问权限，然后他们完成激活过程，并在预定的时间内成为活动管理员。|
|[Azure 信息保护](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)| Azure 信息保护是基于云的解决方案，是作为 EMS E5 产品/服务的一部分提供的，可以帮助组织对其文档和电子邮件进行分类、加注标签和提供保护。 这可以由定义了规则和条件的管理员自动执行、由用户手动执行，或者以组合方式执行，在组合方式中，将向用户提供建议。 可以使用 Azure 信息保护标签对文档和电子邮件进行分类。 执行此操作时，分类始终是可标识的，不管数据存储在什么位置，也不管数据是与谁共享的。 <br><br>Azure 信息保护策略设置由 [Azure 权限管理](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)提供保护。 与标签的应用方式类似，使用权限管理应用的保护跟文档和电子邮件保留在一起，不受位置影响，无论是在组织、网络、文件服务器和应用程序内部还是外部，都是一样。|
|[Microsoft Intune](https://docs.microsoft.com/intune/understand-explore/introduction-to-microsoft-intune)|Intune 是基于云的企业移动管理 (EMM) 服务，可以帮助员工提高工作效率，同时始终使公司数据处于受保护状态。 Intune 与用于标识和访问控制的 Azure AD 紧密集成，并且用于设备和应用程序管理。 [Intune 的设备管理](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies)功能用来配置和保护用户的设备，包括 Windows PC。 <br><br>Intune 设备管理功能同时支持[自带设备办公 (BYOD)](https://docs.microsoft.com/enterprise-mobility-security/solutions/enable-byod) 注册（这允许用户注册其个人手机、平板电脑或 PC）和[企业拥有的设备 (COD)](https://docs.microsoft.com/enterprise-mobility-security/solutions/issue-corp-devices) 注册（这可以实现诸如自动注册、共享设备或预授权注册要求配置等管理方案）。 为提高安全性，甚至可以要求采用 MFA 来注册设备。 向管理功能进行注册后，Intune 可以配置设备功能和设置来实现对公司资源的安全访问。|


## <a name="ems-concepts"></a>EMS 概念
下表中介绍了你应当去熟悉的核心概念和 EMS 功能。

|核心概念|说明|
|------------|-----------|
|[Azure 多重身份验证 (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)|Azure MFA 是 Microsoft 的双重验证解决方案，可帮助保护对数据和应用程序的访问，同时可以满足用户对简单登录过程的需求。 它通过各种验证方法（包括电话、短信或移动应用验证）提供强大的身份验证机制。|
|[Azure AD 条件访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access)|这是 Azure AD 的一项功能，可让你根据特定的条件针对环境中的云应用实施访问控制。 通过这种控制，可以将其他要求关联到访问，或者阻止访问。 条件性访问的实现基于策略。|
|[Exchange Online 数据丢失防护 (DLP)](https://support.office.com/article/Overview-of-data-loss-prevention-policies-1966b2a7-d1e2-4d92-ab61-42efbb137f5e)|Exchange Online 数据丢失防护 (DLP) 策略是作为 Exchange Online Plan 2 和 Office 365 订阅的一项高级功能提供的，它使组织能够识别、监视和自动保护 Office 365 中的敏感信息。<br><br>使用 Exchange Online DLP 策略，可以识别许多位置（例如 Exchange Online、SharePoint Online 和 OneDrive for Business）中的敏感信息。 例如，这些策略可以帮助你识别包含敏感信息的文档或者防止意外与组织外部的人员共享敏感信息。|
|[Exchange 邮件流/传输规则](https://support.office.com/en-US/article/Define-mail-flow-rules-to-encrypt-or-decrypt-email-messages-9B7DAF19-D5F2-415B-BC43-A0F5F4A585E8)|Exchange 邮件流规则（也称为传输规则）在经过你的组织的消息中查找特定条件并对其采取操作。 邮件流规则类似于许多电子邮件客户端中可用的收件箱规则。 邮件流规则与你将在诸如 Outlook 的客户端应用程序中设置的规则之间的主要区别是，邮件流规则在消息传输过程中对其采取操作，而不是在消息送达后对其采取操作。 邮件流规则还包含更丰富的一组条件、例外和操作，这为实施许多类型的消息传递策略提供了灵活性。|
|[Intune 移动设备管理](https://docs.microsoft.com/intune/understand-explore/introduction-to-microsoft-intune)|Intune 使用移动操作系统中可用的协议或 API 提供移动设备管理 (MDM) 功能。 这包括如下所述的任务：将设备注册到管理功能以便 IT 获得访问公司服务的设备的清单；对设备进行配置以确保它们符合公司安全和运行状况标准；提供证书和 Wi-Fi/VPN 配置文件来访问公司设备；报告和度量设备对公司标准的符合性；以及从托管设备中删除公司数据。|
|[Intune 应用保护策略](https://docs.microsoft.com/intune/deploy-use/protect-app-data-using-mobile-app-management-policies-with-microsoft-intune)|Intune 应用保护策略可以用来保护移动应用中的公司数据（可以将设备注册到管理功能，也可以不注册）。 实际上，甚至可以使用其他非 Microsoft MDM 解决方案管理用户的移动设备，而使用 [Intune 帮助保护 Office 365 信息](https://docs.microsoft.com/enterprise-mobility-security/solutions/protect-company-data-without-managing-devices)。 可以在确保员工能够高效工作的同时，防止有意或无意的数据丢失。 通过实现应用级策略，可以限制对公司资源的访问并使数据处于 IT 部门的控制下。|
|[Azure AD 令牌生存期](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)|可以指定 Azure Active Directory (Azure AD) 颁发的令牌的生存期。 可以针对组织中的所有应用、多租户（多组织）应用程序或者组织中的特定服务主体设置生存期。|
|Microsoft 标识中转站|Microsoft 为每个移动平台提供了应用程序，可在来自不同供应商的应用程序之间桥接凭据，并支持需要一个安全位置来验证凭据的特殊增强功能。 我们称之为中转站。 在 iOS 和 Android 上，这些中转站是通过 Microsoft Authenticator 和 Intune 公司门户应用提供的。 在 Windows 10 中，此功能是由操作系统中内置的一个帐户选择器（从技术上称为 Web 身份验证中转站）提供的。|

