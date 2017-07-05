---
title: "体验 Windows 10 时将已加入域的设备连接到 Azure AD | Microsoft 文档"
description: "介绍了管理员可以如何配置支持设备域加入到企业网络的组策略。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
tags: azure-classic-portal
ms.assetid: 2ff29f3e-5325-4f43-9baa-6ae8d6bad3e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 39bd4fcbe1b9a197bcf5b8bb33bf6fffae2063fc
ms.contentlocale: zh-cn
ms.lasthandoff: 03/11/2017


---
# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时将已加入域的设备连接到 Azure AD）
过去 15 年及更长时间，域加入一直是组织连接设备进行工作的惯用方法。 它支持用户使用其 Windows Server Active Directory (Active Directory) 工作帐户或学校帐户登录到其设备，支持获准的 IT 部门全权管理这些设备。 组织通常依靠映像方法将设备分配给用户，通常使用 System Center Configuration Manager (SCCM) 或组策略管理这些设备。


将设备连接到 Azure Active Directory (Azure AD) 后，Windows 10 中的域加入功能可提供以下好处：

* 从任意位置单一登录 (SSO) 到 Azure AD 资源
* 使用工作帐户或学校帐户（不需要 Microsoft 帐户）访问企业版 Windows 应用商店
* 使用工作帐户或学校帐户（不需要 Microsoft 帐户）在设备间符合企业规范地漫游用户设置
* 工作帐户或学校帐户与 Windows Hello 企业版和 Windows Hello 结合使用，实现强身份验证和方便登录
* 可以将访问只限于符合组织的设备组策略设置的设备

## <a name="prerequisites"></a>先决条件
域加入仍非常有用。 不过，若要获取 SSO 的 Azure AD 好处、漫游工作帐户或学校帐户的设置以及使用工作帐户或学校帐户访问 Windows 应用商店，需要满足以下条件：

* Azure AD 订阅
* 将本地目录扩展到 Azure AD 的 Azure AD Connect
* 设置为将已加入域的设备连接到 Azure AD 的策略
* 适用于设备的 Windows 10 版本（版本 10551 或更高版本）

若要启用 Windows Hello 企业版和 Windows Hello，还需要满足以下条件：

- 用于用户证书颁发的**公钥基础结构 (PKI)**。

- **System Center Configuration Manager 当前分支** - 需要安装版本 1606 或更高版本。  
有关详细信息，请参阅： 
    - [System Center Configuration Manager 文档](https://technet.microsoft.com/library/mt346023.aspx)
    - [System Center Configuration Manager 团队博客](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx)
    - [System Center Configuration Manager 中的 Windows Hello 企业版设置](https://docs.microsoft.com/sccm/protect/deploy-use/windows-hello-for-business-settings)

作为 PKI 部署要求的替代方法，可以执行以下操作：

* 使多个域控制器具有 Windows Server 2016 Active Directory 域服务。

若要支持条件访问，可以创建用于允许访问已加入域、但未另行部署的设备的组策略设置。 若要基于设备的合规性管理访问控制，需要满足以下条件：

* 适用于 Windows Hello 企业版方案的 System Center Configuration Manager Current Branch（1606 或更高版本）

## <a name="deployment-instructions"></a>部署说明

若要部署，请按照[如何配置已加入域的 Windows 设备的 Azure Active Directory 自动注册](active-directory-conditional-access-automatic-device-registration-setup.md)中列出的步骤操作

## <a name="next-step"></a>后续步骤
* [面向企业的 Windows 10：在工作中使用设备的方式](active-directory-azureadjoin-windows10-devices-overview.md)
* [通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-user-upgrade.md)
* [了解 Azure AD Join 的使用方案](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connect domain-joined devices to Azure AD for Windows 10 experiences（体验 Windows 10 时将已加入域的设备连接到 Azure AD）](active-directory-azureadjoin-devices-group-policy.md)
* [设置 Azure AD Join](active-directory-azureadjoin-setup.md)


