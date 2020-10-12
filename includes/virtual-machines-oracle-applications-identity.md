---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 2b28cc742bb981e0db535d39cb0c7fbf027470ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83673758"
---
### <a name="identity-tier"></a>标识层 

通过 Microsoft-Oracle 合作关系，你可以跨 Azure、OCI 和 Oracle 应用程序设置统一的标识。 对于 JD Edwards EnterpriseOne 或 PeopleSoft 应用程序套件，需要使用 Oracle HTTP Server (OHS) 的实例才能在 Azure AD 和 Oracle IDCS 之间设置单一登录。

OHS 充当应用层的反向代理，这意味着对最终应用程序的所有请求都将通过它。 Oracle Access Manager WebGate 是 OHS Web 服务器插件，用于拦截发送到最终应用程序的每个请求。 如果正在访问的资源受到保护（需要经过身份验证的会话），则 WebGate 会通过用户的浏览器使用身份云服务启动 OIDC 身份验证流。 有关 OpenID Connect WebGate 支持的流的详细信息，请参阅 [Oracle Access Manager 文档](https://docs.oracle.com/cd/E52734_01/oam/AIAAG/GUID-1E927D1B-FB83-425B-8768-85DB441821A4.htm#AIAAG7327)。

使用此设置，已登录到 Azure AD 的用户可以通过 Oracle 身份云服务导航到 JD Edwards EnterpriseOne 或 PeopleSoft 应用程序，而无需再次登录。 部署此解决方案的客户可享受单一登录的好处，包括一组凭据、改进的登录体验、更高的安全性以及降低的技术支持成本。

要详细了解如何使用 Azure AD 为 JD Edwards EnterpriseOne 或 PeopleSoft 设置单一登录，请参阅相关的 [Oracle 白皮书](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)。