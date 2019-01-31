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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 43f9443e4b5cd700500bd9803f2737ed9e0aa633
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223151"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>使用 Azure CLI 访问 Azure 媒体服务 API
 
应使用 Azure AD 服务主体身份验证连接到 Azure 媒体服务 API。 应用程序需要请求具有以下参数 Azure AD 令牌：

* Azure AD 租户终结点
* 媒体服务资源 URI
* REST 媒体服务的资源 URI
* Azure AD 应用程序值：客户端 ID 和客户端密码

本文介绍如何使用 Azure CLI 创建 Azure AD 应用程序和服务主体，以及获取访问 Azure 媒体服务资源所需的值。

## <a name="prerequisites"></a>先决条件 

[创建媒体服务帐户](create-account-cli-how-to.md)。

请务必记住用于资源组名称和媒体服务帐户名称的值。
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>后续步骤

[流式传输文件](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>另请参阅

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
