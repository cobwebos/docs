---
title: hbase hbck 在 Azure HDInsight 中返回不一致
description: hbase hbck 在 Azure HDInsight 中返回不一致
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887319"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>方案：在 Azure HDInsight 中 `hbase hbck` 命令返回不一致

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue-region-is-not-in-hbasemeta"></a>问题：区域不处于 `hbase:meta`

HDFS 上的区域 xxx，但未列在 `hbase:meta` 中或部署在任何区域服务器上。

### <a name="cause"></a>原因

不定。

### <a name="resolution"></a>分辨率

1. 通过运行以下内容修复元表：

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. 通过运行将区域分配到 RegionServers：

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>问题：区域处于脱机状态

未在任何 RegionServer 上部署区域 xxx。 这意味着区域处于 `hbase:meta`，但处于脱机状态。

### <a name="cause"></a>原因

不定。

### <a name="resolution"></a>分辨率

通过运行以下操作使区域联机：

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>问题：区域具有相同的开始/结束键

### <a name="cause"></a>原因

不定。

### <a name="resolution"></a>分辨率

手动合并这些重叠区域。 请参阅 HBase HMaster Web UI 表部分，选择表链接，该链接有问题。 你将看到属于该表的每个区域的开始键/结束键。 然后合并这些重叠区域。 在 HBase shell 中，执行 `merge_region 'xxxxxxxx','yyyyyyy', true`。 例如：

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

在这种情况下，需要合并 RegionA 和 RegionC，并获取与 RegionB 相同的键范围的 RegionD，然后再合并 RegionB 和 RegionD。 xxxxxxx-xxxx ... 和 yyyyyy 是每个区域名称末尾的哈希字符串。 请注意不要合并两个不连续的区域。 每次合并后（如 merge A 和 C）在 RegionD 上开始压缩。 等待压缩完成，再执行与 RegionD 的另一个合并。 可以在 HBase HMaster UI 中的该区域服务器页面上找到压缩状态。

---

## <a name="issue-cant-load-regioninfo"></a>问题：无法加载 `.regioninfo`

无法为区域 `/hbase/data/default/tablex/regiony`加载 `.regioninfo`。

### <a name="cause"></a>原因

这很可能是由于 RegionServer 崩溃或 VM 重新启动时区域部分删除。 目前，Azure 存储是平面 blob 文件系统，某些文件操作不是原子的。

### <a name="resolution"></a>分辨率

手动清理这些剩余的文件和文件夹：

1. 执行 `hdfs dfs -ls /hbase/data/default/tablex/regiony` 以检查哪些文件夹/文件在其下。

1. 执行 `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` 以删除所有子文件/文件夹

1. 执行 `hdfs dfs -rmr /hbase/data/default/tablex/regiony` 以删除区域文件夹。

---

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 连接[@AzureSupport](https://twitter.com/azuresupport) -用于改善客户体验的官方 Microsoft Azure 帐户。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
