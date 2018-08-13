---
title: Azure Active Directory 部署计划 | Microsoft Docs
description: 提供有关如何部署 Azure Active Directory 功能的端到端指南
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 08/03/2018
ms.author: lizross
ms.openlocfilehash: 07d3915fd007c0827b885b0603eb176b9e408576
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39508356"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 部署计划
正在查找有关如何部署某些 Azure Active Directory (Azure AD) 功能的端到端指南？ 以下部署计划详述了成功推出部分更常用 Azure AD 功能所需的业务价值、规划注意事项、设计和运营过程。 

在文档中，可以找到电子邮件模板、系统体系结构关系图、常见测试案例，等等。 

欢迎对文档提供反馈。 请参与这个简短的[调查](https://aka.ms/deploymentplanfeedback)，说出你对文档的看法。 

|场景 |Description |
|-|-|
|[单一登录](https://aka.ms/SSODPDownload)|进行单一登录时，只需使用单个用户帐户登录一次，就能访问开展业务所需的全部应用和资源。 登录之后，可以从 Microsoft Office 转到 SalesForce，再转到 Box，无需再次进行身份验证（例如键入密码）。|
|[用户预配](https://aka.ms/UserProvisioningDPDownload)|可以通过 Azure AD 自动创建、维护和删除云 (SaaS) 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。|
|[多重身份验证](https://aka.ms/MFADPDownload)|Azure 多重身份验证 (MFA) 是 Microsoft 的双重验证解决方案。 使用管理员批准的身份验证方法，Azure MFA 可帮助保护对数据和应用程序的访问，同时满足对简单登录过程的需求。|
|[条件性访问](https://aka.ms/CADPDownload)|使用条件访问时，可以根据条件实施自动化的访问控制决策，决定谁能够访问云应用。|
|[使用 ADFS 进行直通身份验证](https://aka.ms/ADFSTOPTADPDownload)|借助 Azure AD 直通身份验证证，你的用户可使用相同的密码登录到本地应用程序和基于云的应用程序。 此功能为用户提供更好的体验 - 少记一个密码，并且能减少 IT 支持人员成本，因为用户不太可能忘记如何登录。 当人们使用 Azure AD 登录时，此功能可直接通过本地 Active Directory 验证用户的密码。|
|[使用 ADFS 进行密码哈希同步](https://aka.ms/ADFSTOPHSDPDownload)|有了密码哈希同步，就可以将用户密码的哈希从本地 Active Directory 同步到 Azure AD，让 Azure AD 对用户进行身份验证，不与本地 Active Directory 交互|
|[无缝 SSO](https://aka.ms/SeamlessSSODPDownload)|Azure Active Directory 无缝单一登录（Azure AD 无缝 SSO）可使连接到企业网络的企业设备上的用户自动登录。 启用此功能后，用户无需键入其密码即可登录到 Azure AD；通常情况下，甚至无需键入其用户名。 此功能可让用户轻松访问基于云的应用程序，而无需使用其他任何本地组件。|
|[自助密码重置](https://aka.ms/SSPRDPDownload)|自助密码重置允许用户在需要时重置其密码，不需要管理员干预。|
|[Azure AD 应用程序代理](https://aka.ms/AppProxyDPDownload)|现今的员工想要随时随地都能在任何设备上高效工作。 他们想要在自己的设备上工作，无论设备是平板电脑、手机还是笔记本电脑。 员工期望能够访问其所有的应用程序：云中的 SaaS 应用和本地的公司应用。 传统上，提供本地应用程序访问权限会涉及虚拟专用网络 (VPN) 或外围网络 (DMZ)。 这些解决方案不仅复杂且难以确保安全性，而且设置和管理成本也很高。 还有更好的办法！ - Azure AD 应用程序代理|
