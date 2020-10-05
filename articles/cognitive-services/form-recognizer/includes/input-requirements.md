---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 5de121a1a905a58f8b5eaf8e60c1f8da71ee8cd6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91276930"
---
表单识别器可以处理符合以下要求的输入文档：

* 格式必须为 JPG、PNG、PDF（文本或扫描件）或 TIFF。 最好是使用文本嵌入式 PDF，因为这可以避免在提取和定位字符时出错。
* 文件大小必须小于 20 MB。
* 图像尺寸必须介于 50 x 50 像素与 10000 x 10000 像素之间。
* PDF 尺寸必须最多为 17 x 17 英寸，对应于 Legal 或 A3 纸张尺寸及更小。
* 对于 PDF 和 TIFF，仅处理前 200 页（对于免费层订阅，仅处理前两页）。
* 训练数据集的总大小不得超过 500 页。
* 如果 PDF 是密码锁定的文件，则必须先删除锁，然后才能提交它们。
* 如果从纸质文档扫描表单，应使用优质扫描仪。
* 文本必须使用拉丁字母（英文字符）。
* 对于非监督式学习（没有已标记的数据），数据必须包含键和值。
* 对于非监督式学习（没有已标记的数据），键必须出现在值的上方或左侧；它们不能出现在下方或右侧。

表单识别器目前不支持以下类型的输入数据：

* 复杂表（嵌套表、合并的标题或单元格，等等）。
* 复选框或单选按钮。
