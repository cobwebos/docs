---
title: 访问 Azure 媒体服务 API - Azure CLI | Microsoft Docs
description: 按照本操作说明的步骤访问 Azure 媒体服务 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 8374e4c49012a2c49de41001be0fdb30f9151332
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617829"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>使用 Azure CLI 访问 Azure 媒体服务 API
 
若要使用 Azure AD 服务主体身份验证连接到 Azure 媒体服务 API，你的应用程序需要请求 Azure AD 令牌具有以下参数：

* Azure AD 租户终结点
* 媒体服务资源 URI
* REST 媒体服务的资源 URI
* Azure AD 应用程序值：客户端 ID 和客户端密码

有关详细说明，请参阅[访问媒体服务 v3 Api](media-services-apis-overview.md#accessing-the-azure-media-services-api)。

本文介绍如何使用 Azure CLI 创建 Azure AD 应用程序和服务主体，以及获取访问 Azure 媒体服务资源所需的值。

## <a name="prerequisites"></a>必备组件 

[创建媒体服务帐户](create-account-cli-how-to.md)。

请务必记住用于资源组名称和媒体服务帐户名称的值。
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>另请参阅

- [缩放媒体保留单位 - CLI](media-reserved-units-cli-how-to.md)
- [创建媒体服务帐户 - CLI](./scripts/cli-create-account.md) 
- [重置帐户凭据 - CLI](./scripts/cli-reset-account-credentials.md)
- [创建资产 - CLI](./scripts/cli-create-asset.md)
- [上传文件 - CLI](./scripts/cli-upload-file-asset.md)
- [创建转换 - CLI](./scripts/cli-create-transform.md)
- [创建作业 - CLI](./scripts/cli-create-jobs.md)
- [创建 EventGrid - CLI](./scripts/cli-create-event-grid.md)
- [发布资产 - CLI](./scripts/cli-publish-asset.md)
- [筛选器 - CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="next-steps"></a>后续步骤

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
