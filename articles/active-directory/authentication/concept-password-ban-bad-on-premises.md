---
title: Azure AD 密码保护-Azure Active Directory
description: 使用 Azure AD 密码保护在本地 Active Directory 域服务环境中禁止使用弱密码
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09ad2991d552cb9886911ac75ea23c690204a71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88116642"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>为 Active Directory 域服务强制实施本地 Azure AD 密码保护

Azure AD 密码保护检测并阻止已知弱密码及其变种，还可以阻止特定于你的组织的其他弱条款。 Azure AD 密码保护的本地部署使用 Azure AD 中存储的相同的全局和自定义禁止密码列表，并根据 Azure AD 对基于云的更改执行相同的本地密码更改检查。 这些检查在密码更改和密码重置事件期间针对本地 Active Directory 域服务 (AD DS) 域控制器执行。

## <a name="design-principles"></a>设计原理

Azure AD 密码保护的设计考虑了以下原则：

* 域控制器 (Dc) 不必直接与 internet 通信。
* 在 Dc 上未打开任何新的网络端口。
* 不需要 AD DS 架构更改。 软件使用现有 AD DS *容器* 和 *serviceConnectionPoint* 架构对象。
* 无最低 AD DS 域或林功能级别 (DFL/FFL) 是必需的。
* 该软件不会创建或要求其保护的 AD DS 域中的帐户。
* 在密码验证操作期间或在任何其他时间，用户明文密码始终不会离开域控制器。
* 该软件不依赖于其他 Azure AD 的功能。 例如，Azure AD 密码哈希同步 (PHS) 与 Azure AD 密码保护不相关或不是必需的。
* 支持增量部署，但仅在安装了域控制器代理 (DC Agent) 的情况下才强制执行密码策略。

## <a name="incremental-deployment"></a>增量部署

Azure AD 密码保护支持跨 AD DS 域中的 Dc 进行增量部署。 重要的是了解这一点究竟是什么意思以及权衡是什么。

Azure AD 密码保护 DC 代理软件只能在其安装在 DC 上时验证密码，并且仅适用于发送到该 DC 的密码更改。 不能控制 Windows 客户端计算机选择哪些 Dc 来处理用户密码更改。 为了保证行为和通用 Azure AD 强制实施密码保护安全，必须在域中的所有 Dc 上安装 DC 代理软件。

许多组织希望在进行完整部署之前，仔细测试其 Dc 子集上 Azure AD 密码保护。 为了支持这种情况，Azure AD 密码保护支持部分部署。 即使域中的其他 Dc 未安装 DC 代理软件，给定 DC 上的 DC 代理软件也会主动验证密码。 此类型的部分部署不安全，因此不建议使用，因为用于测试目的。

## <a name="architectural-diagram"></a>体系结构图

在本地 AD DS 环境中部署 Azure AD 密码保护之前，请务必了解基础设计和函数概念。 下图显示了 Azure AD 密码保护的组件如何协同工作：

![Azure AD 密码保护组件如何配合工作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 密码保护代理服务在当前 AD DS 林中的任何已加入域的计算机上运行。 服务的主要用途是将密码策略下载请求从 Dc 转发到 Azure AD，然后将 Azure AD 中的响应返回到 DC。
* DC 代理的密码筛选器 DLL 接收来自操作系统的用户密码验证请求。 筛选器将它们转发到在 DC 上本地运行的 DC 代理服务。
* Azure AD 密码保护的 DC 代理服务从 DC 代理的密码筛选器 DLL 接收密码验证请求。 DC 代理服务使用当前 (本地可用) 密码策略处理它们，并返回 *通过* 或 *失败*的结果。

## <a name="how-azure-ad-password-protection-works"></a>Azure AD 密码保护的工作原理

本地 Azure AD 密码保护组件的工作原理如下所示：

1. 每个 Azure AD 密码保护代理服务实例通过在 Active Directory 中创建 *serviceConnectionPoint* 对象，将自己公布到林中的 dc。

    Azure AD 密码保护的每个 DC 代理服务还会在 Active Directory 中创建 *serviceConnectionPoint* 对象。 此对象主要用于报告和诊断。

1. DC 代理服务负责从 Azure AD 中启动新密码策略的下载。 第一步是通过查询用于代理 *serviceConnectionPoint* 对象的林查找 Azure AD 密码保护代理服务。

1. 当找到可用的代理服务时，DC 代理会将密码策略下载请求发送到代理服务。 代理服务会将请求发送到 Azure AD，然后将响应返回到 DC 代理服务。

1. 在 DC 代理服务接收到 Azure AD 的新密码策略后，该服务会将该策略存储在其域 *sysvol* 文件夹共享根目录下的专用文件夹中。 DC 代理服务还会监视此文件夹，以防新策略从域中的其他 DC 代理服务复制到中。

1. DC 代理服务始终在服务启动时请求新策略。 DC 代理服务启动后，它会每小时检查当前本地可用策略的期限。 如果策略超过1小时，则 DC 代理会通过代理服务请求 Azure AD 新策略，如前文所述。 如果当前策略不超过一小时，DC 代理将继续使用该策略。

1. 当 DC 接收到密码更改事件时，将使用缓存的策略来确定是接受还是拒绝新密码。

### <a name="key-considerations-and-features"></a>关键注意事项和功能

* 每当下载 Azure AD 密码保护密码策略时，该策略特定于租户。 换句话说，密码策略始终是 Microsoft 全局禁止密码列表和每租户自定义禁止密码列表的组合。
* DC 代理通过 TCP 通过 RPC 与代理服务通信。 代理服务根据配置在动态或静态 RPC 端口上侦听这些调用。
* DC 代理从不侦听网络可用的端口。
* 代理服务永远不会调用 DC 代理服务。
* 代理服务是无状态的。 它从不缓存策略或从 Azure 下载的任何其他状态。
* DC 代理服务始终使用最新的本地可用密码策略来评估用户的密码。 如果本地 DC 上没有可用的密码策略，将自动接受该密码。 发生这种情况时，将记录一条事件消息以警告管理员。
* Azure AD 密码保护不是实时策略应用程序引擎。 Azure AD 中的密码策略配置更改发生的时间和该更改达到并在所有 Dc 上强制执行之间可能存在延迟。
* Azure AD 密码保护作为现有 AD DS 密码策略的补充，而不是替代。 这包括任何其他可能已安装的第三方密码筛选器 dll。 AD DS 总是要求所有密码验证组件都在接受密码之前同意。

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>用于 Azure AD 密码保护的林/租户绑定

在 AD DS 林中部署 Azure AD 密码保护需要使用 Azure AD 注册该林。 还必须将部署的每个代理服务注册到 Azure AD。 这些林和代理注册与特定 Azure AD 租户相关联，该租户由注册期间使用的凭据隐式标识。

林中的 AD DS 林和所有已部署的代理服务必须注册到同一个租户。 不支持将 AD DS 的林或该林中的任何代理服务注册到不同的 Azure AD 租户。 这种配置错误的部署的症状包括无法下载密码策略。

> [!NOTE]
> 因此，具有多个 Azure AD 租户的客户必须选择一个可分辨租户来注册每个林以 Azure AD 密码保护目的。

## <a name="download"></a>下载

Azure AD 密码保护的两个必需代理安装程序可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)获取。

## <a name="next-steps"></a>后续步骤

若要开始使用本地 Azure AD 密码保护，请完成以下操作方法：

> [!div class="nextstepaction"]
> [部署本地 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)
