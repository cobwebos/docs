---
title: 使用 REST 将文件上传到 Azure 媒体服务 v3 帐户 |Microsoft Docs
description: 了解如何通过创建和上传资产将媒体内容加入媒体服务。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: a9fd38b857f513ff67ae2fbe4ccd0c6c9540967e
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779155"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>使用 REST 将文件上传到媒体服务帐户

在媒体服务中，可以将数字文件上传到与资产关联的 blob容器中。 [资产](https://docs.microsoft.com/rest/api/media/operations/asset)实体可以包含视频、音频、图片、缩略图集合、文本轨道和隐藏式字幕文件（以及这些文件的相关元数据）。 将文件上传到资产的容器中后，相关内容即安全地存储在云中供后续处理和流式传输。

本文介绍如何使用 REST 上传本地文件。

## <a name="prerequisites"></a>必备组件

若要完成本主题中所述的步骤，必须：

- 查看[资产概念](assets-concept.md)。
- [配置 Postman 以便进行 Azure 媒体服务 REST API 调用](media-rest-apis-with-postman.md)。
    
    确保遵循[获取 Azure AD 令牌](media-rest-apis-with-postman.md#get-azure-ad-token)主题中的最后一步。 

## <a name="create-an-asset"></a>创建资产

本部分介绍如何创建新的资产。

1. 选择“资产” -> “创建或更新资产”。
2. 按“发送”。

    ![创建资产](./media/upload-files/postman-create-asset.png)

可看到包含有关新创建资产的信息的**响应**。

## <a name="get-a-sas-url-with-read-write-permissions"></a>获取具有读写权限的 SAS URL 

本部分介绍如何获取通过所创建资产生成的 SAS URL。 所创建的 SAS URL 具有读写权限，可用于将数字文件上传到资产容器。

1. 选择“资产” -> “列出资产 URL”。
2. 按“发送”。

    ![上传文件](./media/upload-files/postman-create-sas-locator.png)

可看到包含有关资产 URL 的信息的**响应**。 复制第一个 URL 并使用它上传文件。

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>使用上传 URL 将文件上传到 Blob 存储

使用 Azure 存储 Api 或 Sdk （例如，[存储 REST API](../../storage/common/storage-rest-api-auth.md)或[.net sdk](../../storage/blobs/storage-quickstart-blobs-dotnet.md)）。

## <a name="next-steps"></a>后续步骤

[教程：根据 URL 编码远程文件并流式传输视频-REST](stream-files-tutorial-with-rest.md)
