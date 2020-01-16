---
title: 管理 Azure 媒体服务 v2 帐户 |Microsoft Docs
description: 若要开始管理、加密、编码、分析和流式处理 Azure 中的媒体内容，需要创建媒体服务帐户。 本文介绍如何管理 Azure Media Services v2 帐户。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 09a5f004570430fafe5c86f4f8ae048f2d1fe4c4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981949"
---
# <a name="manage-azure-media-services-v2-accounts"></a>管理 Azure 媒体服务 v2 帐户

若要开始管理、加密、编码、分析和流式处理 Azure 中的媒体内容，需要创建媒体服务帐户。 创建媒体服务帐户时，需要提供 Azure 存储帐户资源的名称。 指定存储帐户会附加到媒体服务帐户。 媒体服务帐户和所有关联的存储帐户必须位于同一 Azure 订阅中。  

## <a name="moving-a-media-services-account-between-subscriptions"></a>在订阅之间移动媒体服务帐户 

如果需要将媒体服务帐户移到新订阅，需要先将包含媒体服务帐户的整个资源组移动到新订阅。 必须移动所有附加的资源： Azure 存储帐户、Azure CDN 配置文件等。有关详细信息，请参阅[将资源移到新的资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 与 Azure 中的任何资源一样，资源组移动可能需要一些时间才能完成。

媒体服务 v2 不支持多租户模型。 如果需要将媒体服务帐户移到新租户中的订阅，请在新租户中创建新的 Azure Active Directory （Azure AD）应用程序。 然后，将你的帐户移到新租户中的订阅。 租户移动完成后，可以开始使用新租户中的 Azure AD 应用程序，使用 v2 Api 访问 Media Services 帐户。 

> [!IMPORTANT]
> 需要重置[Azure AD 身份验证](media-services-portal-get-started-with-aad.md)信息才能访问媒体服务 v2 API。  
### <a name="considerations"></a>注意事项

* 在迁移到其他订阅之前，创建帐户中所有数据的备份。
* 需要停止所有流式处理终结点和实时流资源。 在资源组移动期间，你的用户将无法访问你的内容。 

> [!IMPORTANT]
> 在移动成功完成之前，请勿启动流式处理终结点。

### <a name="troubleshoot"></a>故障排除 

如果媒体服务帐户或关联的 Azure 存储帐户在资源组移动后变为 "断开连接" 状态，请尝试轮换存储帐户密钥。 如果轮换存储帐户密钥不能解决媒体服务帐户的 "已断开连接" 状态，请通过媒体服务帐户中的 "支持 + 疑难解答" 菜单来提供新的支持请求。  
 
## <a name="next-steps"></a>后续步骤

[创建帐户](media-services-portal-create-account.md)
