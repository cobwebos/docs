---
title: 在 Azure Synapse 分析中管理 Apache Spark 的库
description: 了解如何添加和管理 Azure Synapse 分析中 Apache Spark 使用的库。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: euang
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: b815dd9218ac395786e7ab7ed3a02ba440fe2807
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260166"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>在 Azure Synapse 分析中管理 Apache Spark 的库

库提供你可能想要包括在程序或项目中的可重用代码。 若要使第三方或本地生成的代码可用于你的应用程序，你可以将库安装到你的一个 Spark 池 (预览) 。 为 Spark 池安装库后，将可用于使用同一池的所有会话。 

## <a name="default-installation"></a>默认安装
Azure Synapse Analytics 中的 Apache Spark 包含完整的 Anacondas 安装和其他库。 可以在 [Apache Spark 版本支持](apache-spark-version-support.md)中找到 "完整库" 列表。 

当 Spark 实例启动时，将自动包含这些库。 可以在 Spark 池 (预览) 级别添加其他 Python 和自定义内置包。


## <a name="manage-python-packages"></a>管理 Python 包
确定要用于 Spark 应用程序的库后，可以将它们安装到 Spark 池中 (预览) 。 

 可使用命令) 的 *requirements.txt* 文件 (输出来 `pip freeze` 升级虚拟环境。 在启动池时，将从 PyPi 下载此文件中列出的用于安装或升级的包。 每次从该 Spark 池中创建 Spark 实例时，都会使用此要求文件。

> [!IMPORTANT]
> - 如果要安装的包很大或需要很长时间才能安装，这会影响 Spark 实例的启动时间。
> - 不支持在安装时需要编译器支持的包（如 GCC）。
> - 包不能降级，只能进行添加或升级。

### <a name="requirements-format"></a>需求格式

以下代码片段显示了要求文件的格式。 PyPi 包名称与精确版本一起列出。 此文件遵循 [pip 冻结](https://pip.pypa.io/en/stable/reference/pip_freeze/) 参考文档中所述的格式。 此示例固定特定版本。 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>安装 Python 包
开发 Spark 应用程序时，可能会发现需要更新现有的或安装新库。 库可以在创建池期间或之后进行更新。

#### <a name="install-packages-during-pool-creation"></a>在创建池期间安装包
若要在创建池的过程中将库安装到 Spark 池 (预览) ：
   
1. 从 Azure 门户导航到 Azure Synapse Analytics 工作区。
   
2. 选择 " **创建 Apache Spark 池** "，然后选择 " **其他设置** " 选项卡。 
   
3. 使用页面的 " **包** " 部分中的文件选择器上载环境配置文件。 
   
![创建池时添加 Python 库](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "添加 Python 库")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>从 Synapse 工作区安装包
若要更新或添加其他库到 Spark 池 (预览) 从 Azure Synapse Analytics 门户：

1.  从 Azure 门户导航到 Azure Synapse Analytics 工作区。
   
2.  从 Azure 门户启动 Azure Synapse Analytics 工作区。

3.  从主导航面板中选择 " **管理** "，然后选择 " **Apache Spark 池**"。
   
4. 选择单个 Spark 池，并使用页面的 "  **包** " 部分中的文件选择器上载环境配置文件。

![在 synapse 中添加 Python 库](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png "添加 Python 库")
   
#### <a name="install-packages-from-the-azure-portal"></a>从 Azure 门户安装包
若要将库安装到 Spark 池 (预览直接从 Azure 门户中) ：
   
 1. 从 Azure 门户导航到 Azure Synapse Analytics 工作区。
   
 2. 在 " **Synapse 资源** " 部分下，选择 " **Apache Spark 池** " 选项卡，然后从列表中选择一个 Spark 池。
   
 3. 从 Spark 池的 "**设置**" 部分中选择 "**包**"。 

 4. 使用文件选择器上载环境配置文件。

![添加 Python 库](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "添加 Python 库")

### <a name="verify-installed-libraries"></a>验证安装的库

若要验证是否安装了正确的库版本，请运行以下代码

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```
### <a name="update-python-packages"></a>更新 Python 包
在会话之间随时可以添加或修改包。 上传新的包配置文件时，将覆盖现有的包和版本。  

更新或卸载库：
1. 导航到 Azure Synapse Analytics 工作区。 

2. 使用 "Azure 门户" 或 "Azure Synapse" 工作区，选择要更新的 **Apache Spark 池** 。

3. 导航到 " **包** " 部分，并上传新的环境配置文件
   
4. 保存更改后，你将需要结束活动会话并等待池重新启动。 或者，您可以通过选中相应的复选框强制 **新的设置**来强制结束活动会话。

![添加 Python 库](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "添加 Python 库")
   

> [!IMPORTANT]
> 通过选择 **强制新设置**的选项，你将结束所选 Spark 池的所有当前会话。 会话结束后，需要等待池重新启动。 
>
> 如果未选中此设置，则需要等待当前 Spark 会话手动结束或停止。 会话结束后，需要重新启动池。 


## <a name="manage-a-python-wheel"></a>管理 Python 轮

### <a name="install-a-custom-wheel-file"></a>安装自定义滚轮文件
可以通过将所有轮文件上传到与 Synapse 工作区链接的 Azure Data Lake Storage (Gen2) 帐户，将自定义内置轮包安装到 Apache Spark 池。 

应将这些文件上传到存储帐户的默认容器中的以下路径： 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>sparkpools/<pool_name>libraries/python/
```

>[!IMPORTANT]
>可以在会话之间添加或修改自定义包。 但是，需要等待池和会话重新启动才能看到更新的包。

## <a name="next-steps"></a>后续步骤
- 查看默认库： [Apache Spark 版本支持](apache-spark-version-support.md)
