---
title: "Azure AD Join 与 Azure Active Directory 域服务的比较 | Microsoft Docs"
description: "在 Azure AD Join 与 Azure AD 域服务之间做出决定"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: fb28c7a66d26119444dd720c2e134f2818d46cfc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>在 Azure Active Directory Join 与 Azure Active Directory 域服务之间进行选择
本文介绍 Azure Active Directory (AD) Join 与 Azure AD 域服务之间的差异，帮助根据用例做出选择。

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>已注册 Azure AD 和已加入 Azure AD 的设备
使用 Azure AD 可以管理组织所用的设备的标识，以及控制这些设备对企业资源的访问。 用户可将其个人（自带）设备注册到 Azure AD，后者为设备提供一个标识。 随后，当用户登录到 Azure AD 并使用设备访问受保护的资源时，Azure AD 可对该设备进行身份验证。 此外，可以使用 Microsoft Intune 等移动设备管理 (MDM) 软件来管理设备。 使用此功能可以限制从受管且符合策略的设备对敏感资源的访问。

还可将组织拥有的设备加入 Azure AD。 此机制提供的优势与将个人设备注册到 Azure AD 相同。 此外，用户可以使用其企业凭据登录到设备。 已加入 Azure AD 的设备提供以下优势：
* 单一登录 (SSO) 到 Azure AD 保护的应用程序
* 在不同的设备之间以符合企业策略的方式漫游用户设置。
* 使用企业凭据访问适用于企业的 Windows 应用商店。
* Windows Hello for Business
* 限制从符合企业策略的设备对应用和资源的访问。

| **设备类型** | **设备平台** | **机制** |
|:---| --- | --- |
| 个人设备 | Windows 10、iOS、Android、Mac OS | 已注册 Azure AD |
| 组织拥有的未加入本地 AD 的设备 | Windows 10 | 已加入 Azure AD |
| 组织拥有的已加入本地 AD 的设备 | Windows 10 | 已加入混合 Azure AD |

在已加入或已注册 Azure AD 的设备上，使用基于 OAuth/OpenID Connect 的新式协议执行用户身份验证。 这些协议设计为通过 Internet 工作，非常适合用于移动方案，可让用户从任何位置访问企业资源。


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>加入 Azure AD 域服务托管域
Azure AD 域服务在 Azure 虚拟网络中提供 AD 托管域。 可以使用传统的域加入机制将计算机加入此托管域。 可将 Windows 客户端（Windows 7、Windows 10）和 Windows Server 计算机加入托管域。 此外，还可将 Linux 和 Mac OS 计算机加入托管域。 将某台计算机加入 AD 域时，用户可以使用其企业凭据登录到该计算机。 可以使用组策略管理这些计算机，因此可以强制要求符合组织的安全策略。

在已加入域的计算机上，使用 NTLM 或 Kerberos 身份验证协议执行用户身份验证。 已加入域的计算机需要能够直接访问托管域的域控制器，这样才能进行用户身份验证。 因此，已加入域的计算机需与托管域位于同一虚拟网络中。 或者，需要通过对等互连的虚拟网络或站点到站点 VPN/ExpressRoute 连接，将已加入域的计算机连接到托管域。 因此，此机制不很适合用于移动设备，或者从企业网络外部连接到资源的设备。

> [!NOTE]
> 从技术上讲，可以通过站点到站点 VPN 或 ExpressRoute 连接将本地客户端工作站加入托管域。 但是，对于最终用户设备，我们强烈建议将设备注册到 Azure AD（个人设备），或者将设备加入 Azure AD（企业设备）。 此机制在 Internet 上可发挥更好的作用，可让最终用户在任何位置工作。 Azure AD 域服务非常适合 Azure 虚拟网络中部署的、已部署应用程序的 Windows 或 Linux Server 虚拟机。


## <a name="summary---key-differences"></a>摘要 - 重要差异
| **方面** | **Azure AD Join** | **Azure AD 域服务** |
|:---| --- | --- |
| 设备控制方 | Azure AD | Azure AD 域服务托管域 |
| 在目录中的表示形式 | Azure AD 目录中的设备对象。 | AAD-DS 托管域中的计算机对象。 |
| 身份验证 | 基于 OAuth/OpenID Connect 的协议 | Kerberos、NTLM 协议 |
| 管理 | Intune 等移动设备管理 (MDM) 软件 | 组策略 |
| 联网 | 通过 Internet 工作 | 要求计算机与托管域位于同一虚拟网络。|
| 非常适合用于... | 最终用户移动设备或台式机设备 | Azure 中部署的服务器虚拟机 |


## <a name="next-steps"></a>后续步骤
### <a name="learn-more-about-azure-ad-domain-services"></a>了解有关 Azure AD 域服务的详细信息
* [Azure AD 域服务概述](active-directory-ds-overview.md)
* [功能](active-directory-ds-features.md)
* [部署方案](active-directory-ds-scenarios.md)
* [了解 Azure AD 域服务是否适合用例](active-directory-ds-comparison.md)
* [了解如何将 Azure AD 域服务与 Azure AD 目录同步](active-directory-ds-synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>详细了解 Azure AD Join
* [Azure Active Directory 中的设备管理简介](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Azure AD 域服务入门
* [使用 Azure 门户启用 Azure AD 域服务](active-directory-ds-getting-started.md)
