---
title: Azure AD 密码保护-Azure Active Directory
description: 使用 Azure AD 密码保护在本地 Active Directory 中禁止使用弱密码
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9d07099f8de996181948921330ef6744b302a8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848640"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>为 Windows Server Active Directory 强制执行 Azure AD 密码保护

Azure AD 密码保护是一项功能，可增强组织中的密码策略。 密码保护的本地部署同时使用存储在 Azure AD 中的全局和自定义禁止密码列表。 它在本地执行相同的检查，Azure AD 对基于云的更改进行检查。 这些检查在密码更改和密码重置方案期间执行。

## <a name="design-principles"></a>设计原理

Azure AD 密码保护在设计时需要考虑以下原则：

* 域控制器决不会直接与 internet 通信。
* 不会在域控制器上打开新的网络端口。
* 无需进行任何 Active Directory 架构更改。 软件使用现有 Active Directory**容器**和**serviceConnectionPoint**架构对象。
* 不需要最低 Active Directory 域或林功能级别（DFL/FFL）。
* 该软件不会创建或要求其保护的 Active Directory 域中的帐户。
* 在密码验证操作期间或在任何其他时间，用户明文密码始终不会离开域控制器。
* 该软件不依赖于其他 Azure AD 功能;例如 Azure AD 密码哈希同步不相关，因此 Azure AD 密码保护的工作不是必需的。
* 支持增量部署，但仅在安装域控制器代理（DC 代理）的位置强制实施密码策略。 有关更多详细信息，请参阅下一主题。

## <a name="incremental-deployment"></a>增量部署

Azure AD 密码保护支持跨 Active Directory 域中的域控制器进行增量部署，但请务必了解这一点究竟是什么意思，这一点非常重要。

Azure AD 密码保护 DC 代理软件只能在安装在域控制器上时验证密码，并且仅适用于发送到域控制器的密码更改。 不能控制 Windows 客户端计算机选择哪些域控制器来处理用户密码更改。 为了保证行为和通用密码保护安全的实施，必须在域中的所有域控制器上安装 DC 代理软件。

许多组织需要在执行完整部署之前，对其域控制器子集上的 Azure AD 密码保护进行认真测试。 Azure AD 密码保护支持部分部署，ie 即使域中的其他 Dc 未安装 DC 代理软件，在给定 DC 上的 DC 代理软件也会主动验证密码。 此类型的部分部署不安全，因此不建议使用，因为用于测试目的。

## <a name="architectural-diagram"></a>体系结构图

在本地 Active Directory 环境中部署 Azure AD 密码保护之前，请务必了解基础设计和函数概念。 下图显示了密码保护组件的协同工作方式：

![Azure AD 密码保护组件如何配合工作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 密码保护代理服务在当前 Active Directory 林中任何已加入域的计算机上运行。 它的主要用途是将密码策略下载请求从域控制器转发到 Azure AD。 然后，它会将 Azure AD 中的响应返回到域控制器。
* DC 代理的密码筛选器 DLL 接收来自操作系统的用户密码验证请求。 它将它们转发到域控制器上本地运行的 DC 代理服务。
* 密码保护的 DC 代理服务从 DC 代理的密码筛选器 DLL 接收密码验证请求。 它使用当前（本地可用）密码策略处理它们，并返回结果： *pass*或*fail*。

## <a name="how-password-protection-works"></a>密码保护的工作原理

每个 Azure AD 密码保护代理服务实例通过在 Active Directory 中创建**serviceConnectionPoint**对象，将自己公布到林中的域控制器。

每个用于密码保护的 DC 代理服务还会在 Active Directory 中创建**serviceConnectionPoint**对象。 此对象主要用于报告和诊断。

DC 代理服务负责从 Azure AD 中启动新密码策略的下载。 第一步是通过查询用于代理**serviceConnectionPoint**对象的林查找 Azure AD 密码保护代理服务。 当找到可用的代理服务时，DC 代理会将密码策略下载请求发送到代理服务。 代理服务会将请求发送到 Azure AD。 然后，代理服务会将响应返回到 DC 代理服务。

在 DC 代理服务接收到 Azure AD 的新密码策略后，该服务会将该策略存储在其域*sysvol*文件夹共享根目录下的专用文件夹中。 DC 代理服务还会监视此文件夹，以防新策略从域中的其他 DC 代理服务复制到中。

DC 代理服务始终在服务启动时请求新策略。 DC 代理服务启动后，它会每小时检查当前本地可用策略的期限。 如果策略超过1小时，则 DC 代理会通过代理服务请求 Azure AD 新策略，如前文所述。 如果当前策略不超过一小时，DC 代理将继续使用该策略。

每当下载 Azure AD 密码保护密码策略时，该策略特定于租户。 换句话说，密码策略始终是 Microsoft 全局禁止密码列表和每租户自定义禁止密码列表的组合。

DC 代理通过 TCP 通过 RPC 与代理服务通信。 代理服务根据配置在动态或静态 RPC 端口上侦听这些调用。

DC 代理从不侦听网络可用的端口。

代理服务永远不会调用 DC 代理服务。

代理服务是无状态的。 它从不缓存策略或从 Azure 下载的任何其他状态。

DC 代理服务始终使用最新的本地可用密码策略来评估用户的密码。 如果本地 DC 上没有可用的密码策略，将自动接受该密码。 发生这种情况时，将记录一条事件消息以警告管理员。

Azure AD 密码保护不是实时策略应用程序引擎。 Azure AD 中的密码策略配置更改发生的时间与更改达到并强制在所有域控制器上的时间之间可能存在延迟。

Azure AD 密码保护作为现有 Active Directory 密码策略的补充，而不是替代。 这包括任何其他可能已安装的第三方密码筛选器 dll。 Active Directory 总是要求所有密码验证组件都在接受密码之前同意。

## <a name="foresttenant-binding-for-password-protection"></a>密码保护的林/租户绑定

在 Active Directory 林中部署 Azure AD 密码保护需要使用 Azure AD 注册该林。 还必须将部署的每个代理服务注册到 Azure AD。 这些林和代理注册与特定 Azure AD 租户相关联，该租户由注册期间使用的凭据隐式标识。

林中的 Active Directory 林和所有已部署的代理服务必须注册到同一个租户。 不支持将 Active Directory 的林或该林中的任何代理服务注册到不同的 Azure AD 租户。 这种配置错误的部署的症状包括无法下载密码策略。

## <a name="download"></a>下载

Azure AD 密码保护的两个必需代理安装程序可从[Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)获取。

## <a name="next-steps"></a>后续步骤
[部署 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)
