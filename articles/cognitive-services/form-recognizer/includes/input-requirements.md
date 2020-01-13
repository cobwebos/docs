---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379414"
---
表单识别器可以处理符合以下要求的输入文档：

* 格式必须为 JPG、PNG、PDF（文本或扫描件）或 TIFF。 最好是使用文本嵌入式 PDF，因为这可以避免在提取和定位字符时出错。
* 如果 PDF 是密码锁定的文件，则必须先删除锁，然后才能提交它们。
* PDF 和 TIFF 文档不得超过 200 页，并且训练数据集的总大小不得超过 500 页。
* 对于图像，尺寸必须介于 600 x 100 像素与 4200 x 4200 像素之间。
* 如果从纸质文档扫描表单，应使用优质扫描仪。
* 文本必须使用拉丁字母（英文字符）。
* 对于非监督式学习（没有已标记的数据），数据必须包含键和值。
* 对于非监督式学习（没有已标记的数据），键必须出现在值的上方或左侧；它们不能出现在下方或右侧。

表单识别器目前不支持以下类型的输入数据：

* 复杂表（嵌套表、合并的标题或单元格，等等）。
* 复选框或单选按钮。
