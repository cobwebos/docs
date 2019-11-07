---
title: 创建自定义终结点 | Microsoft Docs
description: 本文介绍如何配置要使用 Internet 分析器资源进行度量的自定义终结点。
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 44d570746a54154a2f8d3c7a200c761a59b7422b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488103"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>度量要在 Internet 分析器测试中评估的自定义终结点 

本文演示如何设置要在 Internet 分析器测试中进行度量的自定义终结点。 自定义终结点可帮助评估本地工作负荷、在其他云提供商上运行的工作负荷以及自定义 Azure 配置。  如果一个终结点是 Azure 资源，则可以在一个测试中比较两个自定义终结点。 有关 Internet 分析器的详细信息，请参阅[概述](internet-analyzer-overview.md)。 

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>开始之前

请确保设置 Internet 分析器资源，并选择“自定义终结点”选项。 Internet 分析器假设自定义终结点可以访问 Internet。 有关详细信息，请参阅[创建 Internet 分析器资源](internet-analyzer-create-test-portal.md)。


## <a name="create-custom-endpoint"></a>创建自定义终结点

1. [在此处](https://fpc.msedge.net/apc/trans.gif)下载透明的单像素测试图像。 这一单像素图像是客户端 JavaScript 为衡量性能而提取的资产。
2. 在自定义 Web 应用程序中，部署可公开访问的路径中的测试图像。 该路径应通过 HTTPS 运作。 
3. 在测试创建期间，将完整的自定义终结点 URL（例如 https://contoso.com/test/trans.gif) ）复制到“自定义终结点”字段。

## <a name="next-steps"></a>后续步骤

阅读 [Internet 分析器常见问题解答](internet-analyzer-faq.md)

