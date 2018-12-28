---
title: 文档格式和命名约定 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 本指南介绍自定义翻译中的文档格式和命名约定。 这些概念可帮助你更好地管理文档名称，避免发生命名冲突。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 74b421691f5d3536c44af16e99dc54451dfde3f7
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872758"
---
# <a name="document-formats-and-naming-convention-guidance"></a>文档格式和命名约定指南

用于自定义翻译的任何文件的长度必须至少为**四个**字符。

下表包含了可用于生成翻译系统的所有受支持文件格式：

| 格式            | 扩展   | Description                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | .XLF、.XLIFF | 并行文档格式，是翻译记忆系统的导出格式。 使用的语言在该文件中定义。                                                                                                                                                              |
| TMX               | .TMX         | 并行文档格式，是翻译记忆系统的导出格式。 使用的语言在该文件中定义。                                                                                                                                                              |
| ZIP               | .ZIP         | ZIP 是一种存档文件格式。                                                                                                                                                                                                        |
| Locstudio         | .LCL         | 并行文档的 Microsoft 格式                                                                                                                                                                                                                                      |
| Microsoft Word    | .DOCX        | Microsoft Word 文档                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | .PDF         | Adobe Acrobat 可移植文档                                                                                                                                                                                                                                                |
| HTML              | .HTML、.HTM  | HTML 文档                                                                                                                                                                                                                                                                  |
| 文本文件         | .TXT         | UTF-16 或 UTF-8 编码的文本文件                                                                                                                                                                                                                                             |
| 对齐的文本文件 | .ALIGN       | `.ALIGN` 是一个特殊的扩展名，如果你知道文档对中的句子已完美对齐，则可以使用此扩展名。 如果提供 `.ALIGN` 文件，自定义翻译不会对齐句子。 |
| Excel 文件        | .XLSX        | Excel 文件（2013 或更高版本）。 电子表格的第一行应是语言代码。                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>字典格式

对于字典，自定义翻译支持训练集所支持的所有文件格式。 如果使用 Excel 字典，请确保电子表格的第一行是语言代码。

## <a name="zip-file-formats"></a>Zip 文件格式

可将文档组合到单个 zip 文件并上传。 自定义翻译支持 zip 文件格式（ZIP、GZ 和 TGZ）。

扩展名为 TXT、HTML、HTM、PDF、DOCX、ALIGN 的 zip 文件中的每个文档都必须遵循以下命名约定：

{document name}\_{language code}：其中，{document name} 是文档的名称，{language code} 是 ISO 语言 ID（两个字符），指示文档包含采用该语言的句子。 语言代码的前面必须有下划线 (_)。

例如，若要在 zip 中为英语到西班牙语翻译系统上传两个并行文档，则应将文件命名为“data_en”和“data_es”。

无需翻译记忆库文件（TMX、XLF、XLIFF、LCL、XLSX）即可遵循特定的语言命名约定。  

## <a name="next-steps"></a>后续步骤

- 了解如何创建和管理[项目](workspace-and-project.md#what-is-a-custom-translator-project)。
