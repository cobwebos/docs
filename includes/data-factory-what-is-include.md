
<!--
    As of 2017/10/06, this 'include' file is meant to replace the first paragraph of plain text that is duplicated at the top inside every tutorial-*.md article in azure-docs-pr/articles/data-factory/.

    This 'include' file is basically one paragraph.
    It explains what Azure Data Factory is, to someone who knows nothing about ADF.
-->

Azure 数据工厂是数据集成服务。 它可以在云中创建数据驱动型工作流。 工作流作为一个或多个管道来实现。 管道可协调和自动完成数据移动和数据转换。 管道可对数据执行以下顺序的操作：

1. 从不同数据存储引入数据。
2. 使用计算服务（如下）转换或处理数据：
    - Azure HDInsight Hadoop
    - Spark
    - Azure Data Lake Analytics
    - Azure 机器学习
3. 将输出数据发布到数据存储。
    - 发布目标可以是 Azure SQL 数据仓库，目的是供商业智能 (BI) 应用程序使用。 

可以使用数据工厂来计划管道。

