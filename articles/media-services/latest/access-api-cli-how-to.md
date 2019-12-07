---
title: 访问 Azure 媒体服务 API - Azure CLI | Microsoft Docs
description: 请按照本操作指南文章中的步骤访问 Azure 媒体服务 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d66b3e1b6ed2c8eef9f5cd21c0657648ad550ebe
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896158"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>使用 Azure CLI 访问 Azure 媒体服务 API
 
若要使用 Azure AD 服务主体身份验证连接到 Azure 媒体服务 API，你的应用程序需要请求具有以下参数的 Azure AD 令牌：

* Azure AD 租户终结点
* 媒体服务资源 URI
* REST 媒体服务的资源 URI
* Azure AD 应用程序值：客户端 ID 和客户端密码

有关详细说明，请参阅[访问媒体服务 V3 api](media-services-apis-overview.md#accessing-the-azure-media-services-api)。

本文介绍如何使用 Azure CLI 创建 Azure AD 应用程序和服务主体，以及获取访问 Azure 媒体服务资源所需的值。

## <a name="prerequisites"></a>必备组件 

[创建媒体服务帐户](create-account-cli-how-to.md)。

请务必记住用于资源组名称和媒体服务帐户名称的值。
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>另请参阅

- [缩放媒体预留单位 - CLI](media-reserved-units-cli-how-to.md)
- [创建媒体服务帐户 - CLI](create-account-cli-how-to.md) 
- [重置帐户凭据 - CLI](cli-reset-account-credentials.md)
- [创建资产 - CLI](cli-create-asset.md)
- [上传文件 - CLI](cli-upload-file-asset.md)
- [创建转换 - CLI](cli-create-transform.md)
- [使用自定义转换进行编码-CLI](custom-preset-cli-howto.md)
- [创建作业 - CLI](cli-create-jobs.md)
- [创建 EventGrid - CLI](job-state-events-cli-how-to.md)
- [发布资产 - CLI](cli-publish-asset.md)
- [筛选器 - CLI](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>后续步骤

要从中流式传输内容的流式处理终结点必须处于 "正在运行" 状态。 以下 CLI 命令将启动默认流式处理终结点：

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

