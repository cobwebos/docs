---
title: 快速入门：使用 BAM 文件输入提交工作流 | Microsoft Docs
titleSuffix: Azure
description: 本快速入门假定你已安装 msgen 客户端并已通过服务成功运行示例数据。
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: quickstart
ms.date: 12/07/2017
ms.openlocfilehash: 9887121593cad4931c86b55012f1c22686adf25f
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2017
ms.locfileid: "26684511"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>使用 BAM 文件输入提交工作流

本快速入门演示了在输入文件为单个 BAM 文件的情况下，如何将工作流提交到 Microsoft 基因组学服务。 本主题假定你已安装和运行 `msgen` 客户端，并且熟悉如何使用 Azure 存储。 如果你已成功使用提供的示例数据提交工作流，则可开始学习本教程。 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>设置：将 BAM 文件上传到 Azure 存储
假设有一个 BAM 文件 (*reads.bam*)，并且已将其上传到 Azure 中的存储帐户 *myaccount*，其形式为 **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads<span></span>.bam<span></span>**。 你有 API URL 和访问密钥。 你希望将输出置于 **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>** 中。



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
  --input-blob-name-1 reads.bam ^
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
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


如果愿意使用配置文件，可以参考以下示例，其中包含：

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

提交 `config.txt` 文件时可以使用此调用：`msgen submit -f config.txt`

## <a name="next-steps"></a>后续步骤
本文介绍了如何将 BAM 文件上传到 Azure 存储中，以及如何通过 `msgen` Python 客户端将工作流提交到 Microsoft 基因组学服务。 若要详细了解工作流提交以及其他可以与 Microsoft 基因组学服务配合使用的命令，请参阅[常见问题解答](frequently-asked-questions-genomics.md)。 
