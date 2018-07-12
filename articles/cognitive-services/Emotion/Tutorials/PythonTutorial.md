---
title: 情感 API Python 教程 | Microsoft Docs
description: 使用 Jupyter 笔记本了解如何借助 Python 使用认知服务情感 API。 使用常用库直观显示结果。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 70c8ca48c651601f3d7cbb3717c32bfe112176fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365733"
---
# <a name="emotion-api-using-python-tutorial"></a>使用 Python 的情感 API 教程

> [!IMPORTANT]
> 视频 API 预览将于 2017 年 10 月 30 日结束。 请试用新的[视频索引器 API 预览](https://azure.microsoft.com/services/cognitive-services/video-indexer/)，通过检测口语、人脸、字符和情感，轻松从视频提取见解，增强搜索结果等内容发现体验。 [了解详细信息](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

为便于快速入门情感 API，下面链接的 Jupyter 笔记本介绍了如何在 Python 中使用 API 以及如何使用一些常用库直观显示结果。 

[链接 GitHub 中的笔记本](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>使用 Jupyter Notebook

若要以交互方式使用笔记本，需要克隆并在 Jupyter 中运行它。 若要了解如何开始使用交互式 Jupyter 笔记本，请遵循 http://jupyter.readthedocs.org/en/latest/install.html 上的说明。 

若要使用此笔记本，将需要情感 API 的订阅密钥。 访问[订阅页面](https://azure.microsoft.com/try/cognitive-services/)进行注册。 在“登录”页面上，使用你的 Microsoft 帐户登录，将能够订阅并获取免费密钥。 完成注册过程后，将密钥粘贴到下面显示的变量部分。 主密钥或辅助密钥都有效。

```
Python Example 

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
