---
title: Microsoft 基因组学：故障排除指南 | Microsoft Docs
titleSuffix: Azure
description: 详细了解故障排除策略
keywords: 故障排除, 错误, 调试
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: article
ms.date: 04/13/2018
ms.openlocfilehash: 18761c02cc423affe7b1050700e560b1f0b0594d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="troubleshooting-guide"></a>故障排除指南
本概述文章介绍解决使用 Microsoft 基因组服务时的常见问题的策略。 有关常规常见问题解答，请参阅[常见问题](frequently-asked-questions-genomics.md)。 


## <a name="how-do-i-check-my-job-status"></a>如何查看作业状态？
要查看工作流的状态，可在命令行中调用 `msgen status`，如下所示。 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

有三个必需参数：
* URL - API 的基本 URI
* 密钥 - 基因组学帐户的访问密钥。 
* ID - 工作流 ID

要查找 URL 和密钥，请转到 Azure 门户并打开基因组学帐户页。 在“管理”标题下方，选择“访问密钥”。 找到 API URL 和访问密钥。

或者，可在配置文件中包含路径，而不是直接输入 URL 和密钥。 请注意，如果在命令行和配置文件中包含这些参数，命令行参数将具有优先权。 

调用 `msgen status` 后将显示用户友好消息，说明工作流已成功或提供作业失败原因。 


## <a name="get-more-information-about-my-workflow-status"></a>获取有关工作流状态的详细信息

要详细了解作业未成功的可能原因，可查看工作流运行期间生成的日志文件。 输出容器中应显示 `[youroutputfilename].logs.zip` 文件夹。  解压缩此文件夹，将出现以下项：

* outputFileList.txt - 工作流运行期间生成的输出文件列表
* standarderror.txt - 此文件为空。
* standardoutput.txt - 包含工作流的顶级日志记录。 
* GATK 日志文件 - `logs` 文件夹中的所有其他文件

要想确定工作流未成功的原因，建议首先查看 `standardoutput.txt` 文件，因为其中包含工作流的更多低级别信息。 

## <a name="common-issues-and-how-to-resolve-them"></a>常见问题与解决方法
本部分主要简单介绍常见问题和解决方法。

### <a name="fastq-files-are-unmatched"></a>Fastq 文件不匹配
Fastq 文件的区别应仅在于示例标识符中是尾随 /1 还是尾随 /2。 如果意外提交了不匹配的 FASTQ 文件，调用 `msgen status` 时可能看到以下错误消息。
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

要解决此问题，请查看提交到工作流的 fastq 文件是否相互匹配。 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>上传 .bam 文件时出错。 已存在输出 blob 且覆盖选项设置为 False。
如果看到以下错误消息 `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`，则表示输出文件夹中已包含具有相同名称的输出文件。  请删除现有输出文件或在配置文件中启用覆盖选项。 然后重新提交工作流。

### <a name="when-to-contact-microsoft-genomics-support"></a>何时与 Microsoft 基因组学支持人员联系
如果看到以下错误消息，则表示发生了内部错误。 

* `Error locating input files on worker machine`
* `Process management failure`

请尝试重新提交工作流。 如果作业继续失败或有任何其他疑问，请通过 Azure 门户与 Microsoft 基因组学支持人员联系。

![在 Azure 门户中联系支持人员](./media/troubleshooting-guide/genomics-contact-support.png "Contact support on Azure portal")

## <a name="next-steps"></a>后续步骤
本文介绍了如何排除和解决 Microsoft 基因组学服务的常见问题。 有关详细信息和更多常规常见问题解答，请参阅[常见问题](frequently-asked-questions-genomics.md)。 