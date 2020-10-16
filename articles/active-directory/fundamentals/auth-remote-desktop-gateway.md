---
title: Azure Active Directory 远程桌面网关服务
description: 实现此身份验证模式的体系结构指南
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff099d41970ad4d78d5c6035a60f448f29ab24b1
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114083"
---
# <a name="remote-desktop-gateway-services"></a>远程桌面网关服务

标准远程桌面服务 (RDS) 部署包括在 Windows Server 上运行的各种 [远程桌面角色服务](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) 。 使用 Azure Active Directory (Azure AD) 应用程序代理的 RDS 部署与运行连接器服务的服务器之间具有永久出站连接。 其他部署通过负载均衡器保留打开的入站连接。 此身份验证模式允许你通过远程桌面服务发布本地应用程序来提供更多类型的应用程序。 它还通过使用 Azure AD 应用程序代理降低了其部署的受攻击面。

## <a name="use-when"></a>何时使用

你需要通过预身份验证提供远程访问和保护你的远程桌面服务部署。

![体系结构图](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>系统组件

* **用户**：访问应用程序代理提供的 RDS。

* **Web 浏览器**：用户与之交互的组件，用于访问应用程序的外部 URL。

* **Azure AD**：对用户进行身份验证。 

* **应用程序代理服务**：充当反向代理，以将请求从用户转发到 RDS。 应用程序代理还可以强制实施任何条件性访问策略。 

* **远程桌面服务**：充当单个虚拟化应用程序的平台，提供安全的移动和远程桌面访问，使最终用户能够从云中运行应用程序和桌面。 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>通过 Azure AD 实现远程桌面网关服务

* [通过 Azure AD 应用程序代理发布远程桌面](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services) 

* [在 Azure AD 中通过应用程序代理添加用于远程访问的本地应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
