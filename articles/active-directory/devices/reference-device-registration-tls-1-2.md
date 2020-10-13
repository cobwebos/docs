---
title: TLS 1.2 强制-Azure Active Directory 注册服务
description: 删除 Azure AD 设备注册服务的 TLS 1.0 和1.1 支持
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb8c6c64e0a68f5176c4eb0c0177c5220394695
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89268751"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>为 Azure AD 注册服务强制执行 TLS 1。2

Azure AD) 设备注册服务的 Azure Active Directory (用于使用设备标识将设备连接到云。 Azure AD 设备注册服务目前支持使用传输层安全性 (TLS) 1.2 与 Azure 通信。 为了确保安全性和最佳类加密，Microsoft 建议禁用 TLS 1.0 和1.1。 本文档将提供有关如何确保用于完成注册和与 Azure AD 设备注册服务通信的计算机使用 TLS 1.2 的信息。

TLS 协议版本1.2 是一种加密协议，旨在提供安全的通信。 TLS 协议主要目的在于提供隐私和数据完整性。 TLS 已经历多个迭代，版本1.2 是在 [RFC 5246 (外部链接) ](https://tools.ietf.org/html/rfc5246)中定义的。

当前的连接分析只显示 TLS 1.1 和1.0 的使用情况，但我们会提供此信息，以便你可以根据需要更新任何受影响的客户端或服务器，然后才支持 TLS 1.1 和1.0 结束。 如果将任何本地基础结构用于混合方案或 Active Directory 联合身份验证服务 (AD FS) ，请确保该基础结构可以同时支持使用 TLS 1.2 的入站连接和出站连接。

## <a name="update-windows-servers"></a>更新 Windows 服务器

对于使用 Azure AD 设备注册服务或充当代理的 Windows server，请使用以下步骤确保启用 TLS 1.2：

> [!IMPORTANT]
> 更新注册表之后，必须重新启动 Windows 服务器，更改才能生效。

### <a name="enable-tls-12"></a>启用 TLS 1.2

确保按如下所示配置以下注册表字符串：

- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 客户端
  - "DisabledByDefault" = dword：00000000
  - "Enabled" = dword：00000001
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ 服务器
  - "DisabledByDefault" = dword：00000000
  - "Enabled" = dword：00000001
- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\. NETFramework\v4.0.30319
  - "SchUseStrongCrypto"=dword:00000001

## <a name="update-non-windows-proxies"></a>更新非 Windows 代理

在设备与 Azure AD 设备注册服务之间充当代理的任何计算机都必须确保启用 TLS 1.2。 遵循供应商的指南以确保支持。

## <a name="update-ad-fs-servers"></a>更新 AD FS 服务器

任何用于与 Azure AD 设备注册服务通信的 AD FS 服务器都必须确保启用 TLS 1.2。 有关如何启用/验证此配置的信息，请参阅 [管理 SSL/TLS 协议和密码套件 AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) 。

## <a name="client-updates"></a>客户端更新

由于所有客户端服务器和浏览器-服务器组合都必须使用 TLS 1.2 连接到 Azure AD 设备注册服务，因此可能需要更新这些设备。

已知以下客户端无法支持 TLS 1.2。 更新客户端以确保不间断的访问。

- Android 版本4.3 及更早版本
- Firefox 版本5.0 及更早版本
- Windows 7 和更早版本上的 Internet Explorer 版本8-10
- Windows Phone 8.0 上的 Internet Explorer 10
- OS X 10.8.4 和更早版本上的 Safari 版本6.0。4

## <a name="next-steps"></a>后续步骤

[TLS/SSL 概述 (Schannel SSP) ](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)