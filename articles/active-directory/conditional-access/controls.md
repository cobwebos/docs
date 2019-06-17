---
title: 什么是 Azure Active Directory 条件访问中的访问控制？ | Microsoft Docs
description: 了解如何访问控制在 Azure Active Directory 条件性访问工作中。
services: active-directory
keywords: 条件访问应用，Azure AD 中，安全地访问公司资源，条件性访问策略的条件性访问
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd1ea7665daa6fd93a755cbdb20ebc63e3d63a94
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112555"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>什么是 Azure Active Directory 条件访问中的访问控制？

与[Azure Active Directory (Azure AD) 条件性访问](../active-directory-conditional-access-azure-portal.md)，可以控制如何授权的用户访问你的云应用。 在条件性访问策略中，您定义的响应 （"执行此操作"） （"在此情况下"） 触发策略的原因。

![控制](./media/controls/10.png)

条件性访问的上下文中

- “出现这种情况时”称为条件  

- “则执行此操作”称为访问控制  

条件语句与控制的组合表示条件性访问策略。

![控制](./media/controls/61.png)

每个控制要么限制人员或系统必须满足哪些要求才能登录，要么限制用户在登录后可以执行哪些操作。

访问控制分为两种类型：

- **授权控制** - 旨在限制访问

- **会话控制** - 旨在限制可以在会话中执行的操作

本主题介绍 Azure AD 条件访问中可用的各种控件。 

## <a name="grant-controls"></a>授权控制

使用授权控制，可以完全阻止访问，也可以选择所需的控制，限制为只有满足其他要求才能访问。 如果有多个控制，可以要求：

- 满足所有选定控制 (AND  )
- 满足一个选定控制 (OR  )

