---
title: Microsoft 基因组学：常见问题-常见问题 |Microsoft Docs
titleSuffix: Azure
description: 有关 Microsoft 基因组学的用户常见问题解答。
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 12/07/2017
ms.openlocfilehash: 2bcbf9d145d9b8b5a3c42893235906d24516405c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65792622"
---
# <a name="microsoft-genomics-common-questions"></a>Microsoft 基因组学：常见问题

本文列出了用户可能会遇到的与 Microsoft 基因组学相关的几大疑问。 有关 Microsoft 基因组学服务的详细信息，请参阅[什么是 Microsoft 基因组学？](overview-what-is-genomics.md)。 有关故障排除的详细信息，请参阅我们的[故障排除指南](troubleshooting-guide-genomics.md)。 

## <a name="what-is-the-microsoft-genomics-service-gatk-4-promotion"></a>什么是 Microsoft 基因组学服务 GATK 4 促销版？
直到 2019 年 6 月 30 日，Microsoft 基因组学服务将提供使用 GATK4 免费的 20 WGS 运行。 若要参与此产品/服务，请在[此处](https://aka.ms/msgatk4)注册。 

### <a name="what-are-the-common-issues-i-might-encounter-while-running-the-microsoft-genomics-service-gatk4-promotion"></a>运行 Microsoft 基因组学服务 GATK4 促销版时可能会遇到的常见问题
下面是可能会遇到的常见错误及其建议的解决方法列表：

| **消息**                                                                                                                                                                                    | **原因**                                                                                                    | **解决方法**                                                                                                                                                                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 未为帐户启用 `gatk4-promo`。 有关详细信息，请参阅 https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics                               | 你正在尝试使用未激活的 Microsoft 基因组学服务运行 GATK4 工作流。       | 请访问[此网页](https://aka.ms/msgatk4)激活帐户。 请注意在试用版过期 2019 年 6 月 30 日。 在此日期之后，无法激活促销版运行的帐户。 |
| 感谢你试用 `gatk4-promo`。试用期已结束。 有关详细信息，请参阅 https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics                  | GATK4 试用版已在日历年底过期，而你正在尝试调用 `gatk4-promo` process_name。  | 请将 process_name 参数切换为 `gatk4` 而不是 `gatk4-promo`。 这是官方的 gatk4 版本，如果使用此参数，则需要为工作流付费。                                         |
| 感谢你试用 `gatk4-promo`。你已用完所有分配的运行。 有关详细信息，请参阅 https://docs.microsoft.com/azure/genomics/frequently-asked-questions-genomics | 已成功提交 GATK4 的所有 20 个促销版运行。                               | 请在将 process_name 参数设置为 `gatk4`（而不是 `gatk4-promo`）后提交任何新的 gatk4 运行。 使用此参数时，需要为工作流付费。                                                          |        


## <a name="can-i-run-gatk4-workflows-on-microsoft-genomics-without-signing-up-for-the-gatk4-promotion"></a>是否可以在不注册 GATK4 促销版的情况下针对 Microsoft 基因组学运行 GATK4 工作流？
可以。在 Microsoft 基因组学服务的 config.txt 文件中将 process_name 指定为 `gatk4` 即可。 请注意，你需要按一般费率付费，20 个免费运行不适用于你的 Microsoft 基因组学帐户。



## <a name="what-is-the-sla-for-microsoft-genomics"></a>什么是 Microsoft 基因组学的 SLA？
我们保证 Microsoft 基因组学服务 99.9% 的时间均可用于接收工作流 API 请求。 有关详细信息，请参阅 [SLA](https://azure.microsoft.com/support/legal/sla/genomics/v1_0/)。

## <a name="how-does-the-usage-of-microsoft-genomics-show-up-on-my-bill"></a>Microsoft 基因组学的使用情况如何体现在我的帐单上？
Microsoft 基因组学将按每个工作流处理的千兆碱基数计费。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/genomics/)。


## <a name="where-can-i-find-a-list-of-all-possible-commands-and-arguments-for-the-msgen-client"></a>在哪里可以找到所有可用 `msgen` 客户端命令和参数列表？
通过运行 `msgen help` 可获得可用命令和参数的完整列表。 如果未提供进一步的参数，它会显示可用帮助部分的列表，每个 `submit`、`list`、`cancel` 和 `status` 各有一个列表。 若要获取有关特定命令的帮助，请键入 `msgen help command`；例如，`msgen help submit` 会列出所有提交选项。

## <a name="what-are-the-most-commonly-used-commands-for-the-msgen-client"></a>`msgen` 客户端最常用的命令有哪些？
最常用的命令是 `msgen` 客户端的参数，包括： 

 |**命令**          |  **字段说明** |
 |:--------------------|:-------------         |
 |`list`               |返回已提交的作业列表。 有关参数，请参阅 `msgen help list`。  |
 |`submit`             |向服务提交工作流请求。 有关参数，请参阅 `msgen help submit`。|
 |`status`             |返回由 `--workflow-id` 指定的工作流状态。 另请参阅 `msgen help status`。 |
 |`cancel`             |发送请求来取消由 `--workflow-id` 指定的工作流进程。 另请参阅 `msgen help cancel`。 |

## <a name="where-do-i-get-the-value-for---api-url-base"></a>在何处获取 `--api-url-base` 的值？
请转到 Azure 门户并打开基因组学帐户页。 在“管理”标题下方，选择“访问密钥”   。 可在此处找到 API URL 和访问密钥。

## <a name="where-do-i-get-the-value-for---access-key"></a>在何处获取 `--access-key` 的值？
请转到 Azure 门户并打开基因组学帐户页。 在“管理”标题下方，选择“访问密钥”   。 可在此处找到 API URL 和访问密钥。

## <a name="why-do-i-need-two-access-keys"></a>为什么需要两个访问密钥？
如果想更新（重新生成）密钥而不中断对服务的使用，则需要两个访问密钥。 例如，若要更新第一个密钥，则应该让所有新工作流使用第二个密钥。 等待使用第一个密钥的所有工作流完成，然后再更新第一个密钥。

## <a name="do-you-save-my-storage-account-keys"></a>是否保存了我的存储帐户密钥？
存储帐户密钥用于创建 Microsoft 基因组学服务的短期访问令牌，读取输入文件并写入输出文件。 默认令牌持续时间为 48 小时。 可以通过提交命令的 `-sas/--sas-duration` 选项更改令牌持续时间；该值以小时为单位。

## <a name="what-genome-references-can-i-use"></a>可以使用哪些基因组引用？

支持以下这些引用：

 |引用              | `-pa/--process-args` 的值 |
 |:-------------         |:-------------                 |
 |b37                    | `R=b37m1`                     |
 |hg38                   | `R=hg38m1`                    |      
 |hg38（无 alt 分析） | `R=hg38m1x`                   |  
 |hg19                   | `R=hg19m1`                    |    

## <a name="how-do-i-format-my-command-line-arguments-as-a-config-file"></a>如何将命令行参数格式化为配置文件？ 

msgen 可识别采用以下格式的配置文件：
* 所有选项都以键值对的形式提供，值与密钥之间用冒号隔开。
  忽略空格。
* 忽略以 `#` 开头的行。
* 可通过去除任何长格式命令行参数单词之间的前导短划线并将短划线替换为下划线，将其转换为密钥。 此处是一些转换示例：

  |命令行参数            | 配置文件行 |
  |:-------------                   |:-------------                 |
  |`-u/--api-url-base https://url`  | api_url_base: https://url     |
  |`-k/--access-key KEY`            | access_key:KEY               |      
  |`-pa/--process-args R=B37m1`     | process_args:R-b37m1         |  

## <a name="next-steps"></a>后续步骤

使用以下资源进行 Microsoft 基因组学入门：
- 通过 Microsoft 基因组学服务开始运行第一个工作流。 [通过 Microsoft 基因组学服务运行工作流](quickstart-run-genomics-workflow-portal.md)
- 提交自己的数据并通过以下 Microsoft 基因组学服务进行处理：[配对 FASTQ](quickstart-input-pair-FASTQ.md) | [BAM ](quickstart-input-BAM.md) | [多个 FASTQ 或 BAM](quickstart-input-multiple.md) 

