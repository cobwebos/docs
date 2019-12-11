---
title: 页面上的链接对应用程序代理应用程序不起作用
description: 如何对与 Azure AD 集成的应用程序代理应用程序上断开的链接问题进行故障排除
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 268775d125e783b0c640e565c5f63e416cb197fd
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996946"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>页面上的链接对应用程序代理应用程序不起作用

本文可帮助诊断 Azure Active Directory 应用程序代理应用程序中的链接无法正常工作的原因。

## <a name="overview"></a>概述 
发布应用程序代理应用后，应用程序中默认可以工作的链接仅是指向已发布根 URL 内包含的目标的链接。 应用程序内的链接无法正常工作，应用程序的内部 URL 可能未包括应用程序内链接的所有目标。

**为何发生这种情况？** 单击应用程序中的某个链接后，应用程序代理尝试将此 URL 解析为同一应用程序内的内部 URL 或外部可用的 URL。 如果链接指向不在同一应用程序内的内部 URL，它不属于这些存储桶中的任意一个，从而导致出现“未找到”错误。

## <a name="ways-you-can-resolve-broken-links"></a>解决断开的链接的方法

可通过三种方法来解决此问题。 按复杂性递增的顺序列出了以下选项。

1.  请确保内部 URL 是包含应用程序所有相关链接的根 URL。 这会允许所有链接解析为同一应用程序内发布的内容。

    如果更改了内部 URL，但不想更改用户的登陆页面，则将主页 URL 更改为以前发布的内部 URL。 若要完成此操作，请转到 "Azure Active Directory"-&gt; 应用注册-&gt; 选择 "应用程序&gt; 品牌"。 在 "品牌" 部分中，你将看到 "主页 URL" 字段，你可以将其调整为所需的登陆页面。 如果仍在使用旧的应用注册体验，"属性" 选项卡将显示 "主页 URL" 详细信息。 
    
    > [!IMPORTANT]
    > 若要进行上述更改，你需要在 Azure AD 中修改应用程序对象的权限。需要向用户分配 "[应用程序管理员](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/roles-delegate-app-roles#assign-built-in-application-admin-roles)" 角色，以便向用户授予 Azure AD 的应用程序 modificaion 权限。 
    >

2.  如果应用程序使用完全限定的域名 (FQDN)，则使用[自定义域](application-proxy-configure-custom-domain.md)发布应用程序。 此功能允许相同 URL 同时在内部和外部使用。

    此选项确保应用程序内的链接可通过应用程序代理从外部访问，因为应用程序内指向内部 URL 的链接也可从外部识别。 所有链接仍需要属于已发布的应用程序。 但使用此选项，链接不需要属于同一应用程序，而是可以属于多个应用程序。

3.  如果这些选项都不可行，则有多个选项可用于启用内联链接转换。 这些选项包括使用 Intune Managed Browser、“我的应用”扩展，或者使用你的应用程序上的链接转换设置。 若要详细了解这些选项中的每一个以及如何启用它们，请参阅[为使用 Azure AD 应用程序代理发布的应用重定向硬编码的链接](application-proxy-configure-hard-coded-link-translation.md)。

## <a name="next-steps"></a>后续步骤
[使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)

