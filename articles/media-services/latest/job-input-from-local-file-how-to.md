---
title: 从本地文件创建 Azure 媒体服务作业输入 | Microsoft Docs
description: 本主题介绍如何从本地文件创建作业输入。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 9b366cca37e562e229e7d139426fc0b24978e366
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53412957"
---
# <a name="create-a-job-input-from-a-local-file"></a>从本地文件创建作业输入

在媒体服务 v3 中提交作业来处理视频时，必须告知媒体服务查找输入视频的位置。 可将输入视频存储为媒体服务资产，这种情况下会基于文件（存储在本地或 Azure Blob 存储中）创建输入资产。 本主题介绍如何从本地文件创建作业输入。 有关完整示例，请参阅此 [GitHub 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)。

## <a name="net-sample"></a>.NET 示例

以下代码演示如何创建输入资产并将其用作作业的输入。 此 CreateInputAsset 函数执行以下操作：

* 创建资产
* 获取资产的[存储中容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)的可写 [SAS URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
* 使用 SAS URL 将文件上传到存储中的容器中

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>后续步骤

[从 HTTPS URL 创建作业输入](job-input-from-http-how-to.md)。
