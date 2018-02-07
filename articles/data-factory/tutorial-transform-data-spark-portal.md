---
title: "在 Azure 数据工厂中使用 Spark 转换数据 | Microsoft Docs"
description: "本教程提供有关在 Azure 数据工厂中使用 Spark 活动转换数据的分步说明。"
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: c2ec6706c92f229bb05ad9a19246c6ffe5f615c9
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用 Spark 活动转换云中的数据
本教程使用 Azure 门户创建一个数据工厂管道，该管道可以使用 Spark 活动和按需 HDInsight 链接服务转换数据。 在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。 
> * 创建包含“Spark”活动的管道
> * 触发管道运行
> * 监视管道运行。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 文档](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件
* **Azure 存储帐户**。 创建 Python 脚本和输入文件，并将其上传到 Azure 存储。 Spark 程序的输出存储在此存储帐户中。 按需 Spark 群集使用相同的存储帐户作为其主存储。  
* **Azure PowerShell**。 遵循[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的说明。


### <a name="upload-python-script-to-your-blob-storage-account"></a>将 Python 脚本上传到 Blob 存储帐户
1. 创建包含以下内容的名为 **WordCount_Spark.py** 的 Python 文件： 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. 将 **&lt;storageAccountName&gt;** 替换为 Azure 存储帐户的名称。 然后保存文件。 
3. 在 Azure Blob 存储中，创建名为 **adftutorial** 的容器（如果尚不存在）。 
4. 创建名为 **spark** 的文件夹。
5. 在 **spark** 文件夹中创建名为 **script** 的子文件夹。 
6. 将 **WordCount_Spark.py** 文件上传到 **script** 子文件夹。 


### <a name="upload-the-input-file"></a>上传输入文件
1. 创建包含一些文本的名为 **minecraftstory.txt** 的文件。 Spark 程序会统计此文本中的单词数量。 
2. 在 `spark` 文件夹中创建名为 `inputfiles` 的子文件夹。 
3. 将 `minecraftstory.txt` 上传到 `inputfiles` 子文件夹。 

## <a name="create-a-data-factory"></a>创建数据工厂

1. 在左侧菜单中单击“新建”，并依次单击“数据 + 分析”、“数据工厂”。 
   
   ![新建 -> DataFactory](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页中，输入 **ADFTutorialDataFactory** 作为**名称**。 
      
     ![“新建数据工厂”页](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Azure 数据工厂的名称必须 **全局唯一**。 如果看到名称字段的以下错误，请更改数据工厂的名称（例如，改为 yournameADFTutorialDataFactory）。 有关数据工厂项目命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
     ![名称不可用 - 错误](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. 选择要在其中创建数据工厂的 Azure **订阅**。 
4. 对于**资源组**，请执行以下步骤之一：
     
      - 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 
      - 选择“新建”，并输入资源组的名称。   
         
      本快速入门中的一些步骤假定使用 **ADFTutorialResourceGroup** 作为资源组名称。 若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
4. 选择“V2 (预览)”作为**版本**。
5. 选择数据工厂的**位置**。 目前，数据工厂 V2 仅允许在“美国东部”、“美国东部 2”和“西欧”区域中创建数据工厂。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。
6. 选择“固定到仪表板”。     
7. 单击“创建”。
8. 在仪表板上，会看到状态为“正在部署数据工厂”的以下磁贴。 

    ![“正在部署数据工厂”磁贴](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
9. 创建完成后，会显示图中所示的“数据工厂”页。
   
    ![数据工厂主页](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)
10. 单击“创作和监视”磁贴，在单独的选项卡中启动数据工厂 UI 应用程序。

## <a name="create-linked-services"></a>创建链接服务
在本部分中创作两个链接服务： 
    
- 一个用于将 Azure 存储帐户链接到数据工厂的 **Azure 存储链接服务**。 按需 HDInsight 群集使用此存储。 此存储还包含要执行的 Spark 脚本。 
- 一个**按需 HDInsight 链接服务**。 Azure 数据工厂自动创建 HDInsight 群集，运行 Spark 程序，然后在 HDInsight 群集空闲预配置的时间后将其删除。 

### <a name="create-an-azure-storage-linked-service"></a>创建 Azure 存储链接服务

1. 在“入门”页的左侧面板中，切换到“编辑”选项卡，如下图所示： 

    ![“创建管道”磁贴](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. 单击窗口底部的“连接”，然后单击“+ 新建”。 

    ![“新建连接”按钮](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. 在“新建链接服务”窗口中，选择“Azure Blob 存储”，然后单击“继续”。 

    ![选择“Azure Blob 存储”](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. 选择自己的 **Azure 存储帐户名称**，然后单击“保存”。 

    ![指定 Blob 存储帐户](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>创建按需 HDInsight 链接服务

1. 再次单击“+ 新建”按钮创建另一个链接服务。 
2. 在“新建链接服务”窗口中，选择“Azure HDInsight”，然后单击“继续”。 

    ![选择“Azure HDInsight”](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. 在“新建链接服务”窗口中执行以下步骤： 

    1. 在“名称”中输入 **AzureHDInsightLinkedService**。
    2. 确认为“类型”选择了“按需 HDInsight”。
    3. 为“Azure 存储链接服务”选择“AzureStorage1”。 前面已创建此链接服务。 如果使用了其他名称，请为此字段指定正确的名称。 
    4. 选择“spark”作为**群集类型**。
    5. 输入有权创建 HDInsight 群集的**服务主体的 ID**。 此服务主体需是订阅“参与者”角色的成员，或创建群集的资源组的成员。 有关详细信息，请参阅[创建 Azure Active Directory 应用程序和服务主体](../azure-resource-manager/resource-group-create-service-principal-portal.md)。
    6. 输入**服务主体密钥**。 
    7. 选择为**资源组**创建数据工厂时使用的同一资源组。 将在此资源组中创建 Spark 群集。 
    8. 展开“OS 类型”。
    9. 输入**群集用户**的**名称**。 
    10. 输入该用户的**密码**。 
    11. 单击“ **保存**”。 

        ![HDInsight 链接服务设置](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight 会限制可在其支持的每个 Azure 区域中使用的核心总数。 对于按需 HDInsight 链接服务，将在 Azure 存储用作其主存储的同一位置创建 HDInsight 群集。 请确保有足够的核心配额，以便能够成功创建群集。 有关详细信息，请参阅[使用 Hadoop、Spark、Kafka 等在 HDInsight 中设置群集](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。 

## <a name="create-a-pipeline"></a>创建管道

2. 单击“+”（加号）按钮，然后在菜单中单击“管道”。

    ![“新建管道”菜单](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
3. 在“活动”工具箱中展开“HDInsight”，将“Spark”活动从“活动”工具箱拖放到管道设计器图面。 

    ![拖放 Spark 活动](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
4. 在底部“Spark”活动窗口的属性中执行以下步骤： 

    1. 切换到“HDI 群集”选项卡。
    2. 选择在上一步骤中创建的 **AzureHDInsightLinkedService**。 
        
    ![指定 HDInsight 链接服务](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
5. 切换到“脚本/Jar”选项卡，然后执行以下步骤： 

    1. 为“作业链接服务”选择“AzureStorage1”。
    2. 单击“浏览存储”。 
    3. 导航到“adftutorial/spark/script folder”文件夹，选择“WordCount_Spark.py”，然后单击“完成”。      

    ![指定 Spark 脚本](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
6. 若要验证管道，请单击工具栏中的“验证”按钮。 单击**右箭头** (>>) 按钮关闭验证窗口。 
    
    ![“验证”按钮](./media/tutorial-transform-data-spark-portal/validate-button.png)
7. 单击“发布” 。 数据工厂 UI 会将实体（链接服务和管道）发布到 Azure 数据工厂服务。 
    
    ![发布按钮](./media/tutorial-transform-data-spark-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>触发管道运行
单击工具栏中的“触发器”，然后单击“立即触发”。 

![立即触发](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>监视管道运行

1. 切换到“监视”选项卡。确认可以看到一个管道运行。 创建 Spark 群集大约需要 20 分钟。 

    ![监视管道运行](./media/tutorial-transform-data-spark-portal/monitor-tab.png)
2. 单击“刷新”定期检查运行管道的状态。 

    ![管道运行状态](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png)
1. 若要查看与管道运行相关联的活动运行，请单击“操作”列中的“查看活动运行”操作。 单击顶部的“管道”链接可以切换回到管道运行视图。

    ![“活动运行”视图](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>验证输出
验证 adftutorial 容器的 spark/otuputfiles/wordcount 文件夹中是否创建了一个输出文件。 

![验证输出](./media/tutorial-transform-data-spark-portal/verity-output.png)

该文件应包含输入文本文件中的每个单词，以及该单词在该文件中出现的次数。 例如： 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>后续步骤
此示例中的管道使用 Spark 活动和按需 HDInsight 链接服务转换数据。 你已了解如何： 

> [!div class="checklist"]
> * 创建数据工厂。 
> * 创建包含“Spark”活动的管道
> * 触发管道运行
> * 监视管道运行。

继续学习下一篇教程，了解如何通过运行 Azure HDInsight 群集上的 Hive 脚本，转换虚拟网络中的数据。 

> [!div class="nextstepaction"]
> [教程：在 Azure 虚拟网络中使用 Hive 转换数据](tutorial-transform-data-hive-virtual-network-portal.md)。





