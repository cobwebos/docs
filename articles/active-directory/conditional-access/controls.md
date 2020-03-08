---
title: Azure AD 条件访问中的自定义控件
description: 了解 Azure Active Directory 条件访问中的自定义控件如何工作。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fff08690eb2807fbbd50f297761c57d3fef88fe
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671840"
---
# <a name="custom-controls-preview"></a>自定义控件（预览版）

自定义控件是 Azure Active Directory Premium P1 版的一项功能。 使用自定义控件时，用户将被重定向至兼容服务，以满足 Azure Active Directory 之外的其他要求。 为了满足此控制，用户的浏览器重定向到外部服务，执行任何所需的身份验证或验证活动，然后重定向回 Azure Active Directory。 Azure Active Directory 验证响应，并且如果用户已成功通过身份验证或验证，则用户将继续在条件访问流中。

这些控件允许将某些外部或自定义服务用作条件性访问控制，并通常扩展条件性访问的功能。

提供商当前提供的兼容服务包括：

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping 标识](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales （Gemalto 身份）](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

有关这些服务的详细信息，请直接与提供商联系。

## <a name="creating-custom-controls"></a>创建自定义控件

若要创建自定义控件，应首先联系想使用的控件的提供商。 每个非 Microsoft 提供程序都有自己的过程和要求来注册、订阅或以其他方式成为服务的一部分，并表明您希望与条件性访问集成。 此时，提供商将提供采用 JSON 格式的数据块。 此数据允许提供商和条件访问一起为你的租户一起工作，创建新的控件，并定义条件性访问如何判断你的用户是否已成功执行与提供程序的验证。

自定义控件不能与需要多重身份验证的身份保护自动化一起使用，也不能在特权标识管理器（PIM）中提升角色。

复制 JSON 数据，然后将其粘贴到相关文本框中。 除非明确了解所做的更改，否则不要对 JSON 进行任何更改。 做出任何更改可能中断提供商和 Microsoft 之间的联系，并且有可能将你和你的用户锁定在帐户之外。

用于创建自定义控件的选项位于 "**条件访问**" 页的 "**管理**" 部分中。

![控制](./media/controls/82.png)

单击“新建自定义控件”，打开包含控件 JSON 数据文本框的边栏选项卡。  

![控制](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>删除自定义控件

若要删除自定义控件，必须首先确保它未在任何条件性访问策略中使用。 完成后：

1. 转到“自定义控件”列表
1. 单击...  
1. 选择“删除”。

## <a name="editing-custom-controls"></a>编辑自定义控件

若要编辑自定义控件，必须删除当前控件，然后使用更新的信息创建新控件。

## <a name="next-steps"></a>后续步骤

- [条件访问公用策略](concept-conditional-access-policy-common.md)

- [仅限报表模式](concept-conditional-access-report-only.md)

- [使用条件性访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
