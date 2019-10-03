---
title: 管理 Azure 媒体服务 v3 帐户 |Microsoft Docs
description: 若要开始管理、加密、编码、分析和流式处理 Azure 中的媒体内容，需要创建媒体服务帐户。 本文介绍如何管理 Azure 媒体服务 v3 帐户。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: fa9720c2c29af184016d2903e60520e701b4cf79
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670683"
---
# <a name="manage-azure-media-services-v3-accounts"></a>管理 Azure 媒体服务 v3 帐户

若要开始管理、加密、编码、分析和流式处理 Azure 中的媒体内容，需要创建媒体服务帐户。 创建媒体服务帐户时，需要提供 Azure 存储帐户资源的名称。 指定存储帐户会附加到媒体服务帐户。 媒体服务帐户和所有关联的存储帐户必须位于同一 Azure 订阅中。 有关详细信息，请参阅[存储帐户](storage-account-concept.md)。

## <a name="moving-a-media-services-account-between-subscriptions"></a>订阅之间移动媒体服务帐户 

如果你需要将媒体服务帐户移到新订阅，您需要首先移动包含到新的订阅的媒体服务帐户的整个资源组。 必须同时移动所有附加的资源：Azure 存储帐户，Azure CDN 配置文件，等等。有关详细信息，请参阅[将资源移到新资源组或订阅](../../azure-resource-manager/resource-group-move-resources.md)。 使用 Azure 中的任何资源，资源组移动可能需要一些时间才能完成。

> [!NOTE]
> 媒体服务 v3 支持多租户模型。

### <a name="considerations"></a>注意事项

* 迁移到其他订阅之前，请在帐户中创建的所有数据的备份。
* 您需要停止所有流式处理终结点和实时传送视频流的资源。 你的用户不能以资源组移动的时间里访问你的内容。 

> [!IMPORTANT]
> 已成功完成移动之前不会启动流式处理终结点。

### <a name="troubleshoot"></a>故障排除 

如果媒体服务帐户或相关联的 Azure 存储帐户"断开"在资源组移动操作后，请尝试轮换存储帐户密钥。 轮换存储帐户密钥不能解决的媒体服务帐户的"断开连接"状态，如果文件从新的支持请求"支持 + 故障排除"菜单中的媒体服务帐户。  

## <a name="next-steps"></a>后续步骤

[创建帐户](create-account-cli-quickstart.md)
