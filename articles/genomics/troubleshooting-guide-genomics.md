---
title: 故障排除指南
titleSuffix: Microsoft Genomics
description: 了解有关使用 Microsoft 基因组学的故障排除策略。
keywords: 故障排除, 错误, 调试
services: genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: ce8af4d444e642a8f67f43f8cf403ce9b2cb08ab
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248516"
---
# <a name="troubleshooting-guide"></a>故障排除指南

下面提供了当你使用 Microsoft 基因组学服务 (MSGEN) 时可能会遇到的一些常见问题的故障排除提示。

 如需与故障排除无关的常见问题解答，请参阅[常见问题](frequently-asked-questions-genomics.md)。
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>步骤 1：查找与工作流关联的错误代码

可通过以下方式查找与工作流关联的错误消息：

1. 使用命令行并键入 `msgen status`
2. 检查 standardoutput.txt 的内容。

### <a name="1-using-the-command-line-msgen-status"></a>1.使用命令行 `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




有三个必需参数：

* URL - API 的基本 URI
* 密钥 - 基因组学帐户的访问密钥
    * 若要查找 URL 和密钥，请转到 Azure 门户并打开 Microsoft 基因组学帐户页。 在“管理”标题下方，选择“访问密钥”。 可在此处找到 API URL 和访问密钥。

  
* ID - 工作流 ID
    * 若要查找工作流 ID，请键入 `msgen list` 命令。 假设配置文件包含 URL 和访问密钥，并且与 msgen 可执行文件位于同一位置，则命令如下所示： 
        
        ```bash
        msgen list -f "config.txt"
        ```
        此命令的输出如下所示：
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  或者，可在配置文件中包含路径，而不是直接输入 URL 和密钥。 如果在命令行和配置文件中包含这些参数，命令行参数将具有优先权。  

如果工作流 ID 为 1001，并且 config.txt 文件与 msgen 可执行文件位于同一路径，则命令如下所示：

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2.检查 standardoutput.txt 的内容 
查找有问题的工作流的输出容器。 每次执行工作流后，MSGEN 会创建一个 `[workflowfilename].logs.zip` 文件夹。 请解压缩该文件夹以查看其内容：

* outputFileList.txt - 工作流运行期间生成的输出文件列表
* standarderror.txt - 此文件为空。
* standardoutput.txt - 记录运行工作流时发生的所有顶级状态消息，包括错误。
* GATK 日志文件 - `logs` 文件夹中的所有其他文件

若要进行故障排除，请检查 standardoutput.txt 的内容，并记下出现的任何错误消息。


## <a name="step-2-try-recommended-steps-for-common-errors"></a>步骤 2：尝试常见错误的建议步骤

本部分简要描述 Microsoft 基因组学服务 (msgen) 输出的常见错误，以及可用于解决这些错误的策略。 

Microsoft 基因组学服务 (msgen) 可能引发以下两种类型的错误：

1. 内部服务错误：服务内部的错误，可能无法通过修复参数或输入文件进行解析。 有时，重新提交工作流可能会解决这些错误。
2. 输入错误：使用正确的参数或修复文件格式可以解决的错误。

### <a name="1-internal-service-errors"></a>1.内部服务错误

内部服务错误不可由用户处理。 可以重新提交工作流，但如果这样做也解决不了问题，请联系 Microsoft 基因组学支持人员

| 错误消息                                                                                                                            | 建议的故障排除步骤                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 发生了内部错误。 请尝试重新提交工作流。 如果再次看到此错误，请联系 Microsoft 基因组学支持人员以获得帮助 | 重新提交工作流。 如果问题持续出现，请通过创建支持[票证](file-support-ticket-genomics.md )来联系 Microsoft 基因组学支持人员，以获得帮助。 |

### <a name="2-input-errors"></a>2.输入错误

这些错误可由用户处理。 根据文件类型和错误代码，Microsoft 基因组学服务会输出不同的错误代码。 请遵循下面列出的建议故障排除步骤。

