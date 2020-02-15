---
title: 为 Azure API 管理实例配置自定义域名
titleSuffix: Azure API Management
description: 本主题介绍了如何为 Azure API 管理实例配置自定义域名。
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 72075d4eff336af625bbf6d62f1276d2997bfed4
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251187"
---
# <a name="configure-a-custom-domain-name"></a>配置自定义域名

创建 Azure API 管理服务实例时，Azure 会为其分配一个 `azure-api.net` 的子域（例如，`apim-service-name.azure-api.net`）。 但是，可以使用自己的自定义域名（如**contoso.com**）来公开 API 管理终结点。 本教程介绍如何将现有的自定义 DNS 名称映射到 API 管理实例公开的终结点。

> [!IMPORTANT]
> API 管理仅接受[主机标头](https://tools.ietf.org/html/rfc2616#section-14.23)值与默认域名或任何已配置的自定义域名匹配的请求。

> [!WARNING]
> 希望使用证书固定来改善其应用程序安全性的客户必须使用自定义域名和他们管理的证书，而不是默认证书。 固定默认证书的客户将会硬性依赖于他们不控制的证书的属性，这不是建议的做法。

## <a name="prerequisites"></a>必备条件

若要执行本文中所述的步骤，必须具有：

-   一个有效的 Azure 订阅。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API 管理实例。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。
-   由你或你的组织拥有的自定义域名。 本主题不提供有关如何购买自定义域名的说明。
-   托管在 DNS 服务器上的 CNAME 记录，用于将自定义域名映射到 API 管理实例的默认域名。 本主题不提供有关如何承载 CNAME 记录的说明。
-   必须具有有效的带有公钥和私钥 (.PFX) 的证书。 使用者或使用者备用名称（SAN）必须与域名匹配（这使得 API 管理实例可以安全地通过 SSL 公开 Url）。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>使用 Azure 门户设置自定义域名

1. 在[Azure 门户](https://portal.azure.com/)中导航到 API 管理实例。
1. 选择 "**自定义域**"。

    你可以为多个终结点分配自定义域名。 当前有以下终结点可用：

    - **网关**（默认值为： `<apim-service-name>.azure-api.net`）、
    - **门户**（默认值为：`<apim-service-name>.portal.azure-api.net`），
    - **管理**（默认值为：`<apim-service-name>.management.azure-api.net`），
    - **SCM**（默认值为：`<apim-service-name>.scm.azure-api.net`）。

    > [!NOTE]
    > 仅**网关**终结点可用于在消耗层中进行配置。
    > 可以更新所有终结点或者更新其中的一部分。 通常，客户更新**网关**（此 URL 用于调用通过 api 管理公开的 api）和**门户**（开发人员门户 URL）。
    > **管理**终结点和**SCM**终结点仅由 API 管理实例所有者在内部使用，因此不太频繁地分配自定义域名。
    > **高级**层支持为**网关**终结点设置多个主机名。

1. 选择要更新的终结点。
1. 在右侧窗口中，单击“自定义”。

    - 在“自定义域名”中，指定要使用的名称。 例如，`api.contoso.com` 。
    - 在**证书**中，从 Key Vault 中选择一个证书。 你还可以上传有效的。PFX 文件，如果证书受密码保护，则提供其**密码**。

    > [!NOTE]
    > 通配符域名（例如 `*.contoso.com` 在使用层除外的所有层中都受支持。

    > [!TIP]
    > 建议使用 Azure Key Vault 来管理证书，并将其设置为 autorotate。
    > 如果使用 Azure Key Vault 管理自定义域 SSL 证书，请确保将证书[作为_证书_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)（而不是_机密_）插入 Key Vault。
    >
    > 若要获取 SSL 证书，API 管理必须具有列表并获取包含证书的 Azure Key Vault 的机密权限。 使用时 Azure 门户会自动完成所有必需的配置步骤。 使用命令行工具或管理 API 时，必须手动授予这些权限。 此过程分为两个步骤。 首先，使用 API 管理实例上的 "托管标识" 页，确保已启用托管标识，并记下该页面上显示的主体 id。 其次，授予权限列表，并在包含证书的 Azure Key Vault 上获取对此主体 id 的机密权限。
    >
    > 如果将证书设置为 autorotate，则 API 管理将自动选取最新版本，而不会对服务造成任何停机（如果你的 API 管理层在开发人员层以外的所有层中都有 SLA-i. e）。

1. 单击“应用”。

    > [!NOTE]
    > 分配证书的过程可能需要 15 分钟或更久，这取决于部署规模。 开发人员 SKU 停机，基本和更高的 Sku 不会造成停机。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS 配置

为自定义域名配置 DNS 时，有两个选项：

-   配置一个 CNAME 记录，该记录指向配置的自定义域名的终结点。
-   配置指向你的 API 管理网关 IP 地址的 A 记录。

> [!NOTE]
> 尽管 API 管理实例 IP 地址是静态的，但在少数情况下，它可能会更改。 因此，建议在配置自定义域时使用 CNAME。 选择 DNS 配置方法时要考虑这一点。 有关详细信息，请参阅[API 管理常见问题解答](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)。

## <a name="next-steps"></a>后续步骤

[升级和缩放你的服务](upgrade-and-scale.md)
