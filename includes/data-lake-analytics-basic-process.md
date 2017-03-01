**基本 Data Lake Analytics 过程：**

![Azure Data Lake Analytics 流程图](./media/data-lake-analytics-basic-process-include/data-lake-analytics-process.png)

1. 创建 Data Lake Analytics 帐户。
2. 准备源数据。 Data Lake Analytic 作业可从 Azure Data Lake Store 帐户或 Azure Blob 存储帐户读取数据。   
3. 开发 U-SQL 脚本。
4. 将作业（U-SQL 脚本）提交到 Data Lake Analytics 帐户。 该作业将读取源数据，根据 U-SQL 脚本的指示处理数据，然后将输出保存到 Data Lake Store 帐户或 Blob 存储帐户。



<!--HONumber=Jan17_HO3-->


