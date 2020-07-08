---
title: 在 Azure Synapse Analytics 中添加和管理 Apache Spark 库
description: 了解如何添加和管理 Azure Synapse 分析中 Apache Spark 使用的库。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cf78a2f7d909fb260c5ff99f80c9d1482f2cd08b
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027301"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中添加和管理 Apache Spark 库

Apache Spark 依赖于许多库来提供功能。 可以扩充这些库，或将其替换为其他库或旧版本的更新版本。

可以在 Spark 池（预览版）级别添加 Python 包，也可以在 Spark 作业定义级别添加基于 .jar 的包。

## <a name="adding-or-updating-python-libraries"></a>添加或更新 Python 库

Azure Synapse Analytics 中的 Apache Spark 包含完整的 Anacondas 安装和其他库。 可以在[Apache Spark 版本支持](apache-spark-version-support.md)中找到 "完整库" 列表。

当 Spark 实例启动时，将使用此安装作为基础来创建新的虚拟环境。 此外，还可以使用*requirements.txt*文件（命令的输出 `pip freeze` ）来升级虚拟环境。 在群集启动时，将从 PyPi 下载此文件中列出的用于安装或升级的包。 每次从该 Spark 池中创建 Spark 实例时，都会使用此要求文件。

> [!IMPORTANT]
>
> - 如果要安装的包很大或需要很长时间才能安装，这会影响 Spark 实例的启动时间。
> - 不支持在安装时需要编译器支持的包（如 GCC）。
> - 包不能降级，只能进行添加或升级。

### <a name="requirements-format"></a>需求格式

以下代码片段显示了要求文件的格式。 PyPi 包名称与精确版本一起列出。 此文件遵循[pip 冻结](https://pip.pypa.io/en/stable/reference/pip_freeze/)参考文档中所述的格式。 此示例固定特定版本。 你还可以在此文件中指定 "不大于" 和 "小于" 版本。

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Python 库用户界面

用于添加库的 UI 位于 Azure 门户上 "**创建 Apache Spark 池**" 页的 "**其他设置**" 选项卡中。

使用页面的 "**包**" 部分中的文件选择器上载环境配置文件。

![添加 Python 库](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "添加 Python 库")

### <a name="verifying-installed-libraries"></a>验证已安装的库

若要验证是否安装了正确的库版本，请运行以下代码

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 文档](https://spark.apache.org/docs/2.4.4/)
