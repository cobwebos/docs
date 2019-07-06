---
title: 执行图像操作 - Python
titlesuffix: Azure Cognitive Services
description: 通过 Jupyter Notebook 了解如何使用 Python 中的计算机视觉 API。 使用常用库直观显示结果。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: b18f41db772c1c214bdf48c9bb765b1ce7e6d2c2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604155"
---
# <a name="computer-vision-api-jupyter-notebook"></a>计算机视觉 API Jupyter 笔记本

本指南演示了如何在 Python 中使用计算机视觉 API 以及如何使用流行库可视化结果。 你将使用 Jupyter 运行本教程。 若要了解如何开始使用交互式 Jupyter Notebook，请参阅 [Jupyter 文档](https://jupyter.readthedocs.io/en/latest/index.html)。

## <a name="prerequisites"></a>必备组件

- [Python 2.7+ 或 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) 工具
- 已安装 [Jupyter Notebook](https://jupyter.org/install)

## <a name="open-the-notebook-in-jupyter"></a>在 Jupyter 中打开笔记本 

1. 转到[认知视觉 Python](https://github.com/Microsoft/Cognitive-Vision-Python) GitHub 存储库。 
2. 单击绿色按钮以克隆或下载存储库。 
3. 打开命令提示符并导航到文件夹 Cognitive-Vision-Python\Jupyter Notebook  。
1. 通过从命令提示符运行命令 `pip install requests opencv-python numpy matplotlib`，确保已安装所有必需的库。
1. 通过从命令提示符运行命令 `jupyter notebook` 启动 Jupyter。
1. 在 Jupyter 窗口中，单击“计算机视觉 API Example.ipynb”以打开教程笔记本  。

## <a name="run-the-notebook"></a>运行笔记本

若要使用此笔记本，将需要计算机视觉 API 的订阅密钥。 访问[订阅页面](https://azure.microsoft.com/try/cognitive-services/)进行注册。 在“登录”  页面上，使用 Microsoft 帐户登录，便能够订阅并获取免费密钥。 完成注册过程后，将密钥粘贴到 Notebook 的 `Variables` 节（如下所示）。 主密钥或辅助密钥都将有效。 确保将密钥括在引号中以使其成为字符串。

还需要确保 `_region` 字段与你的订阅对应的区域匹配。

```python
# Variables
_region = 'westcentralus'  # Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(
    _region)
_key = None  # Here you have to paste your primary key
_maxNumRetries = 10
```

运行本教程时，你将能够从 URL 和本地存储中添加要分析的图像。 脚本将在 Notebook 中显示图像和分析信息。
