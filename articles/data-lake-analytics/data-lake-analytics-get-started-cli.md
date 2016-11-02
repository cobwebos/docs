<properties 
   pageTitle="通过 Azure 命令行接口开始使用 Azure Data Lake Analytics | Microsoft Azure" 
   description="了解如何使用 Azure 命令行接口创建 Data Lake Store 帐户，使用 U-SQL 创建 Data Lake Analytics 作业，并提交该作业。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# 教程：通过 Azure 命令行接口 (CLI) 开始使用 Azure Data Lake Analytics

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


了解如何使用 Azure CLI 来创建 Azure Data Lake Analytics 帐户，在 [U-SQL](data-lake-analytics-u-sql-get-started.md) 定义 Data Lake Analytics 作业，并将作业提交至 Data Lake Analytics 帐户。有关 Data Lake Analytics 的详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

在本教程中，用户将开发一项作业，它能读取制表符分隔值 (TSV) 文件，并将其转换为逗号分隔值 (CSV) 文件。若要通过其他支持的工具来完成此教程，请单击本节顶部的选项卡。

##先决条件

在开始阅读本教程前，你必须具有：

- **一个 Azure 订阅**。请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
- **Azure CLI**。请参阅[安装和配置 Azure CLI](../xplat-cli-install.md)。
	- 若要完成此演示，请下载并安装**预发行版** [Azure CLI 工具](https://github.com/MicrosoftBigData/AzureDataLake/releases)。
- **身份验证**，请使用以下命令：

		azure login
	有关使用公司或学校帐户进行身份验证的详细信息，请参阅[从 Azure CLI 连接到 Azure 订阅](../xplat-cli-connect.md)。
- 使用以下命令**切换到 Azure 资源管理器模式**：

		azure config mode arm
		
## 创建 Data Lake Analytics 帐户

在运行任何作业之前，首先必须拥有 Data Lake Analytics 帐户。若要创建 Data Lake Analytics 帐户，必须指定以下各项：

- **Azure 资源组**：必须在 Azure 资源组中创建一个 Data Lake 分析帐户。可以使用 [Azure Resource Manager](../resource-group-overview.md) 以组的方式处理应用程序中的资源。你可以通过一个协调的操作为应用程序部署、更新或删除所有资源。

	枚举订阅中的资源组：
    
    	azure group list 
    
	若要创建新的资源组：

		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Data Lake Analytics 帐户名**
- **位置**：支持 Data Lake Analytics 的 Azure 数据中心之一。
- **默认 Data Lake 帐户**：每个 Data Lake Analytics 帐户都有一个默认 Data Lake 帐户。

	列出现有 Data Lake 帐户：
	
		azure datalake store account list

	创建新的 Data Lake 帐户：

		azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

	> [AZURE.NOTE] Data Lake 帐户名必须仅包含小写字母和数字。



**创建 Data Lake Analytics 帐户**

		azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

		azure datalake analytics account list
		azure datalake analytics account show "<Data Lake Analytics Account Name>"			

![Data Lake Analytics 显示帐户](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] Data Lake Analytics 帐户名必须仅包含小写字母和数字。


## 将数据上传到 Data Lake Store

在本教程中，用户将处理一些搜索日志。搜索日志可以存储在 Data Lake Store 或 Azure Blob 存储中。

Azure 门户提供一个用户界面，可将一些示例数据复制到默认 Data Lake 帐户，包括搜索日志文件。要将数据上传至默认 Data Lake Store 帐户，请参阅[准备源数据](data-lake-analytics-get-started-portal.md#prepare-source-data)。

若要使用 CLI 上传文件，请使用以下命令：

  	azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
  	azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Data Lake Analytics 还可以访问 Azure Blob 存储。有关将数据上传到 Azure Blob 存储的详细信息，请参阅[将 Azure CLI 用于 Azure 存储](../storage/storage-azure-cli.md)。

## 提交 Data Lake Analytics 作业

Data Lake Analytics 作业使用 U-SQL 语言编写而成。若要了解有关 U-SQL 的详细信息，请参阅 [U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)和 [U-SQL 语言参考](http://go.microsoft.com/fwlink/?LinkId=691348)。

**创建 Data Lake Analytics 作业脚本**

- 使用下列 U-SQL 脚本创建文本文件，并将其保存至工作站：

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	此 U-SQL 脚本通过 **Extractors.Tsv()** 读取源数据文件，然后通过 **Outputters.Csv()** 创建 csv 文件。
    
    除非将源文件复制到其他位置，否则不要修改这两条路径。如果输出文件夹不存在，Data Lake Analytics 将创建一个。
	
	对于存储在默认 Data Lake 帐户中的文件而言，使用相对路径更为简单。也可使用绝对路径。例如
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    必须使用绝对路径来访问链接的存储帐户中的文件。链接的 Azure 存储帐户中所储存文件的语法是：
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] 当前不支持对含公共 blob 的 Azure Blob 容器或公共容器的访问权限。

	
**提交作业**


	azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
可以使用以下命令列出作业、获取作业详细信息和取消作业：

  	azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
  	azure datalake analytics job list "<Data Lake Analytics Account Name>"
	azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

作业完成后，可以使用以下 cmdlet 列出该文件，并下载它：
	
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
	azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
	azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## 另请参阅

- 若要了解使用其他工具来完成此教程，请单击页面顶部的选项卡选择器。
- 若要查看更复杂的查询，请参阅[使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)。
- 若要着手开发 U-SQL 应用程序，请参阅[使用 Data Lake Tools for Visual Studio 开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
- 若要了解 U-SQL，请参阅 [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)。
- 有关管理任务，请参阅[使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)。
- 有关 Data Lake Analytics 的概述，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

<!---HONumber=AcomDC_0921_2016-->