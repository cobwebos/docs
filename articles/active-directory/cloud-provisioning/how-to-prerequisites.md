---
title: Azure AD Connect 云预配的先决条件 Azure AD
description: 本主题介绍云预配的先决条件和硬件要求。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 155edf72a60e079a609853e953e3cf66024cc83c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794245"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect 云预配的先决条件
本主题提供有关选择和使用 Azure AD Connect 云预配作为标识解决方案的指南。



## <a name="cloud-provisioning-agent-requirements"></a>云设置代理要求
你将需要以下内容才能使用 Azure AD Connect 云预配：
    
- Azure AD 租户的全局管理员帐户
- 使用 Windows 2012 R2 或更高版本的预配代理的本地服务器
- 本地防火墙配置

本文档的其余部分将提供有关这些先决条件的分步说明。

### <a name="in-the-azure-active-directory-admin-center"></a>在 Azure Active Directory 管理中心中

1. 在 Azure AD 租户中创建仅限云的全局管理员帐户。 这样一来，就可以在本地服务出现故障或不可用时管理租户的配置。 了解如何[添加仅限云的全局管理员帐户](../active-directory-users-create-azure-portal.md)。 完成此步骤至关重要，可确保自己不被锁定在租户外部。
2. 在 Azure AD 租户中添加一个或多个[自定义域名](../active-directory-domains-add-azure-portal.md)。 用户可以使用其中一个域名登录。

### <a name="in-your-on-premises-environment"></a>在本地环境中

1. 标识已加入域且运行 Windows Server 2012 R2 或更高版本且至少具有 4 GB RAM 和 .NET 4.7.1 + 运行时的主机服务器 

2. 如果服务器和 Azure AD 之间存在防火墙，请配置以下项：
   - 确保代理可以通过以下端口发出到 Azure AD 的*出站*请求：

     | 端口号 | 用途 |
     | --- | --- |
     | **80** | 下载证书吊销列表 (Crl) 的同时验证 SSL 证书 |
     | **443** | 处理与服务的所有出站通信 |
     | **8080**（可选） | 如果443端口8080不可用，代理将通过端口每十分钟报告其状态。 此状态显示在 Azure AD 门户上。 用户登录不会使用端口 8080。 |
     
     如果防火墙根据原始用户强制实施规则，请打开这些端口以允许来自作为网络服务运行的 Windows 服务的流量。
   - 如果你的防火墙或代理允许你指定安全后缀，则将连接添加到 **\*msappproxy.net**和 **\*。** 否则，请允许访问每周更新的 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。
   - 代理需要访问**login.windows.net**和**login.microsoftonline.com** ，以便进行初始注册。 另外，还请为这些 URL 打开防火墙。
   - 对于证书验证，请取消阻止以下 Url： **mscrl.microsoft.com:80**、 **crl.microsoft.com:80**、 **ocsp.msocsp.com:80**和**www\.microsoft.com:80**。 由于这些 URL 与其他 Microsoft 产品一起用于证书验证，因此可能已取消阻止这些 URL。

### <a name="verify-the-port"></a>验证端口
若要验证 Azure 是否正在侦听端口443，以及代理是否可以与其通信，可以使用以下内容：

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此测试将验证代理是否能够通过端口443与 Azure 通信。  打开浏览器，并从安装了代理的服务器导航到上述 URL。
![服务](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>其他要求
- [Microsoft .NET 框架4.7。1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS 要求

>[!NOTE]
>传输层安全性（TLS）是提供安全通信的协议。  更改 TLS 设置会影响整个林。  有关详细信息，请参阅[更新以在 Windows 中的 WinHTTP 中启用 tls 1.1 和 tls 1.2 作为默认安全协议](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)

将承载 Azure AD Connect 云预配代理的 Windows server 需要在安装后启用 TLS 1.2。

若要启用 TLS 1.2，请执行以下操作：

1. 设置以下注册表项：
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. 重新启动服务器。


## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)

