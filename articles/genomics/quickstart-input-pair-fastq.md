---
title: 提交使用 FASTQ 文件输入-Microsoft 基因组学工作流
titleSuffix: Azure
description: 本文假定你已安装 msgen 客户端并成功运行了通过服务的示例数据。
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 2662a8f52c58a39916e5789fa9ed7fadd91216c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60333666"
---
# <a name="submit-a-workflow-using-fastq-file-inputs-in-microsoft-genomics"></a>使用 Microsoft 基因组学中的 FASTQ 文件输入提交工作流

本文演示如何将输入的文件为一对 FASTQ 文件提交到 Microsoft 基因组学服务的工作流。 本主题假设你已安装并运行 `msgen` 客户端，且熟悉如何使用 Azure 存储。 如果您已成功提交使用提供的示例数据的工作流，你现可继续执行本文。 

## <a name="set-up-upload-your-fastq-files-to-azure-storage"></a>设置：FASTQ 文件上传到 Azure 存储
假设你有两个文件：*reads_1.fq.gz* 和 *reads_2.fq.gz* 并已将其上传到 Azure 中的存储帐户 *myaccount*，其形式为 **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads_1<span></span>.fq<span></span>.gz<span></span>** 和 **https://<span></span>myaccount.blob.core.<span></span>windows<span></span>.net/<span></span>inputs/<span></span>reads_2.fq<span></span>.gz<span></span>**。 你有 API URL 和访问密钥。 你希望将输出置于 **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** 中。


## <a name="submit-your-job-to-the-msgen-client"></a>将作业提交到 `msgen` 客户端 

下面是需提供给 `msgen` 客户端的最小参数集；为清楚起见，添加了换行符：

对于 Windows：

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fq.gz ^
  --input-blob-name-2 reads_2.fq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```

对于 Unix：

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fq.gz \
  --input-blob-name-2 reads_2.fq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


如果愿意使用配置文件，可以参考以下示例，其中包含：

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz
input_blob_name_2:                reads_2.fq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

提交 `config.txt` 文件时可以使用此调用：`msgen submit -f config.txt`

## <a name="next-steps"></a>后续步骤
本文介绍了如何将一对 FASTQ 文件上传到 Azure 存储中，以及如何通过 `msgen` Python 客户端将工作流提交到 Microsoft 基因组学服务。 若要了解有关工作流提交以及可以与 Microsoft 基因组学服务配合使用其他命令的详细信息，请参阅我们[常见问题解答](frequently-asked-questions-genomics.md)。 
