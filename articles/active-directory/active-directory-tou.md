---
title: "Azure Active Directory 使用条款 | Microsoft Docs"
description: "通过 Azure AD 使用条款，你和你的公司可以向 Azure AD 服务用户提供使用条款。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/17/2017
ms.author: billmath
ms.openlocfilehash: cf7b64bd956c236f5c70bb7dcd209ecdcd7176dd
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2017
---
# <a name="azure-active-directory-terms-of-use-feature-preview"></a>Azure Active Directory 使用条款功能（预览）
Azure AD 使用条款提供了一个简单方法，组织可以使用它向最终用户显示信息。  这可确保用户看到法律要求或合规性要求的相关免责声明。

Azure AD 使用条款使用 PDF 格式显示内容。   此 PDF 可以是任意内容（例如现有的合同文档），使你可以在用户登录时收集最终用户协议。  可以将使用条款用于应用程序、用户组，如果你有多个使用条款，也可以将它们用于不同的目的。

此文档的剩余部分介绍了如何开始使用 Azure AD 使用条款。  

## <a name="why-use-azure-ad-terms-of-use"></a>为什么要使用 Azure AD 使用条款
发现难以让员工或来宾在获得访问权限之前先同意你的使用条款？ 在查看已同意或尚未同意公司使用条款的人员方面需要帮助？  Azure AD 使用条款提供了一个简单方法，组织可以使用它向最终用户显示信息。  这可确保他们看到法律要求或合规性要求的相关免责声明。

可以在以下应用场景中使用 Azure AD 使用条款：
-   针对组织中所有用户的常规使用条款。
-   基于用户属性（例如， [动态组](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups)中的医生与护士或国内员工和国际员工）的特定使用条款。
-   基于访问具有高业务影响的应用（例如 Salesforce）的特定使用条款。


## <a name="prerequisites"></a>先决条件
使用以下步骤配置 Azure AD 使用条款：

1. 使用你想要为其配置 Azure AD 使用条款的目录的全局管理员、安全管理员或条件访问管理员身份登录到 Azure AD。
2. 请确保此目录具有 Azure AD Premium P1、P2、EMS E3 或 EMS E5 订阅。  如果没有此订阅，请[获取 Azure AD Premium](active-directory-get-started-premium.md) 或[开始试用](https://azure.microsoft.com/trial/get-started-active-directory/)。
3. 可在 [https://aka.ms/catou](https://aka.ms/catou) 上查看 Azure AD 用户条款仪表板。

>[!IMPORTANT]
>条件访问策略控制（包括使用条款）不支持对服务帐户强制实施。  我们建议从条件访问策略中排除所有服务帐户。

## <a name="add-company-terms-of-use"></a>添加公司使用条款
完成使用条款后，请使用以下过程添加它。

### <a name="to-add-terms-of-use"></a>添加使用条款
1. 导航到仪表板（位于 [https://aka.ms/catou](https://aka.ms/catou)）
2. 单击“添加”。</br>
![添加 TOU](media/active-directory-tou/tou2.png)
3. 为该使用条款输入名称
4. 输入“显示名称”。  此标题是用户登录时将看到的内容。
5. 浏览到已完成的使用条款 (PDF)，并选择它。  建议的字号是 24。
6. 可以使用模板或自定义条件访问策略强制执行已上传的使用条款。  可以使用自定义条件访问条款策略将使用条款细化，可向下细化到特定云应用程序或用户组。  有关详细信息，请参阅[配置条件访问策略](active-directory-conditional-access-best-practices.md)
7. 单击“创建” 。
8. 选择自定义条件访问模板后，将出现新的屏幕，可以通过此屏幕自定义 CA 策略。
7. 现在应能看到你的新使用条款。</br>

![添加 TOU](media/active-directory-tou/tou3.png)

## <a name="delete-terms-of-use"></a>删除使用条款
可以使用以下过程删除旧的使用条款：

### <a name="to-delete-terms-of-use"></a>删除使用条款
1. 导航到仪表板（位于 [https://aka.ms/catou](https://aka.ms/catou)）
2. 选择你想要删除的使用条款。
3. 单击“删除” 。
4. 应不会再看到你的新使用条款。


## <a name="audit-terms-of-use"></a>审核使用条款
Azure AD 使用条款提供了易于使用的审核功能，使你可以看到哪些人已接受了你的使用条款及他们接受使用条款的时间。  若要开始使用审核功能，请使用以下过程：

### <a name="to-audit-terms-of-use"></a>审核使用条款
1. 导航到仪表板（位于 [https://aka.ms/catou](https://aka.ms/catou)）
2. 单击“审核事件”。</br>
![审核事件](media/active-directory-tou/tou8.png)
3.  在 Azure AD 审核日志屏幕上，可以使用提供的下拉列表筛选信息，将特定审核日志信息作为目标。
![审核事件](media/active-directory-tou/tou9.png)
4.  还可以将信息下载为 .csv 文件中，在本地使用它。

## <a name="what-users-see"></a>用户看到的内容
创建并强制执行使用条款后，处于范围内的用户将看到以下内容。  他们在登录期间将看到这些屏幕。
-   最佳做法是将此 PDF 的字号设置为 24。
![审核事件](media/active-directory-tou/tou10.png)
-   此屏幕显示它在移动设备上的外观</br></br>
![审核事件](media/active-directory-tou/tou11.png)

## <a name="additional-information"></a>其他信息
以下信息是需要注意的事项，可帮助你使用使用条款。

在以下情况下，处于范围的用户必须在注销后登录才能符合新策略的要求：
 - 在使用条款上启用了条件访问策略
 - 或创建了第二个使用条款

这是因为条件访问策略会立即生效。 发生此情况时，管理员将开始看到“表示遗憾的阴云”或“Azure AD 令牌颁发”。 管理员必须注销然并重新登录才能符合新策略的要求。





## <a name="frequently-asked-questions"></a>常见问题

**问：如何查看用户何时/是否接受了使用条款？**</br>
答：已将接受了使用条款的用户写入审核日志。 可以搜索 Azure AD 审核日志来查看结果。  

**问：更改使用条款后，是否需要用户重新接受该条款？**</br>
答：是的，管理员可以更改使用条款的条款，这需要用户重新接受新的条款。

**问：使用条款是否支持多种语言？**</br>
答：不支持，目前单个使用条款中无法包含多种语言。  但是，可以将范围设定到某个组（例如，适用于法国的使用条款不同于适用于英国的使用条款）。 

**问：什么时候会触发使用条款？**</br>
答：在登录体验期间触发使用条款。

**问：我可以将哪些应用程序作为使用条款的目标？**</br>
答：可以使用新式验证对企业应用程序创建条件访问策略。  有关详细信息，请参阅[企业应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal)。

**问：是否可以向给定用户或应用添加多个使用条款？**</br>
答：可以，方法是创建多个以这些组或应用为目标的条件访问策略。 如果用户处于多个使用条款的范围内，他们可一次同意一个使用条款。
 
**问：用户拒绝使用条款后会发生什么？**</br>
答：将阻止此用户访问该应用程序。 用户需要重新登录并同意条款才能获得访问权限。