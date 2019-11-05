---
title: 可伸缩性和性能-Personalizer
titleSuffix: Azure Cognitive Services
description: 对于高性能和高流量的网站和应用程序，个性化体验创建服务在可伸缩性和性能方面需要考虑两个主要因素：延迟和训练吞吐量。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: 5ac9a870cb05328f040febd0f8161a97f0982e09
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490775"
---
# <a name="scalability-and-performance"></a>可伸缩性和性能

对于高性能和高流量的网站和应用程序，个性化体验创建服务在可伸缩性和性能方面需要考虑两个主要因素：

* 在调用排名 API 时保持低延迟
* 确保训练吞吐量与事件输入保持同步

个性化设置可以快速返回排名，其中的大多数调用持续时间都专用于通过 REST API 进行通信。 Azure 将自动调整快速响应请求的能力。

##  <a name="low-latency-scenarios"></a>低延迟方案

某些应用程序在返回排名时需要较低的延迟。 低延迟是必需的：

* 以防止用户在显示排名内容之前等待很长时间。
* 以帮助正在经历极端流量的服务器避免占用稀缺的计算时间和网络连接。


## <a name="scalability-and-training-throughput"></a>可伸缩性和训练吞吐量

个性化体验创建服务的工作方式是更新一个模型，该模型基于个性化体验创建服务在排名和奖励 API 之后异步发送的消息进行重新训练。 应用程序使用 Azure EventHub 发送这些消息。

 大多数应用程序不太可能达到个性化体验创建服务的最大加入和训练吞吐量。 虽然达到这个最大值不会降低应用程序的速度，但这意味着事件中心队列在内部填充的速度要快于清理它们的速度。

## <a name="how-to-estimate-your-throughput-requirements"></a>如何估计吞吐量需求

* 估计每个排名事件的平均字节数，加上上下文和操作 JSON 文档的长度。
* 用 20MB/秒除以这个估计的平均字节数。

例如，如果平均有效负载有 500 个特征，并且每个特征估计为 20 个字符，则每个事件大约为 10kb。 根据这些估计值，20,000,000/10,000 = 2,000 个事件/秒，也就是大约 1.73 亿个事件/天。 

如果快达到这些限制，请联系我们的支持团队以获取体系结构建议。

## <a name="next-steps"></a>后续步骤

[创建和配置个性化体验创建服务](how-to-settings.md)。