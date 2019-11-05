---
title: 为自承载 Azure API 管理网关配置自定义域名 |Microsoft Docs
description: 本主题介绍为自承载 Azure API 管理网关配置自定义域名的步骤。
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 1df2cce04021c1cd14c356311df921dd1c0298e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513804"
---
# <a name="configure-a-custom-domain-name"></a>配置自定义域名

预配[AZURE API 管理网关](self-hosted-gateway-overview.md)时，不会为其分配主机名，必须由其 IP 地址引用。 本文介绍如何将现有的自定义 DNS 名称（也称为主机名）映射到自承载的网关。

> [!NOTE]
> 自承载网关功能处于预览阶段。 在预览期间，仅在开发人员和高级层提供自承载的网关，无额外费用。 开发人员层限制为单个自行托管的网关部署。

## <a name="prerequisites"></a>必备组件

若要执行本文中所述的步骤，必须具有：

-   一个有效的 Azure 订阅。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API 管理实例。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。
- 自承载的网关。 有关详细信息，请参阅[如何设置自托管网关](api-management-howto-provision-self-hosted-gateway.md)
-   由你或你的组织拥有的自定义域名。 本主题不会提供有关如何购买自定义域名的说明。
-   在 DNS 服务器上托管的 DNS 记录，用于将自定义域名映射到自承载网关的 IP 地址。 本主题不提供有关如何托管 DNS 记录的说明。
-   必须具有有效的带有公钥和私钥 (.PFX) 的证书。 使用者或使用者可选名称 (SAN) 必须与域名匹配（这使得 API 管理实例可以通过 SSL 安全地公开 URL）。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>将自定义域证书添加到 API 管理服务

1. 选择 "**安全**" 下的 "**证书**"。
2. 选择“+ 添加”。
3. 在 " **Id** " 字段中输入证书的资源名称。
4. 选择包含证书的文件（。PFX），方法是选择 "**证书**" 字段或旁边的文件夹图标。
5. 在 "**密码**" 字段中输入证书的密码。
6. 选择 "**创建**" 将证书添加到 API 管理服务。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>使用 Azure 门户为自承载网关设置自定义域名

1. 选择 "**设置**" 下的**网关**。
2. 选择要为其配置域名的自承载网关。
3. 选择 "**设置**" 下的 "**主机名**"。
4. 选择“+ 添加”。
5. 在 "**名称**" 字段中输入主机名的资源名称。
6. 在 "**主机名**" 字段中输入域名。
7. 从 "**证书**" 下拉列表中选择一个证书。
8. 如果通过此网关公开的任何 Api 使用客户端证书身份验证，请选择 "**协商客户端证书**" 复选框。
    > [!WARNING]
    > 此设置由为网关配置的所有域名共享。
9. 选择 "**添加**" 可将自定义域名分配给所选的自承载网关。

## <a name="next-steps"></a>后续步骤

[升级和缩放你的服务](upgrade-and-scale.md)
