---
title: 必应自定义搜索：搜索自定义视图 | Microsoft Docs
description: 介绍如何搜索 Web 的自定义视图
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 75f6c8d299c7eed901dda0631fca74b040f72e30
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365884"
---
# <a name="call-your-custom-search"></a>调用自定义搜索
在第一次调用自定义搜索 API 以获取实例的搜索结果之前，需要先获取认知服务订阅密钥。 若要获取自定义搜索 API 的密钥，请参阅[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)。

> [!NOTE]
> 如果现有必应自定义搜索客户拥有在 2017 年 10 月15 日当天或之前预配的预览密钥，则可以在 2017 年 11 月 30 日或用尽所允许的最大查询数前使用他们的密钥。 之后，他们需要迁移到 Azure 上的正式发布版本。

## <a name="try-it-out"></a>试用
配置完自定义搜索体验后，可以在自定义搜索门户中测试配置。 登录到[自定义搜索](https://customsearch.ai)，单击自定义搜索实例，然后单击“生产”选项卡。系统将显示“终结点”选项卡。 你的订阅将决定可以试用的终结点，请参阅[定价页](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)。 若要测试某个终结点，请从下拉列表中选中它并设置关联的配置选项。 

以下是可用的选项。

- **查询**：要搜索的搜索词。 仅适用于 Web 终结点、图像终结点和自动建议终结点。
- **自定义配置 ID**：所选自定义搜索实例的配置 ID。 该字段是只读字段。
- **市场**：结果的来源市场。 仅适用于 Web 终结点、图像终结点和托管 UI 终结点。
- **订阅密钥**：测试时要使用的订阅密钥。 可以从下拉列表中选择一个密钥或手动输入一个密钥。
- **安全搜索**：用于筛选成人内容网页的筛选器。 仅适用于 Web 终结点、图像终结点和托管 UI 终结点。
- **计数**：要在响应中返回的搜索结果数。 仅适用于 Web 终结点和图像终结点。
- **偏移量**：要在响应中返回的搜索结果数。 仅适用于 Web 终结点和图像终结点。

在为 Web、图像或自动建议指定了所有必需选项后，单击“调用”以在右侧窗格中查看 JSON 响应。 

如果选择托管 UI 终结点，则可以从右侧窗格测试搜索体验。

## <a name="next-steps"></a>后续步骤
- [使用 C# 调用自定义视图](./call-endpoint-csharp.md)
- [使用 Java 调用自定义视图](./call-endpoint-java.md)
- [使用 NodeJs 调用自定义视图](./call-endpoint-nodejs.md)
- [使用 Python 调用自定义视图](./call-endpoint-python.md)