---
title: 所访问的帐户不支持 Azure HDInsight 中的 http 错误
description: 本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77165548"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>所访问的帐户不支持 Azure HDInsight 中的 http 错误

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

收到以下错误消息：

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>原因

收到错误消息的原因有多种：

* 存储帐户已启用[安全传输](../../storage/common/storage-require-secure-transfer.md)，但使用的 [URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)不正确。

* 已使用禁用  安全传输的存储帐户创建了群集。 此后在存储帐户上启用了安全传输。

## <a name="resolution"></a>解决方法

如果为 Azure 存储或 Data Lake Storage Gen2 启用了安全传输，则 URI 分别是 `wasbs://` 或 `abfss://`。  另请参阅[安全传输](../../storage/common/storage-require-secure-transfer.md)。

对于新群集，请使用已经有所需安全传输设置的存储帐户。 请勿更改现有群集所用存储帐户的安全传输设置。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
