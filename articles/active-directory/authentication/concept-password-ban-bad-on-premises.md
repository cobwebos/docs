---
title: Azure AD 密码保护预览版
description: 使用 Azure AD 密码保护预览版在本地 Active Directory 中禁止弱密码
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 0819a947229e633331253923654de56638a6c76a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36292926"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>预览版：为 Windows Server Active Directory 实施 Azure AD 密码保护

|     |
| --- |
| Azure AD 密码保护和自定义禁止密码列表是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

Azure AD 密码保护是由 Azure Active Directory (Azure AD) 提供支持的一项处于公共预览版的新功能，用来增强组织的密码策略。 Azure AD 密码保护的本地部署同时使用 Azure AD 中存储的全局和自定义的禁止密码列表，并且在本地执行与 Azure AD 基于云的更改相同的检查。

有三个软件组件构成了 Azure AD 密码保护：

* Azure AD 密码保护代理服务在当前 Active Directory 林中任何已加入域的计算机上运行。 它将来自域控制器的请求转发到 Azure AD 并将响应从 Azure AD 返回到域控制器。
* Azure AD 密码保护 DC 代理服务接收来自 DC 代理密码筛选器 dll 的密码验证请求，使用当前在本地可用的密码策略处理它们，并返回结果（通过\失败）。 此服务负责定期（每小时一次）调用 Azure AD 密码保护代理服务来检索密码策略的新版本。 用于调用的以及来自 Azure AD 密码保护代理服务的通信是基于 TCP 通过 RPC（远程过程调用）处理的。 在检索时，新策略存储在 sysvol 文件夹中，它们可以从该文件夹复制到其他域控制器。 DC 代理服务还监视 sysvol 文件夹中的更改以防其他域控制器在那里写入了新的密码策略，如果已有相当新的策略可用，则会跳过对 Azure AD 密码保护代理服务的检查。
* DC 代理密码筛选器 dll 接收来自操作系统的密码验证请求，并将其转发到在域控制器本地运行的 Azure AD 密码保护 DC 代理服务。

![Azure AD 密码保护组件如何配合工作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

## <a name="requirements"></a>要求

* 安装了 Azure AD 密码保护组件的所有计算机（包括域控制器）都必须运行 Windows Server 2012 或更高版本。
* 在每个域中的至少一台域控制器和托管 Azure AD 密码保护代理服务的至少一台服务器之间必须存在网络连接。
* 运行 DC 代理服务软件的任何 Active Directory 域都必须使用 DFSR 进行 sysvol 复制。
* 拥有用来向 Azure AD 注册 Azure AD 密码保护代理服务的全局管理员帐户。
* 拥有在林根域中具有 Active Directory 域管理员权限的帐户。

### <a name="license-requirements"></a>许可要求

全局禁止密码列表会使 Azure Active Directory (Azure AD) 的所有用户受益。

自定义的禁止密码列表需要 Azure AD Basic 许可证。

Windows Server Active Directory 的 Azure AD 密码保护需要 Azure AD Premium 许可证。 

可以在 [Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)上找到包括成本在内的其他许可信息。

## <a name="download"></a>下载

可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)下载 Azure AD 密码保护的两个必需安装程序

## <a name="answers-to-common-questions"></a>常见问题的解答

* 域控制器不需要 Internet 连接。 运行 Azure AD 密码保护代理服务的计算机是唯一需要 Internet 连接的计算机。
* 域控制器上未打开任何网络端口。
* 无需进行任何 Active Directory 架构更改。
   * 该软件使用现有的 Active Directory 容器和 serviceConnectionPoint 架构对象。
* 没有最小 Active Directory 域或林功能级别 (DFL\FFL) 要求。
* 该软件不在它保护的 Active Directory 域中创建任何帐户，也不需要其中的任何帐户。
* 支持增量部署，采取的权衡方法是仅在安装了域控制器代理的地方实施密码策略。
* Azure AD 密码保护不是实时的策略应用程序引擎。 在密码策略配置发生更改的时间与它到达并在所有域控制器上实施的时间之间可能存在延迟。

## <a name="next-steps"></a>后续步骤

[部署 Azure AD 密码保护](howto-password-ban-bad-on-premises.md)