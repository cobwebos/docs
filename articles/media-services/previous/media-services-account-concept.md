---
title: 管理 Azure 媒体服务 v2 帐户 |Microsoft Docs
description: 若要开始管理、加密、编码、分析和流式处理 Azure 中的媒体内容，需要创建媒体服务帐户。 本文介绍如何管理 Azure 媒体服务 v2 帐户。
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
ms.openlocfilehash: b4c19b1f502d079d7dfcc1edef4674d21f78ac3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622038"
---
# <a name="manage-azure-media-services-v2-accounts"></a>管理 Azure 媒体服务 v2 帐户

若要开始管理、加密、编码、分析和流式处理 Azure 中的媒体内容，需要创建媒体服务帐户。 创建媒体服务帐户时，需要提供 Azure 存储帐户资源的名称。 指定存储帐户会附加到媒体服务帐户。 媒体服务帐户和所有关联的存储帐户必须位于同一 Azure 订阅中。  

## <a name="moving-a-media-services-account-between-subscriptions"></a>订阅之间移动媒体服务帐户 

如果你需要将媒体服务帐户移到新订阅，您需要首先移动包含到新的订阅的媒体服务帐户的整个资源组。 必须同时移动所有附加的资源：Azure 存储帐户，Azure CDN 配置文件，等等。有关详细信息，请参阅[将资源移到新资源组或订阅](../../azure-resource-manager/resource-group-move-resources.md)。 使用 Azure 中的任何资源，资源组移动可能需要一些时间才能完成。

媒体服务 v2 不支持多租户模型。 如果需要媒体服务帐户移动到新租户中的订阅，请在新的租户中创建新的 Azure Active Directory (Azure AD) 应用程序。 然后将你的帐户移到新的租户中订阅。 租户移动操作完成后，可以开始使用新租户中的 Azure AD 应用程序访问媒体服务帐户使用 v2 Api。 

> [!IMPORTANT]
> 需要重置[Azure AD 身份验证](media-services-portal-get-started-with-aad.md)访问媒体服务 v2 API 的信息。  
### <a name="considerations"></a>注意事项

* 迁移到其他订阅之前，请在帐户中创建的所有数据的备份。
* 您需要停止所有流式处理终结点和实时传送视频流的资源。 你的用户不能以资源组移动的时间里访问你的内容。 

> [!IMPORTANT]
> 已成功完成移动之前不会启动流式处理终结点。

### <a name="troubleshoot"></a>故障排除 

如果媒体服务帐户或相关联的 Azure 存储帐户"断开"在资源组移动操作后，请尝试轮换存储帐户密钥。 轮换存储帐户密钥不能解决的媒体服务帐户的"断开连接"状态，如果文件从新的支持请求"支持 + 故障排除"菜单中的媒体服务帐户。  
 
## <a name="next-steps"></a>后续步骤

[创建帐户](media-services-portal-create-account.md)
