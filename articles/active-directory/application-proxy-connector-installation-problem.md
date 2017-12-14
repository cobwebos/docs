---
title: "安装应用程序代理程序连接器时出现问题 | Microsoft Docs"
description: "如何排除在安装应用程序代理程序连接器时可能遇到的问题"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: a02f6cf0923bbe7083ba29fac5f59bcce9f5ab44
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>安装应用程序代理程序连接器时出现问题

Microsoft AAD 应用程序代理连接器是一个内部域组件，该组件使用出站连接来建立从云可用终结点到内部域的连接性。

## <a name="general-problem-areas-with-connector-installation"></a>连接器安装的常规问题区域

连接器安装失败时，根本原因通常是以下方面之一：

1.  **连接性** – 若要成功完成安装，新连接器需要注册并建立未来的信任属性。 此操作可以通过连接到 AAD 应用程序代理云服务来完成。

2.  **信任建立** – 新连接器创建自签名证书并注册到云服务。

3.  **管理员的身份验证** – 在安装过程中，用户必须提供管理员凭据才能完成连接器安装。

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>验证与云应用程序代理服务和 Microsoft 登录页的连接性

**目的︰**验证连接器计算机是否可以连接到 AAD 应用程序代理注册终结点以及 Microsoft 登录页。

1.  打开浏览器，并转到以下网页：<https://aadap-portcheck.connectorporttest.msappproxy.net>，验证与美国中部和美国东部数据中心（使用端口 9090 和 9091）的连接是否正常。

2.  如果任何端口都没有成功连接（即没有绿色复选标记），则验证防火墙或后端代理是否已正确定义 \*.msappproxy.net（使用端口 9090 和 9091）。

3.  打开浏览器（单独选项卡）并转到以下网页：<https://login.microsoftonline.com>，确保可登录到该页。

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>验证计算机和后端组件是否支持应用程序代理信任证书

**目的：**验证连接器计算机、后端代理和防火墙是否可以支持连接器为未来信任所创建的证书。

>[!NOTE]
>连接器尝试创建 TLS1.2 支持的 SHA512 证书。 如果计算机或后端防火墙和代理不支持 TLS1.2，安装会失败。
>
>

**若要解决该问题：**

1.  验证计算机是否支持 TLS1.2 – 2012 R2 后的所有 Windows 版本都应支持 TLS 1.2。 如果连接器计算机来自 2012 R2 版本或更早的版本，确保计算机上安装了以下知识库：<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  联系网络管理员并要求验证后端代理和防火墙不会阻止 SHA512 传出流量。

## <a name="verify-admin-is-used-to-install-the-connector"></a>验证“admin”是否可用于安装连接器

**目的：**验证尝试安装连接器的用户是否是具有正确凭据的管理员。 目前，用户必须是全局管理员才能成功安装。

**若要验证凭据是否正确：**

连接到 <https://login.microsoftonline.com> 并使用相同的凭据。 确保登录成功。 可以通过转到“Azure Active Directory” -&gt;“用户和组” -&gt;“所有用户”来检查用户角色。 

选择用户帐户，并在生成的菜单中选择“目录角色”。 验证所选角色是否为“全局管理员”。 如果按这些步骤操作无法访问任何页，则不是全局管理员。

## <a name="next-steps"></a>后续步骤
[了解 Azure AD 应用程序代理连接器](application-proxy-understand-connectors.md)
