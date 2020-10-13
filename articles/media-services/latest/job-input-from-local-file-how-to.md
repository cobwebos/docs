---
title: 从本地文件创建 Azure 媒体服务作业输入 | Microsoft Docs
description: 本文演示如何从本地文件创建 Azure 媒体服务作业输入。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 02aaae89f7544392765b46361b1617e0fd7dae8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296800"
---
# <a name="create-a-job-input-from-a-local-file"></a>从本地文件创建作业输入

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在媒体服务 v3 中提交作业来处理视频时，必须告知媒体服务查找输入视频的位置。 可将输入视频存储为媒体服务资产，这种情况下会基于文件（存储在本地或 Azure Blob 存储中）创建输入资产。 本主题介绍如何从本地文件创建作业输入。 有关完整示例，请参阅此 [GitHub 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)。

## <a name="prerequisites"></a>必备条件 

* [创建媒体服务帐户](./create-account-howto.md)。

## <a name="net-sample"></a>.NET 示例

以下代码演示如何创建输入资产并将其用作作业的输入。 此 CreateInputAsset 函数执行以下操作：

* 创建资产
* 获取资产的[存储中容器](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)的可写 [SAS URL](../../storage/common/storage-sas-overview.md)
* 使用 SAS URL 将文件上传到存储中的容器中

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

以下代码片段将创建一个输出资产（如果它尚不存在）：

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

以下代码片段将提交编码作业：

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>作业错误代码

请参阅[错误代码](/rest/api/media/jobs/get#joberrorcode)。

## <a name="next-steps"></a>后续步骤

[从 HTTPS URL 创建作业输入](job-input-from-http-how-to.md)。
