---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361552"
---
### <a name="identity-tier"></a>标识层 

通过 Microsoft-Oracle 合作关系，您可以在 Azure、OCI 和 Oracle 应用程序之间设置统一标识。 对于 JD Edwards 企业一 号或 PeopleSoft 应用程序套件，需要在 Azure AD 和 Oracle IDCS 之间设置单个登录的 Oracle HTTP 服务器 （OHS） 实例。

OHS 充当应用程序层的反向代理，这意味着所有对端应用程序的请求都通过它。 Oracle 访问管理器 WebGate 是一个 OHS Web 服务器插件，它拦截到最终应用程序的每个请求。 如果正在访问的资源受到保护（需要经过身份验证的会话），WebGate 将通过用户的浏览器启动具有标识云服务的 OIDC 身份验证流。 有关 OpenID 连接 WebGate 支持的流的详细信息，请参阅[Oracle 访问管理器文档](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html)。

通过此设置，已登录到 Azure AD 的用户可以通过 Oracle 标识云服务导航到 JD Edwards 企业一 号或 PeopleSoft 应用程序，而无需再次登录。 部署此解决方案的客户可享受单一登录的优势，包括一组凭据、改进的登录体验、改进的安全性和降低帮助台成本。

要了解有关为 JD Edwards 企业一 号或使用 Azure AD 为 PeopleSoft 设置单一登录设置单一登录名的更多信息，请参阅关联的[Oracle 白皮书](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)。