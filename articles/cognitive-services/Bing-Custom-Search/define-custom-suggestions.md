---
title: 必应自定义搜索：定义自定义自动建议 | Microsoft Docs
description: 介绍如何为自定义自动建议配置自定义建议
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: a41b4e5b6c268ec68488c6764d4192cf8d2345a4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365883"
---
# <a name="configure-your-custom-autosuggest-experience"></a>配置自定义自动建议体验
如果已订阅相应级别的自定义搜索（请参阅[定价页](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)），则可以自定义在自定义搜索体验中显示的搜索建议。 自定义自动建议根据用户提供的部分查询字符串返回建议查询的列表。 借助自定义自动建议，可以提供与搜索体验相关的自定义搜索建议。 指定只返回自定义建议还是包括必应建议。 如果包括必应建议，则自定义建议在必应建议之前显示。 必应建议仅限于自定义搜索实例的上下文。

## <a name="configure-custom-autosuggest"></a>配置自定义自动建议
使用以下说明为自定义搜索实例配置自定义自动建议。

1.  登录[自定义搜索](https://customsearch.ai)。
2.  单击自定义搜索实例。 若要创建实例，请参阅[创建首个必应自定义搜索实例](quick-start.md)。
3.  单击“自动建议”选项卡。

## <a name="enable-bing-suggestions"></a>启用必应建议
若要启用必应建议，请将“自动必应建议”滑块切换到开启位置。 滑块变为蓝色。

## <a name="add-suggestions"></a>添加建议
若要添加建议，请在文本框中输入建议。 按 Enter 键或单击 + 图标。 自定义建议可以是任何语言，且都会显示在必应建议之前。

## <a name="upload-suggestions"></a>上传建议
可从文件上传建议列表。 将每个建议单独成行。 单击“上传”图标，并选择文件。

## <a name="remove-suggestions"></a>删除建议
若要删除建议，请单击要删除的建议旁的删除图标。

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

  >[!NOTE]  
  >自定义自动建议配置更改最多可能需要 24 小时才能生效。

## <a name="next-steps"></a>后续步骤

- [获取自定义建议](./get-custom-suggestions.md)
- [搜索自定义实例](./search-your-custom-view.md)
- [配置和使用自定义托管 UI](./hosted-ui.md)