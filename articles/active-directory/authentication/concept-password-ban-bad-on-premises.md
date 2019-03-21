---
title: Azure AD 密码保护
description: 禁止通过使用 Azure AD 密码保护的本地 Active Directory 中的弱密码
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1b3660d256e4beda948f723035aa75ca8a9ed2e
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58284862"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>为 Windows Server Active Directory 强制执行 Azure AD 密码保护

Azure AD 密码保护是一项功能，增强了在组织中的密码策略。 在本地部署密码保护使用这两个全局和自定义受禁密码列表存储在 Azure AD 中。 它执行相同检查在本地与 Azure AD 进行基于云的更改。

## <a name="design-principles"></a>设计原理

Azure AD 密码保护专为提供记住这些原则：

* 域控制器无需直接与 internet 进行通信。
* 不会在域控制器上打开新的网络端口。
* 无需进行任何 Active Directory 架构更改。 软件使用现有的 Active Directory**容器**并**serviceConnectionPoint**架构对象。
* 没有最小 Active Directory 域或林功能级别 (DFL/FFL) 是必需的。
* 该软件不会创建或要求它保护的 Active Directory 域中的帐户。
* 在密码验证期间或在其他任何时间，用户明文密码不要将域控制器。
* 支持增量部署。 但是，仅安装域控制器和代理 （DC 代理） 实施密码策略。
* 我们建议，以确保通用密码保护安全实施的所有域控制器上安装 DC 代理。

## <a name="architectural-diagram"></a>体系结构图

请务必了解的基础设计和函数的概念之前部署的本地 Active Directory 环境中的 Azure AD 密码保护。 下图显示了密码保护的组件如何协同工作：

![Azure AD 密码保护组件如何配合工作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 密码保护代理服务在当前 Active Directory 林中任何已加入域的计算机上运行。 其主要用途是将密码策略的下载请求从域控制器转发到 Azure AD。 然后返回响应从 Azure AD 域控制器。
* 密码筛选器 DLL 的 DC 代理接收来自操作系统的用户密码验证请求。 它将其转发到本地的域控制器运行的 DC 代理服务。
* 密码保护的 DC 代理服务接收来自密码筛选器 DLL 的 DC 代理密码验证请求。 它使用当前的 （本地可用） 的密码策略的处理它们，并返回结果：*传递*或*失败*。

## <a name="how-password-protection-works"></a>密码保护的工作原理

每个 Azure AD 密码保护代理服务实例将公布自己到林中的域控制器通过创建**serviceConnectionPoint** Active Directory 中的对象。

针对密码保护的每个 DC 代理服务还会创建**serviceConnectionPoint** Active Directory 中的对象。 此对象主要用于报告和诊断功能。

DC 代理服务负责启动下载的 Azure AD 中的新密码策略。 第一步是 Azure AD 密码保护代理服务通过查询来查找代理服务器的林**serviceConnectionPoint**对象。 当找到可用的代理服务时，DC 代理会将密码策略下载请求发送到代理服务。 代理服务又将请求发送到 Azure AD。 代理服务然后将响应返回给 DC 代理服务。

DC 代理服务从 Azure AD 收到新的密码策略后，服务会将该策略存储在专用文件夹中其域的根目录*sysvol*文件夹共享。 DC 代理服务还监视此文件夹，以防从域中的其他 DC 代理服务以复制较新的策略。

DC 代理服务始终请求在服务启动新的策略。 DC 代理服务启动后，它会检查每小时当前的本地可用策略的期限。 如果策略超过一小时，DC 代理新策略从 Azure AD 请求，如前面所述。 如果当前策略不超过一小时，DC 代理将继续使用该策略。

只要下载 Azure AD 密码保护密码策略，该策略是特定于租户。 换而言之，密码策略始终是 Microsoft 全球受禁密码列表和每个租户的自定义受禁密码列表的组合。

DC 代理通过 TCP 通信与通过 RPC 代理服务。 代理服务侦听的动态或静态的 RPC 端口，具体取决于配置这些调用。

DC 代理永远不会在网络可用的端口上侦听。

代理服务永远不会调用 DC 代理服务。

代理服务是无状态。 它永远不会缓存策略或任何其他状态已从 Azure 下载。

DC 代理服务始终使用最新的本地可用密码策略来评估用户的密码。 如果没有密码策略在本地的 DC 上不可用，会自动接受此密码。 在这种情况，被记录的事件消息来警告管理员。

Azure AD 密码保护并不是实时的策略应用程序引擎。 可以在 Azure AD 中执行密码策略配置更改时与该更改达到和所有域控制器上强制实施之间延迟。

## <a name="foresttenant-binding-for-password-protection"></a>针对密码保护的林/租户绑定

在 Active Directory 林中的 Azure AD 密码保护的部署需要该林与 Azure AD 的注册。 此外必须与 Azure AD 注册部署每个代理服务。 这些林和代理注册相关联的特定 Azure AD 租户，在注册过程中使用的凭据由隐式标识。

Active Directory 林和在林中的所有已部署的代理服务必须使用相同的租户注册。 不支持具有 Active Directory 林或任何代理服务，因为林中要注册到不同的 Azure AD 租户。 部署配置错误的症状包括无法下载的密码策略。

## <a name="license-requirements"></a>许可要求

全局受禁的密码列表的优点适用于 Azure AD 的所有用户。

自定义受禁密码列表需要 Azure AD Basic 许可证。

Windows Server Active Directory 的 Azure AD 密码保护需要 Azure AD Premium 许可证。

有关许可的其他信息，请参阅[Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="download"></a>下载

为 Azure AD 密码保护的两个必需的代理安装程序中有[Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071)。

## <a name="next-steps"></a>后续步骤
[部署 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)
