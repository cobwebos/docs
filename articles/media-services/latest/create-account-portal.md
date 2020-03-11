---
title: 使用 Azure 门户创建 Azure 媒体服务帐户
description: 本教程介绍了使用 Azure 门户创建 Azure 媒体服务帐户的步骤。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: c90278eccf889595378d3b6b07de2468910c660c
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080870"
---
# <a name="use-the-azure-portal-to-create-a-media-services-account"></a>使用 Azure 门户创建媒体服务帐户

Azure 门户提供了一种快速创建 Azure 媒体服务帐户的方法。 可以使用自己的帐户访问媒体服务，这些服务使你能够在 Azure 中存储、加密、编码、管理和流式传输媒体内容。

目前，可以使用[Azure 门户](https://portal.azure.com/)执行以下操作：

* 管理媒体服务 v3[实时事件](live-events-outputs-concept.md)， 
* 查看（不管理） v3[资产](assets-concept.md)， 
* [获取有关访问 api 的信息](access-api-portal.md)。 

对于所有其他管理任务（例如，[转换和作业](transforms-jobs-concept.md)和[内容保护](content-protection-overview.md)），请使用[REST API](https://aka.ms/ams-v3-rest-ref)、 [CLI](https://aka.ms/ams-v3-cli-ref)或其中一个受支持的[sdk](media-services-apis-overview.md#sdks)。

本文展示了如何使用 Azure 门户创建媒体服务帐户。

## <a name="prerequisites"></a>必备条件

要完成本教程，需要一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。 

## <a name="create-a-media-services-account"></a>创建媒体服务帐户

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 单击 " **+ 创建资源**" > **Media** > **media Services**"。
1. 在 "**创建媒体服务帐户**" 部分中，输入所需的值。
    
    | 名称 | 说明 |
    | ---|---|
    |**帐户名**|输入新的媒体服务帐户的名称。 媒体服务帐户名称由小写字母或数字构成（不含空格），长度为 3 到 24 个字符。|
    |**订阅**|如果有多个订阅，请从有权访问的 Azure 订阅的列表中选择一个订阅。|
    |**资源组**|选择新的或现有的资源。 资源组是共享生命周期、权限和策略的资源的集合。 在[此处](../../azure-resource-manager/management/overview.md#resource-groups)了解更多信息。|
    |**位置**|选择用于存储媒体服务帐户的媒体和元数据记录的地理区域。 此区域用于处理和流式播放媒体。 下拉列表中仅显示可用的媒体服务区域。 |
    |**存储帐户**|选择一个存储帐户，以便为媒体服务帐户中的媒体内容提供 blob 存储。 可以选择位于媒体服务帐户所在的地理区域内的现有存储帐户，也可以创建一个新的存储帐户。 将在同一区域内创建一个新的存储帐户。 适用于存储帐户名的规则对媒体服务帐户同样适用。<br/><br/>必须具有一个主存储帐户，并且可以拥有任意数量的与媒体服务帐户关联的辅助存储帐户。 你可以使用 Azure 门户来添加辅助存储帐户。 有关详细信息，请参阅 azure[媒体服务帐户的 Azure 存储帐户](storage-account-concept.md)。<br/><br/>媒体服务帐户和所有关联的存储帐户必须位于同一 Azure 订阅中。 强烈建议在媒体服务帐户所在的位置使用存储帐户，避免额外的延迟和数据出口成本。|
    
1. 选择“固定到仪表板”以查看帐户部署进度。
1. 单击窗体底部的“创建”。
   
    成功创建帐户后，会打开概述页。 在流式处理终结点表中，帐户的默认流式处理终结点处于 "**已停止**" 状态。 

    创建媒体服务帐户后，会将一个处于“已停止”状态的**默认**流式处理终结点添加到帐户。 若要开始流式传输内容并利用[动态打包](dynamic-packaging-overview.md)和[动态加密](content-protection-overview.md)，要从中流式传输内容的流式处理终结点必须处于 "**正在运行**" 状态。 

## <a name="next-steps"></a>后续步骤

如果计划以编程方式访问媒体服务 API，请参阅[使用 Azure AD 身份验证访问 Azure 媒体服务 api](access-api-portal.md)。

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

