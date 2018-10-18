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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: bac11640f5256223c1053f03da42c763508af98f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377372"
---
# <a name="create-a-job-input-from-an-https-url"></a>从 HTTPS URL 创建作业输入

在媒体服务 v3 中提交作业来处理视频时，必须告知媒体服务查找输入视频的位置。 其中一个选项是指定 HTTP URL 作为作业输入（如本例中所示）。 请注意，目前，AMS v3 不支持基于 HTTPS URL 的块传输编码。 有关完整示例，请参阅此 [github 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)。

## <a name="net-sample"></a>.NET 示例

以下代码说明了如何使用 HTTPS URL 输入创建作业。

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>后续步骤

[从本地文件创建作业输入](job-input-from-local-file-how-to.md)。
