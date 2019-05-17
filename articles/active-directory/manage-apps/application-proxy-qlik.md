---
title: Azure AD 应用代理和 Qlik Sense| Microsoft Docs
description: 在 Azure 门户中，打开应用程序代理并为反向代理安装连接器。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fc03751859160f2c234810dcb01e1579cb3e2a8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782658"
---
# <a name="application-proxy-and-qlik-sense"></a>应用程序代理和 Qlik Sense 
Azure Active Directory 应用程序代理和 Qlik Sense 已进行合作，确保可轻松使用应用程序代理来提供对 Qlik Sense 部署的远程访问。  

## <a name="prerequisites"></a>必备组件 
此方案的剩余部分假定你已完成以下操作：
 
- 配置 [Qlik Sense](https://community.qlik.com/docs/DOC-19822)。 
- [安装应用程序代理连接器](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>在 Azure 中发布应用程序 
若要发布 QlikSense，需要在 Azure 中发布两个应用程序。  

### <a name="application-1"></a>应用程序 1： 
按以下步骤发布应用。 有关步骤 1-8 更详细的演练，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)。 


1. 以全局管理员身份登录到 Azure 门户。 
2. 选择“Azure Active Directory” > “企业应用程序”。 
3. 单击边栏选项卡顶部的“添加”。 
4. 选择“本地应用程序”。 
5. 在必填的字段中填写有关新应用的信息。 参考以下指导完成设置： 
   - **内部 URL**：此应用程序具有的内部 URL 本身应为 QlikSense URL。 例如，**https&#58;//demo.qlikemm.com:4244** 
   - **预身份验证方法**：Azure Active Directory（推荐使用但并非必需项） 
1. 选择边栏选项卡底部的“添加”。 添加应用程序后，将打开快速启动菜单。 
2. 在快速启动菜单中选择“分配用于测试的用户”，并将至少一个用户添加到应用程序。 确保此测试帐户有权访问本地应用程序。 
3. 选择“分配”，保存测试用户分配。 
4. （可选）在应用管理边栏选项卡中选择“单一登录”。 从下拉菜单中选择“Kerberos 约束委派”，然后根据 Qlik 配置填写必填字段。 选择“保存”。 

### <a name="application-2"></a>应用程序 2： 
按照应用程序 1 的相同步骤操作，但存在以下例外： 

**步骤 5**：内部 URL 现应为包含应用程序所用的身份验证端口的 QlikSense URL。 HTTPS 的默认端口为 4244，HTTP 的默认端口为 4248。 例如：**https&#58;//demo.qlik.com:4244**</br></br> 
**步骤 10：** 请勿设置 SSO，并禁用“单一登录”****
 
 
## <a name="testing"></a>正在测试 
现在已准备好测试应用程序。 访问应用程序 1 中用来发布 QlikSense 的外部 URL，并以分配到两个应用程序的用户身份登录。  

## <a name="additional-references"></a>其他参考
有关使用应用程序代理发布 Qlik Sense 的更多信息，请参阅 Qlik 社区文章：[Azure AD with Integrated Windows Authentication using a Kerberos Constrained Delegation with Qlik Sense](https://community.qlik.com/docs/DOC-20183)（通过 Kerberos 约束委派和 Qlik Sense，将 Azure AD 与集成 Windows 身份验证配合使用）。

## <a name="next-steps"></a>后续步骤

- [使用应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)
- [使用应用程序代理连接器](application-proxy-connector-groups.md)

