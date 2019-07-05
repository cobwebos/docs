---
title: 为 Azure API 管理实例配置自定义域名 | Microsoft Docs
description: 本主题介绍了如何为 Azure API 管理实例配置自定义域名。
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 07/01/2019
ms.author: apimpm
ms.openlocfilehash: 59b44dcc9ec3a1f7c274f426a19aa8ed2258db3e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509300"
---
# <a name="configure-a-custom-domain-name"></a>配置自定义域名

创建 Azure API 管理服务实例时，Azure 将其分配了 azure api.net 的子域 (例如， `apim-service-name.azure-api.net`)。 但是，可以公开使用自己的自定义域的名称，例如 API 管理终结点**contoso.com**。 本教程演示如何将现有的自定义 DNS 名称映射到 API 管理实例公开的终结点。

> [!WARNING]
> 想要使用证书固定改进其应用程序安全性的客户必须使用自定义域名和他们管理的证书，而不是使用默认证书。 改为固定默认证书的客户将硬依赖于他们不控制的证书属性，建议不要这样做。

## <a name="prerequisites"></a>必备组件

若要执行本文中所述的步骤，必须具有：

-   一个有效的 Azure 订阅。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API 管理实例。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。
-   一个由你拥有的自定义域名。 必须单独获取要使用的自定义域名并将其托管在 DNS 服务器上。 本主题没有说明如何托管自定义域名。
-   必须具有有效的带有公钥和私钥 (.PFX) 的证书。 使用者或使用者可选名称 (SAN) 必须匹配 （这会使 API 管理实例安全地公开 Url 通过 SSL） 的域名。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>使用 Azure 门户设置自定义域名

1. 导航到 API 管理实例中[Azure 门户](https://portal.azure.com/)。
1. 选择“自定义域和 SSL”  。

    可以为许多终结点分配自定义域名。 当前有以下终结点可用：

    - **代理**（默认值为：`<apim-service-name>.azure-api.net`），
    - **门户**（默认值为：`<apim-service-name>.portal.azure-api.net`），
    - **管理**（默认值为：`<apim-service-name>.management.azure-api.net`），
    - **SCM**（默认值为：`<apim-service-name>.scm.azure-api.net`）。

    > [!NOTE]
    > 可以更新所有终结点或者更新其中的一部分。 通常情况下，客户会更新**代理**（此 URL 用来调用通过 API 管理公开的 API）和**门户**（开发人员门户 URL）。 **管理**并**SCM**终结点由 API 管理实例所有者在内部使用，因此不经常分配自定义域名。 在大多数情况下仅单个自定义域名可以设置为给定的终结点。 但是， **Premium**层支持设置为多个主机名**代理**终结点。

1. 选择要更新的终结点。
1. 在右侧窗口中，单击“自定义”  。

    - 在“自定义域名”  中，指定要使用的名称。 例如，`api.contoso.com` 。 通配符域名 (例如， \*。 domain.com) 也受支持。
    - 在**证书**中，从密钥保管库中选择证书。 如果证书受密码保护，你还可以上传有效的 .PFX 文件并提供其**密码**。

    > [!TIP]
    > 我们建议使用 Azure 密钥保管库来管理证书并将其设置为 autorotate。
    > 如果使用 Azure 密钥保管库来管理自定义域 SSL 证书，请确保该证书[作为证书  ](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate)而不是机密  插入到密钥保管库中。
    >
    > 若要提取的 SSL 证书，API 管理必须具有列表获取机密权限包含证书的 Azure 密钥保管库。 使用 Azure 门户时将自动完成所有必需的配置步骤。 使用命令行工具或 API 管理时，必须手动授予这些权限。 此过程分为两个步骤。 首先，使用托管标识页上 API 管理实例以确保托管标识启用状态，请记下该页面显示的主体 id。 其次，为权限列表，并获取对包含该证书的 Azure 密钥保管库机密权限与此主体的 id。
    >
    > 如果证书设置为 autorotate，API 管理会选取而不会显示任何停机时间缩到该服务的最新版本 （如果你的 API 管理层没有 SLA，因此 i.e.除非开发人员层的所有层中）。

1. 单击“应用”。

    > [!NOTE]
    > 分配证书的过程可能需要 15 分钟或更久，这取决于部署规模。 开发人员 SKU 有故障时间，基本和更高版本的 SKU 没有故障时间。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>后续步骤

[升级和缩放你的服务](upgrade-and-scale.md)