| 文件类型 | 错误代码 | 错误消息                                                                           | 建议的故障排除步骤                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| 任意          | 701        | 读取操作 [readId] 具有 [numberOfBases] 个基，但限制为 [maxReadLength]           | 出现此错误的最常见原因是文件损坏，导致串联了两个读取操作。 请检查输入文件。 |
| BAM          | 200        |   无法读取文件“[yourFileName]”。                                                                                       | 请检查 BAM 文件的格式。 使用格式正确的文件重新提交工作流。                                                                           |
| BAM          | 201        |  无法读取 BAM 文件 [File_name]。                                                                                      |请检查 BAM 文件的格式。  使用格式正确的文件提交工作流。                                                                            |
| BAM          | 202        | 无法读取 BAM 文件 [File_name]。 文件太小，且缺少标头。                                                                                        | 请检查 BAM 文件的格式。  使用格式正确的文件提交工作流。                                                                            |
| BAM          | 203        |   无法读取 BAM 文件 [File_name]。 文件标头已损坏。                                                                                      |请检查 BAM 文件的格式。  使用格式正确的文件提交工作流。                                                                           |
| BAM          | 204        |    无法读取 BAM 文件 [File_name]。 文件标头已损坏。                                                                                     | 请检查 BAM 文件的格式。  使用格式正确的文件提交工作流。                                                                           |
| BAM          | 205        |    无法读取 BAM 文件 [File_name]。 文件标头已损坏。                                                                                     | 请检查 BAM 文件的格式。  使用格式正确的文件提交工作流。                                                                            |
| BAM          | 206        |   无法读取 BAM 文件 [File_name]。 文件标头已损坏。                                                                                      | 请检查 BAM 文件的格式。  使用格式正确的文件提交工作流。                                                                            |
| BAM          | 207        |  无法读取 BAM 文件 [File_name]。 在偏移量 [offset] 附近截断了文件。                                                                                       | 请检查 BAM 文件的格式。  使用格式正确的文件提交工作流。                                                                            |
| BAM          | 208        |   BAM 文件无效。 ReadID [Read_Id] 在文件 [File_name] 中没有序列。                                                                                      | 请检查 BAM 文件的格式。  使用格式正确的文件提交工作流。                                                                             |
| FASTQ        | 300        |  无法读取 FASTQ 文件。 [File_name] 不是以换行符结尾。                                                                                     | 请更正 FASTQ 文件的格式，然后重新提交工作流。                                                                           |
| FASTQ        | 301        |   无法读取 FASTQ 文件 [File_name]。 FASTQ 记录大于偏移量 [_offset] 处的缓冲区大小                                                                                      | 请更正 FASTQ 文件的格式，然后重新提交工作流。                                                                         |
| FASTQ        | 302        |     FASTQ 语法错误。 文件 [File_name] 包含空白行。                                                                                    | 请更正 FASTQ 文件的格式，然后重新提交工作流。                                                                         |
| FASTQ        | 303        |       FASTQ 语法错误。 文件 [File_name] 在偏移量 [_offset] 处包含无效的起始字符，行类型: [line_type]，字符: [_char]                                                                                  | 请更正 FASTQ 文件的格式，然后重新提交工作流。                                                                         |
| FASTQ        | 304      |  readID [_ReadID] 处存在 FASTQ 语法错误。  在文件 [File_name] 中，第一个批读取不包含以 /1 结尾的 readID                                                                                       | 请更正 FASTQ 文件的格式，然后重新提交工作流。                                                                         |
| FASTQ        | 305        |  readID [_readID] 处存在 FASTQ 语法错误。 在文件 [File_name] 中，第二个批读取不包含以 /2 结尾的 readID                                                                                      | 请更正 FASTQ 文件的格式，然后重新提交工作流。                                                                          |
| FASTQ        | 306        |  readID [_ReadID] 处存在 FASTQ 语法错误。 在文件 [File_name] 中，第一个对读取不包含以 /1 结尾的 ID                                                                                       | 请更正 FASTQ 文件的格式，然后重新提交工作流。                                                                          |
| FASTQ        | 307        |   readID [_ReadID] 处存在 FASTQ 语法错误。 ReadID 不是以 /1 或 /2 结尾。 文件 [File_name] 不可用作配对的 FASTQ 文件。                                                                                      |请更正 FASTQ 文件的格式，然后重新提交工作流。                                                                          |
| FASTQ        | 308        |  FASTQ 读取错误。 两端的读取以不同的方式做出了响应。 是否选择了正确的 FASTQ 文件?                                                                                       | 请更正 FASTQ 文件的格式，然后重新提交工作流。                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>步骤 3：联系 Microsoft 基因组学支持人员

如果作业继续失败或有任何其他疑问，请通过 Azure 门户与 Microsoft 基因组学支持人员联系。 可从[此处](file-support-ticket-genomics.md)找到有关如何提交支持请求的更多信息。

## <a name="next-steps"></a>后续步骤

本文介绍了如何排除和解决 Microsoft 基因组学服务的常见问题。 有关详细信息和更多常规常见问题解答，请参阅[常见问题](frequently-asked-questions-genomics.md)。 
