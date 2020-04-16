---
title: 在 Azure 突触分析中添加和管理 Apache Spark 的库
description: 了解如何在 Azure 同步分析中添加和管理 Apache Spark 使用的库。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 83dfd1b4df37018329b5d7a707e9b65fdf0782a6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427830"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure 突触分析中添加和管理 Apache Spark 的库

Apache Spark 依赖于许多库来提供功能。 这些库可以增强或替换为其他库或旧库的更新版本。

可以在 Spark 池（预览）级别添加 Python 包，也可以在 Spark 作业定义级别添加基于 .jar 的包。

## <a name="adding-or-updating-python-libraries"></a>添加或更新 Python 库

Azure Synapse 分析中的 Apache Spark 具有完整的 Anacondas 安装以及其他库。 完整的库列表可以在[Apache Spark版本支持](apache-spark-version-support.md)中找到。

当 Spark 实例启动时，将使用此安装作为基础创建新的虚拟环境。 此外，*要求.txt*文件（来自命令的`pip freeze`输出）可用于升级虚拟环境。 此文件中列出的用于安装或升级的软件包在群集启动时从 PyPi 下载。 每次从 Spark 池创建 Spark 实例时，都会使用此要求文件。

> [!IMPORTANT]
>
> - 如果您要安装的包很大或安装时间过长，这会影响 Spark 实例的启动时间。
> - 不支持在安装时需要编译器支持的包，如 GCC。
> - 无法降级、仅添加或升级包。

### <a name="requirements-format"></a>需求格式

以下代码段显示需求文件的格式。 PyPi 包名称随确切版本一起列出。 此文件遵循[点冻结](https://pip.pypa.io/en/stable/reference/pip_freeze/)参考文档中所述的格式。 本示例固定特定版本。 您还可以在此文件中指定"不大于"和"小于"版本。

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Python 库用户界面

用于添加库的 UI 位于 Azure 门户上的"**创建 Apache Spark 池"** 页上的"**其他设置"** 选项卡中。

使用页面"**包"** 部分中的文件选择器上载环境配置文件。

![添加 Python 库](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "添加 Python 库")

## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [阿帕奇火花文档](https://spark.apache.org/docs/2.4.4/)
