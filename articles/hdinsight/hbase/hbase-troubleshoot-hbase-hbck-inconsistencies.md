---
title: hbase hbck 返回 Azure HDInsight 中不一致的结果
description: hbase hbck 返回 Azure HDInsight 中不一致的结果
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "75887319"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>场景：`hbase hbck` 命令返回 Azure HDInsight 中不一致的结果

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue-region-is-not-in-hbasemeta"></a>问题：区域不在 `hbase:meta` 中

区域 xxx 位于 HDFS 上，但未在 `hbase:meta` 中列出，或未在任何区域服务器上部署。

### <a name="cause"></a>原因

多种多样。

### <a name="resolution"></a>解决方法

1. 运行以下命令修复元表：

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. 运行以下命令将区域分配到 RegionServer：

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>问题：区域已脱机

区域 xxx 未部署在任何 RegionServer 上。 这意味着区域在 `hbase:meta` 中，但已脱机。

### <a name="cause"></a>原因

多种多样。

### <a name="resolution"></a>解决方法

运行以下命令，使区域联机：

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>问题：区域具有相同的开始/结束键

### <a name="cause"></a>原因

多种多样。

### <a name="resolution"></a>解决方法

手动合并这些重叠的区域。 转到 HBase HMaster Web UI 表部分，选择有问题的表链接。 你将看到属于该表的每个区域的开始键/结束键。 然后合并这些重叠的区域。 在 HBase shell 中执行 `merge_region 'xxxxxxxx','yyyyyyy', true`。 例如：

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

在这种情况下，需要合并 RegionA 和 RegionC，获取键范围与 RegionB 相同的 RegionD，然后合并 RegionB 和 RegionD。 xxxxxxx 和 yyyyyy 是每个区域名称末尾的哈希字符串。 此处请注意，不要合并两个非连续区域。 每次合并后（例如，合并 A 和 C），HBase 将在 RegionD 上启动压缩。 等待压缩完成，然后与 RegionD 进行另一次合并。 可以在 HBase HMaster UI 中该区域服务器的页面上找到压缩状态。

---

## <a name="issue-cant-load-regioninfo"></a>问题：无法加载 `.regioninfo`

无法加载区域 `/hbase/data/default/tablex/regiony` 的 `.regioninfo`。

### <a name="cause"></a>原因

原因很可能是 RegionServer 崩溃或 VM 重新启动时部分删除了区域。 目前，Azure 存储是一个平面 Blob 文件系统，某些文件操作不是原子性的。

### <a name="resolution"></a>解决方法

手动清理这些剩余文件和文件夹：

1. 执行 `hdfs dfs -ls /hbase/data/default/tablex/regiony` 检查其中仍然包含哪些文件夹/文件。

1. 执行 `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` 删除所有子文件/文件夹。

1. 执行 `hdfs dfs -rmr /hbase/data/default/tablex/regiony` 删除区域文件夹。

---

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
