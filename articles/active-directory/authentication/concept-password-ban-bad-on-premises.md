---
title: Azure AD 密码保护预览版
description: 使用 Azure AD 密码保护预览版在本地 Active Directory 中禁止弱密码
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
ms.openlocfilehash: f1beae186f6eb276b9aa302d3d51f0ba8688e591
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415742"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>预览版：为 Windows Server Active Directory 强制执行 Azure AD 密码保护

|     |
| --- |
| Azure AD 密码保护和自定义禁止密码列表是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

Azure AD 密码保护是由 Azure Active Directory (Azure AD) 提供支持的一项处于公共预览版的新功能，用来增强组织的密码策略。 Azure AD 密码保护的本地部署同时使用 Azure AD 中存储的全局和自定义的禁止密码列表，并且在本地执行与 Azure AD 基于云的更改相同的检查。

## <a name="design-principles"></a>设计原理

Active Directory 的 Azure AD 密码保护是遵循以下原则设计的：

* 域控制器永远不需要与 Internet 直接通信
* 不会在域控制器上打开新的网络端口。
* 无需进行任何 Active Directory 架构更改。 该软件使用现有的 Active Directory 容器和 serviceConnectionPoint 架构对象。
* 没有最低的 Active Directory 域或林功能级别 (DFL\FFL) 要求。
* 该软件不在它保护的 Active Directory 域中创建任何帐户，也不需要其中的任何帐户。
* 用户明文密码永远不会离开域控制器（无论是在密码验证操作期间，还是在其他任何时间）。
* 支持增量部署，采取的权衡方法是仅在安装了域控制器代理的地方实施密码策略。
* 建议在所有 DC 上安装 DC 代理，以确保全面实施密码保护安全性。

## <a name="architectural-diagram"></a>体系结构图

在本地 Active Directory 环境中部署 Azure AD 密码保护之前，必须了解底层设计和功能概念。 下图显示了 Azure AD 密码保护的组件如何协同工作：

![Azure AD 密码保护组件如何配合工作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

上图显示，有三个基本软件组件构成了 Azure AD 密码保护：

* Azure AD 密码保护代理服务在当前 Active Directory 林中任何已加入域的计算机上运行。 它的主要用途是将来自域控制器的密码策略下载请求转发到 Azure AD，并将响应从 Azure AD 返回到域控制器。
* Azure AD 密码保护 DC 代理密码筛选器 dll 接收来自操作系统的用户密码验证请求，并将其转发到在域控制器本地运行的 Azure AD 密码保护 DC 代理服务。
* Azure AD 密码保护 DC 代理服务接收来自 DC 代理密码筛选器 dll 的密码验证请求，使用当前（本地可用）的密码策略处理它们，并返回结果（通过\失败）。

## <a name="theory-of-operations"></a>操作理论

在符合上图和设计原则的前提下，Azure AD 密码保护的实际工作原理是怎样的呢？

每个 Azure AD 密码保护代理服务通过在 Active Directory 中创建 serviceConnectionPoint 对象，将自身播发到林中的域控制器。

每个 Azure AD 密码保护 DC 代理服务还会在 Active Directory 中创建 serviceConnectionPoint 对象。 但是，此对象主要用于报告和诊断。

Azure AD 密码保护 DC 代理服务负责发起从 Azure AD 下载新密码策略的操作。 第一步是通过在林中查询代理 serviceConnectionPoint 对象，找到 Azure AD 密码保护代理服务。 找到可用的代理服务后，DC 代理服务将密码策略下载请求发送到该代理服务，后者将该请求发送到 Azure AD，然后向 DC 代理服务返回响应。 从 Azure AD 收到新的密码策略后，DC 代理服务将该策略存储在其域 sysvol 共享根目录中的专用文件夹内。 DC 代理服务还会监视此文件夹，以确定是否从域中的其他 DC 代理服务复制了更新的策略。

当服务启动时，Azure AD 密码保护 DC 代理服务始终会请求新策略。 DC 代理服务启动后，将定期（每小时一次）检查当前本地可用策略的新旧程度；如果当前策略超过一小时，则 DC 代理服务将按上文所述从 Azure AD 请求新策略，否则 DC 代理将继续使用当前策略。

Azure AD 密码保护 DC 代理服务使用 RPC（远程过程调用）通过 TCP 来与 Azure AD 密码保护代理服务通信。 代理服务在配置的动态或静态 RPC 端口上侦听这些调用。

Azure AD 密码保护 DC 代理永远不会侦听网络中提供的端口，代理服务永远不会尝试调用 DC 代理服务。

Azure AD 密码保护代理服务是无状态的；它永远不会缓存从 Azure 下载的策略或其他任何状态。

Azure AD 密码保护 DC 代理服务永远只会使用最新的本地可用密码策略评估用户的密码。 如果本地 DC 上没有可用的密码策略，则自动接受密码，并记录事件日志消息以向管理员发出警告。

Azure AD 密码保护不是实时的策略应用程序引擎。 在 Azure AD 中的密码策略配置发生更改的时间，与它到达并在所有域控制器上实施的时间之间可能存在延迟。

## <a name="foresttenant-binding-for-azure-ad-password-protection"></a>Azure AD 密码保护的林\租户绑定

在 Active Directory 林中部署 Azure AD 密码保护需要将 Active Directory 林以及任何已部署的 Azure AD 密码保护代理服务注册到 Azure AD。 这两种注册（林和代理）与注册期间使用的凭据隐式标识的特定 Azure AD 租户相关联。 每次下载一个 Azure AD 密码保护密码策略，该策略都始终特定于此租户（即，该策略始终是 Microsoft 全局受禁密码列表和租户自定义受禁密码列表的组合）。 不支持在绑定到不同 Azure AD 租户的林中配置不同的域或代理。

## <a name="license-requirements"></a>许可要求

全局禁止密码列表会使 Azure Active Directory (Azure AD) 的所有用户受益。

自定义的禁止密码列表需要 Azure AD Basic 许可证。

Windows Server Active Directory 的 Azure AD 密码保护需要 Azure AD Premium 许可证。

可以在 [Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)上找到包括成本在内的其他许可信息。

## <a name="download"></a>下载

可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)下载 Azure AD 密码保护的两个必需代理安装程序

## <a name="next-steps"></a>后续步骤

[部署 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)
