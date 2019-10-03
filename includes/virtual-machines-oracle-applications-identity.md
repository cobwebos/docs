---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361552"
---
### <a name="identity-tier"></a>标识层 

利用 Microsoft Oracle 合作关系, 你可以在 Azure、OCI 和 Oracle 应用程序中设置统一标识。 对于 JD Edwards EnterpriseOne 或 PeopleSoft 应用程序套件, 需要 Oracle HTTP 服务器的实例 (OHS) 才能在 Azure AD 和 Oracle IDCS 之间设置单一登录。

OHS 充当应用层的反向代理, 这意味着对最终应用程序的所有请求都将通过它。 Oracle Access Manager WebGate 是一个 OHS web 服务器插件, 用于截获转到最终应用程序的每个请求。 如果要访问的资源受到保护 (需要经过身份验证的会话), WebGate 将通过用户的浏览器通过标识云服务启动 OIDC authentication flow。 有关 OpenID Connect WebGate 支持的流的详细信息, 请参阅[Oracle Access Manager 文档](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html)。

使用此设置, 已登录到 Azure AD 的用户可以导航到 JD Edwards EnterpriseOne 或 PeopleSoft 应用程序, 而无需通过 Oracle 标识云服务再次登录。 部署此解决方案的客户可享受单一登录的好处, 包括一组凭据、改进的登录体验、提高的安全性以及降低的技术支持成本。

若要详细了解如何设置 JD Edwards EnterpriseOne 或 PeopleSoft Azure AD 的单一登录, 请参阅关联的[Oracle 白皮书](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)。