---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 4079b0cab0126a9750bbb91ca7046aa18dbe9ece
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657734"
---
表单识别器可以处理符合以下要求的输入文档：

* 格式必须为 JPG、PNG 或 PDF（文本或扫描件）。 最好是使用文本嵌入式 PDF，因为这可以避免在提取和定位字符时出错。
* 如果 PDF 是密码锁定的文件，则必须先删除锁，然后才能提交它们。
* 文件大小必须小于 4 MB。
* 对于图像，尺寸必须介于 600 x 100 像素与 4200 x 4200 像素之间。
* 如果从纸质文档扫描表单，应使用优质扫描仪。
* 文本必须使用拉丁字母（英文字符）。
* 数据必须包含键和值。
* 键可以显示在值的上方或左侧，而不能显示在下方或右侧。

表单识别器目前不支持以下类型的输入数据：

* 复杂表（嵌套表、合并的标题或单元格，等等）。
* 复选框或单选按钮。
* 超过 50 页的 PDF 文档。
