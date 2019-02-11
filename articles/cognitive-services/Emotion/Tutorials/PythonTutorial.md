---
title: 教程：识别图像中人脸的情感 - 情感 API、Python
titlesuffix: Azure Cognitive Services
description: 使用 Jupyter Notebook 了解如何通过 Python 使用情感 API。 使用常用库直观显示结果。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: tutorial
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: ea96495fb9fa453f7c7f9d6a870291329d2793b2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220529"
---
# <a name="tutorial-use-the-emotion-api-with-a-jupyter-notebook--python"></a>教程：通过 Jupyter Notebook 和 Python 使用情感 API。

> [!IMPORTANT]
> 情感 API 将于 2019 年 2 月 15 日弃用。 情感识别功能现在已作为[人脸 API](https://docs.microsoft.com/azure/cognitive-services/face/) 的一部分正式发布。 

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