![控制](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>多重身份验证

此控制可用于要求必须通过多重身份验证，才能访问指定的云应用程序。 此控制支持以下多重身份验证提供程序：

- Azure 多重身份验证

- 本地多重身份验证提供程序，与 Active Directory 联合身份验证服务 (AD FS) 结合使用。

使用多重身份验证有助于保护资源，使其免遭可能已有权访问有效用户的主要凭据的未授权用户访问。

### <a name="compliant-device"></a>合规的设备

可以配置基于设备的条件性访问策略。 基于设备的条件访问策略的目的是仅授予对从所选的云应用的访问权限[托管设备](require-managed-devices.md)。 要求将设备标记为合规是限制对受管理设备的访问可以选择的一个选项。 可以通过 Intune（适用于任何设备 OS）或通过适用于 Windows 10 设备的第三方 MDM 系统将设备标记为合规。 不支持除 Windows 10 以外的设备 OS 类型的第三方 MDM 系统。 

你的设备需要先注册到 Azure AD，然后才能将其标记为合规。 若要注册设备，你有三种选择： 

- [Azure AD 注册设备](../devices/overview.md#azure-ad-registered-devices)
- [已加入 Azure AD 的设备](../devices/overview.md#azure-ad-joined-devices)  
- [已加入混合 Azure AD 的设备](../devices/overview.md#hybrid-azure-ad-joined-devices)

有关详细信息，请参阅[如何要求使用条件性访问的云应用访问权限的被管理的设备](require-managed-devices.md)。

### <a name="hybrid-azure-ad-joined-device"></a>混合 Azure AD 加入设备

需要的混合 Azure AD 加入的设备是您必须配置基于设备的条件性访问策略的另一个选项。 此要求是指已加入本地 Active Directory 的 Windows 台式机、笔记本电脑和企业平板电脑。 如果选择此选项，则条件性访问策略会授权使用加入到你的本地 Active Directory 和 Azure Active Directory 的设备进行的访问尝试。  

有关详细信息，请参阅[设置了 Azure Active Directory 基于设备的条件性访问策略](require-managed-devices.md)。

### <a name="approved-client-app"></a>核准客户端应用程序

由于员工使用移动设备执行个人和工作任务，因此可能需要能够保护设备访问的公司数据，即使这些设备不受你管理，也不例外。
可以使用 [Intune 应用程序保护策略](https://docs.microsoft.com/intune/app-protection-policy)，帮助保护公司数据，而不受任何移动设备管理 (MDM) 解决方案的影响。

利用核准客户端应用程序，可以要求客户端应用程序必须支持 [Intune 应用程序保护策略](https://docs.microsoft.com/intune/app-protection-policy)，才能访问云应用程序。 例如，可以限制为只有 Outlook 应用程序，才能访问 Exchange Online。 需要批准的客户端应用的条件性访问策略是也称为[基于应用的条件性访问策略](app-based-conditional-access.md)。 有关支持的核准客户端应用程序列表，请参阅[核准客户端应用程序要求](technical-reference.md#approved-client-app-requirement)。

### <a name="app-protection-policy-preview"></a>应用保护策略 （预览版）

由于员工使用移动设备执行个人和工作任务，因此可能需要能够保护设备访问的公司数据，即使这些设备不受你管理，也不例外。
可以使用 [Intune 应用程序保护策略](https://docs.microsoft.com/intune/app-protection-policy)，帮助保护公司数据，而不受任何移动设备管理 (MDM) 解决方案的影响。

使用应用保护策略，可以限制对已向 Azure 报告的客户端应用程序访问 AD 已收到[Intune 应用保护策略](https://docs.microsoft.com/intune/app-protection-policy)。 例如，可以限制对 Exchange Online 访问到具有 Intune 应用保护策略 Outlook 应用。 需要应用保护策略的条件性访问策略是也称为[保护基于应用的条件性访问策略](app-protection-based-conditional-access.md)。 

你的设备必须注册到 Azure AD 之前应用程序可以将标记为受保护的策略。

受保护的客户端应用，请参阅有关受支持的策略的列表[应用保护策略要求](technical-reference.md#app-protection-policy-requirement)。


### <a name="terms-of-use"></a>使用条款

在向某个资源授予访问权限之前，可以要求租户中的用户同意相关使用条款。 作为管理员，可以通过上传 PDF 文档配置和自定义使用条款。 如果用户属于此控制范围，则仅在同意使用条款的情况下才授予某个应用程序的访问权限。

### <a name="custom-controls-preview"></a>自定义控件（预览版）

将用户重定向至兼容服务，以满足其他要求 Azure Active Directory 之外的条件访问中，可以添加自定义控件。 这样，可以使用某些外部的多重身份验证和验证提供程序来强制实施条件性访问要求。 若要满足此控件要求，用户浏览器将重定向至外部服务，执行任何需要的身份验证或验证活动，然后重定向回 Azure Active Directory。 如果用户已成功完成身份验证或验证，该用户将继续留在条件访问流中。 

## <a name="custom-controls"></a>自定义控件

自定义控件是 Azure Active Directory Premium P1 版的一项功能。 使用自定义控件时，用户将被重定向至兼容服务，以满足 Azure Active Directory 之外的其他要求。 若要满足此控件要求，用户浏览器将重定向至外部服务，执行任何需要的身份验证或验证活动，然后重定向回 Azure Active Directory。 Azure Active Directory 验证响应，而且，如果用户成功通过身份验证或验证，用户将继续留在条件访问流。

这些控件允许使用某些外部或自定义服务用作条件访问控制和一般情况下扩展条件访问的功能。

提供商当前提供的兼容服务包括：

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [Ping 标识](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

有关这些服务的详细信息，请直接与提供商联系。

### <a name="creating-custom-controls"></a>创建自定义控件

若要创建自定义控件，应首先联系想使用的控件的提供商。 每个非 Microsoft 提供程序具有其自己的进程和要求，以注册、 订阅或以其他方式的服务，加入并指示你想要使用条件性访问将集成。 此时，提供商将提供采用 JSON 格式的数据块。 此数据允许提供程序和条件性访问一起服务于你的租户，创建新控件并定义条件性访问如何才能知道如果你的用户已成功执行了与提供程序的验证。

使用 Identity Protection 的自动化要求多重身份验证或提升角色特权标识管理器 (PIM)，则不能使用自定义控件。

复制 JSON 数据，然后将其粘贴到相关文本框中。 不要对 JSON 做任何更改，除非用户明确理解所做的更改。 做出任何更改可能中断提供商和 Microsoft 之间的联系，并且有可能将你和你的用户锁定在帐户之外。

若要创建自定义控件的选项处于**管理**一部分**条件性访问**页。

![控制](./media/controls/82.png)

单击“新建自定义控件”  ，打开包含控件 JSON 数据文本框的边栏选项卡。  

![控制](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>删除自定义控件

若要删除自定义控件，必须首先确保在任何条件访问策略上未被使用。 完成后：

1. 转到“自定义控件”列表

2. 单击...  

3. 选择“删除”。 

### <a name="editing-custom-controls"></a>编辑自定义控件

若要编辑自定义控件，必须删除当前控件，然后使用更新的信息创建新控件。

## <a name="session-controls"></a>会话控制

通过会话控制，可以限制云应用程序中的体验。 会话控制由云应用强制实施，取决于由 Azure AD 提供给应用的有关会话的其他信息。

![控制](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>使用应用所强制实施的限制

可以使用此控制要求 Azure AD 将设备信息传递给所选云应用。 设备信息使云应用能够知道连接是从兼容设备还是已加入域设备发起的。 此控制仅支持将 SharePoint Online 和 Exchange Online 作为选定的云应用。 选择后，云应用会使用设备信息为用户提供有限或完整的体验，具体取决于设备状态。

若要了解更多信息，请参阅以下文章：

- [使用 SharePoint Online 启用受限访问](https://aka.ms/spolimitedaccessdocs)

- [使用 Exchange Online 启用受限访问](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>后续步骤

- 如果你想要了解如何配置条件性访问策略，请参阅[需要 MFA 的特定应用的 Azure Active Directory 条件性访问](app-based-mfa.md)。

- 如果你已准备好配置你的环境的条件性访问策略，请参阅[的 Azure Active Directory 中条件性访问的最佳做法](best-practices.md)。
