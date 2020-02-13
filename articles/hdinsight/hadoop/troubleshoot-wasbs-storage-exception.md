---
title: 正在访问的帐户不支持 Azure HDInsight 中的 http 错误
description: 本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165548"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>正在访问的帐户不支持 Azure HDInsight 中的 http 错误

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

收到以下错误消息：

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>原因

收到错误消息的原因有多种：

* 存储帐户已启用[安全传输](../../storage/common/storage-require-secure-transfer.md)，并使用了错误的[URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。

* 使用*已禁用*安全传输的存储帐户创建了群集。 此后，会在存储帐户上启用安全传输。

## <a name="resolution"></a>解决方法

如果为 Azure 存储或 Data Lake Storage Gen2 启用安全传输，则 URI 将分别为 `wasbs://` 或 `abfss://`。  另请参阅[安全传输](../../storage/common/storage-require-secure-transfer.md)。

对于新群集，请使用已具有所需安全传输设置的存储帐户。 请勿更改现有群集使用的存储帐户的安全传输设置。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -用于改善客户体验的官方 Microsoft Azure 帐户。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
