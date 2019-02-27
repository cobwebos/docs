---
title: 从 HTTPS URL 创建 Azure 媒体服务作业输入 | Microsoft Docs
description: 本主题介绍如何从 HTTP URL 创建作业输入。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: 5e301a671551ee65e8dc56ca6f86e273fe2f6241
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417170"
---
# <a name="create-a-job-input-from-an-https-url"></a>从 HTTPS URL 创建作业输入

在媒体服务 v3 中提交作业来处理视频时，必须告知媒体服务查找输入视频的位置。 其中一个选项是指定 HTTPS URL 作为作业输入（如本示例所示）。 请注意，目前，AMS v3 不支持基于 HTTPS URL 的块传输编码。 有关完整示例，请参阅此 [GitHub 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)。

## <a name="net-sample"></a>.NET 示例

以下代码说明了如何使用 HTTPS URL 输入创建作业。

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>作业错误代码

请参阅[错误代码](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)。

## <a name="next-steps"></a>后续步骤

[从本地文件创建作业输入](job-input-from-local-file-how-to.md)。
