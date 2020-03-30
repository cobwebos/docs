---
title: Azure AD 连接 Azure AD 云配置的先决条件
description: 本文介绍了云配置所需的先决条件和硬件要求。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45648170f69d513b15e79cdd76f56e66bbc88bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332088"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Azure AD 连接云配置的先决条件
本文提供有关如何选择和使用 Azure 活动目录 （Azure AD） 将云资源调配作为标识解决方案的指导。



## <a name="cloud-provisioning-agent-requirements"></a>云预配代理要求
使用 Azure AD 连接云配置需要以下内容：
    
- 不是来宾用户的 Azure AD 租户的全局管理员帐户。
- 具有 Windows 2012 R2 或更高版本的预配代理的本地服务器。
- 本地防火墙配置。

>[!NOTE]
>预配代理目前只能安装在英语服务器上。 在非英语服务器上安装英语语言包不是有效的解决方法，将导致代理无法安装。 

文档的其余部分提供这些先决条件的分步说明。

### <a name="in-the-azure-active-directory-admin-center"></a>在 Azure Active Directory 管理中心中

1. 在 Azure AD 租户中创建仅限云的全局管理员帐户。 这样，如果本地服务失败或不可用，则可以管理租户的配置。 了解如何[添加仅云全局管理员帐户](../active-directory-users-create-azure-portal.md)。 完成此步骤对于确保不会锁定租户至关重要。
1. 在 Azure AD 租户中添加一个或多个[自定义域名](../active-directory-domains-add-azure-portal.md)。 用户可以使用其中一个域名登录。

### <a name="in-your-directory-in-active-directory"></a>在活动目录中的目录中

运行[IdFix 工具](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)以准备用于同步的目录属性。

### <a name="in-your-on-premises-environment"></a>在本地环境中

1. 使用至少 4 GB RAM 和 .NET 4.7.1+ 运行时，标识运行 Windows Server 2012 R2 或更高域的主机服务器。

1. 如果服务器和 Azure AD 之间存在防火墙，请配置以下项目：
   - 确保代理可以通过以下端口向 Azure AD 发出出站请求：**

        | 端口号 | 用途 |
        | --- | --- |
        | **80** | 在验证 TLS/SSL 证书时下载证书吊销列表 （CRL）。  |
        | **443** | 处理与服务的所有出站通信。 |
        | **8080**（可选） | 如果端口 443 不可用，代理将每隔 10 分钟通过端口 8080 报告其状态。 此状态显示在 Azure AD 门户中。 |
     
   - 如果防火墙根据原始用户强制实施规则，请打开这些端口以允许来自作为网络服务运行的 Windows 服务的流量。
   - 如果您的防火墙或代理允许您指定安全后缀，请向\*.msappproxy.net 和\*.servicebus.windows.net 添加连接。 否则，请允许访问每周更新的 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。
   - 代理需要访问 login.windows.net 和 login.microsoftonline.com 来完成初始注册。 另外，还请为这些 URL 打开防火墙。
   - 对于证书验证，取消阻止以下 URL：mscrl.microsoft.com:80、crl.microsoft.com:80、ocsp.msocsp.com:80 和 www\.microsoft.com:80。 这些 URL 用于与其他 Microsoft 产品的证书验证，因此您可能已经解除了这些 URL 的阻止功能。

### <a name="verify-the-port"></a>验证端口
要验证 Azure 正在侦听端口 443，并且代理是否可以与它通信，请使用以下 URL：

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

此测试验证代理是否可以通过端口 443 与 Azure 通信。 打开浏览器，然后从安装代理的服务器转到以前的 URL。

![端口可到达性验证](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>其他需求
- [微软 .NET 框架 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS 要求

>[!NOTE]
>传输层安全 （TLS） 是一种提供安全通信的协议。 更改 TLS 设置会影响整个林。 有关详细信息，请参阅[更新以启用 TLS 1.1 和 TLS 1.2 作为 Windows 中的 WinHTTP 中的默认安全协议](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)。

承载 Azure AD Connect 云预配代理的 Windows 服务器在安装 TLS 1.2 之前必须启用 TLS 1.2。

要启用 TLS 1.2，请按照以下步骤操作。

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

