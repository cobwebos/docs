---
title: Azure AD 密码保护 - Azure 活动目录
description: 通过使用 Azure AD 密码保护，禁止本地活动目录中的弱密码
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848640"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>为 Windows Server Active Directory 强制执行 Azure AD 密码保护

Azure AD 密码保护是增强组织中密码策略的功能。 密码保护的本地部署使用存储在 Azure AD 中的全局和自定义禁止密码列表。 它在本地执行与 Azure AD 对基于云的更改相同的检查。 这些检查在密码更改和密码重置方案期间执行。

## <a name="design-principles"></a>设计原理

Azure AD 密码保护的设计考虑到了以下原则：

* 域控制器永远不必与互联网直接通信。
* 不会在域控制器上打开新的网络端口。
* 无需进行任何 Active Directory 架构更改。 该软件使用现有的活动目录**容器****和服务连接点**架构对象。
* 不需要最小活动目录域或林功能级别 （DFL/FFL）。
* 该软件不创建或要求帐户在活动目录域中，它保护。
* 用户明文密码永远不会离开域控制器，无论是在密码验证操作期间还是在任何其他时间。
* 该软件不依赖于其他 Azure AD 功能;因此，该软件不依赖于其他 Azure AD 功能。例如，Azure AD 密码哈希同步不相关，并且不需要 Azure AD 密码保护才能正常工作。
* 支持增量部署，但密码策略仅在安装域控制器代理 （DC 代理）时强制执行。 有关详细信息，请参阅下一个主题。

## <a name="incremental-deployment"></a>增量部署

Azure AD 密码保护支持在 Active Directory 域中的域控制器之间增量部署，但了解这真正含义和权衡非常重要。

Azure AD 密码保护 DC 代理软件只能在安装在域控制器上时验证密码，并且只能验证发送到该域控制器的密码更改。 无法控制 Windows 客户端计算机选择哪些域控制器来处理用户密码更改。 为了保证一致的行为和通用的密码保护安全实施，DC 代理软件必须安装在域中的所有域控制器上。

许多组织希望在执行完全部署之前对其域控制器的子集仔细测试 Azure AD 密码保护。 Azure AD 密码保护确实支持部分部署，即给定 DC 上的 DC 代理软件将主动验证密码，即使域中的其他 DC 未安装 DC 代理软件也是如此。 此类型的部分部署不安全，不建议出于测试目的以外的部署。

## <a name="architectural-diagram"></a>体系结构图

在本地活动目录环境中部署 Azure AD 密码保护之前，了解基础设计和功能概念非常重要。 下图显示了密码保护组件如何协同工作：

![Azure AD 密码保护组件如何配合工作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 密码保护代理服务在当前 Active Directory 林中任何已加入域的计算机上运行。 其主要目的是将密码策略下载请求从域控制器转发到 Azure AD。 然后，它将 Azure AD 的响应返回到域控制器。
* DC 代理的密码筛选器 DLL 接收来自操作系统的用户密码验证请求。 它将它们转发到在域控制器上本地运行的 DC 代理服务。
* 密码保护的 DC 代理服务接收来自 DC 代理的密码筛选器 DLL 的密码验证请求。 它使用当前（本地可用）密码策略处理它们，并返回结果：*通过*或*失败*。

## <a name="how-password-protection-works"></a>密码保护的工作原理

每个 Azure AD 密码保护代理服务实例通过在 Active Directory 中创建服务**ConnectPoint**对象，向林中的域控制器通告自身。

每个用于密码保护的 DC 代理服务还会在活动目录中创建**一个服务连接点**对象。 此对象主要用于报告和诊断。

DC 代理服务负责从 Azure AD 启动新密码策略的下载。 第一步是通过查询代理**服务 ConnectPoint**对象的林来查找 Azure AD 密码保护代理服务。 找到可用的代理服务后，DC 代理会向代理服务发送密码策略下载请求。 代理服务又将请求发送到 Azure AD。 然后，代理服务返回对 DC 代理服务的响应。

DC 代理服务从 Azure AD 收到新的密码策略后，该服务将该策略存储在其域*sysvol*文件夹共享根目录的专用文件夹中。 DC 代理服务还会监视此文件夹，以防从域中的其他 DC 代理服务复制更新的策略。

DC 代理服务始终在服务启动时请求新策略。 启动 DC 代理服务后，它会每小时检查当前本地可用策略的年数。 如果策略超过一小时，DC 代理将通过代理服务从 Azure AD 请求新策略，如前所述。 如果当前策略的年长时间不超过一小时，DC 代理将继续使用该策略。

每当下载 Azure AD 密码保护密码策略时，该策略都是特定于租户的。 换句话说，密码策略始终是 Microsoft 全局禁止密码列表和每个租户自定义禁止密码列表的组合。

DC 代理通过 TCP 通过 RPC 与代理服务通信。 代理服务侦听动态或静态 RPC 端口上的这些调用，具体取决于配置。

DC 代理从不侦听网络可用端口。

代理服务从不调用 DC 代理服务。

代理服务是无状态的。 它永远不会缓存从 Azure 下载的策略或任何其他状态。

DC 代理服务始终使用最新的本地可用密码策略来评估用户的密码。 如果本地 DC 上没有可用的密码策略，则将自动接受密码。 发生这种情况时，将记录事件消息以警告管理员。

Azure AD 密码保护不是实时策略应用程序引擎。 在 Azure AD 中进行密码策略配置更改和更改达到并在所有域控制器上强制执行之间可能会有延迟。

Azure AD 密码保护充当现有活动目录密码策略的补充，而不是替换。 这包括可能安装的任何其他第三方密码筛选器 dll。 活动目录始终要求所有密码验证组件在接受密码之前都同意。

## <a name="foresttenant-binding-for-password-protection"></a>用于密码保护的林/租户绑定

在活动目录林中部署 Azure AD 密码保护需要将该林与 Azure AD 注册。 部署的每个代理服务也必须在 Azure AD 中注册。 这些林和代理注册与特定的 Azure AD 租户相关联，该租户由注册期间使用的凭据隐式标识。

活动目录林和林中的所有已部署代理服务必须注册到同一租户。 不支持将 Active Directory 林或该林中的任何代理服务注册到不同的 Azure AD 租户。 此类配置错误的部署的症状包括无法下载密码策略。

## <a name="download"></a>下载

Azure AD 密码保护所需的两个代理安装程序可从 Microsoft[下载中心](https://www.microsoft.com/download/details.aspx?id=57071)获得。

## <a name="next-steps"></a>后续步骤
[部署 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)
