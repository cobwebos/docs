---
title: Azure 机器学习服务的已知问题和故障排除
description: 获取已知问题、解决方法和故障排除的列表
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: d84040dc440c373ae9bae6dbac7a95109a387ba7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162740"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Azure 机器学习服务的已知问题和故障排除
 
本文可帮助你查找和更正使用 Azure 机器学习服务时遇到的错误或失败。 


## <a name="databricks"></a>Databricks

Databricks 和 Azure 机器学习问题。

1. Databricks 群集建议：
   
   使用 Python 3 创建版本为 4.x 的 Azure Databricks 群集。 我们建议使用高并发群集。
 
1. 安装更多程序包时，AML SDK 会在 Databricks 上安装失败。

   某些包（如 `psutil upgrade libs`）可能会导致冲突。 为了避免安装错误，请通过冻结 lib 版本来安装包。 此问题与 Databricks 有关，与 AML SDK 无关。 示例：
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="gather-diagnostics-information"></a>收集诊断信息
如果在请求帮助时可以提供诊断信息，有时会很有帮助。 下面是日志文件所在的位置：

## <a name="resource-quotas"></a>资源配额

了解使用 Azure 机器学习时可能遇到的[资源配额](how-to-manage-quotas.md)。

## <a name="get-more-support"></a>获取更多支持

可以通过技术支持、论坛等提交支持请求并获取帮助。 [了解详细信息...](support-for-aml-services.md)
