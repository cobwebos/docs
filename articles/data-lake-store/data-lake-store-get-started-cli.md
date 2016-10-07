<properties
   pageTitle="通过跨平台命令行界面实现 Data Lake Store 入门 | Microsoft Azure"
   description="使用 Azure 跨平台命令行来创建 Data Lake Store 帐户以及执行基本操作"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# 通过 Azure 命令行实现 Azure Data Lake Store 入门

> [AZURE.SELECTOR]
- [门户](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

了解如何使用 Azure 命令行界面来创建 Azure Data Lake Store 帐户以及执行基本操作，如创建文件夹、上传和下载数据文件、删除帐户等。有关 Data Lake Store 的详细信息，请参阅 [Data Lake Store 概述](data-lake-store-overview.md)。

Azure CLI 是以 Node.js 实现的。可以在支持 Node.js 的任意平台（包括 Windows、Mac 和 Linux）上使用它。Azure CLI 是开放源代码。在 GitHub 中管理源代码（网址为 <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>）。本文仅介绍如何配合使用 Azure CLI 和 Data Lake Store。有关如何使用 Azure CLI 的一般指导，请参阅[如何使用 Azure CLI][azure-command-line-tools]。


##先决条件

在开始阅读本文前，你必须具有：

- **一个 Azure 订阅**。请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
- **Azure CLI** - 有关安装和配置信息，请参阅[安装和配置 Azure CLI](../xplat-cli-install.md)。安装 CLI 后，确保重启计算机。

##登录到你的 Azure 订阅

按照[从 Azure 命令行接口 (Azure CLI) 连接到 Azure 订阅](../xplat-cli-connect.md)中所述的步骤，使用__登录__方法连接到订阅。


## 创建 Azure Data Lake Store 帐户

打开命令提示符、shell 或终端会话，运行以下命令。

1. 登录到 Azure 订阅：

		azure login

	系统会提示打开一个网页，输入身份验证代码。按照网页中的说明，登录到 Azure 订阅。

2. 使用以下命令切换到 Azure Resource Manager 模式：

		azure config mode arm


3. 列出帐户的 Azure 订阅。

		azure account list


4. 如果有多个 Azure 订阅，可以使用以下命令来设置 Azure CLI 命令要使用的订阅：

		azure account set <subscriptionname>

5. 创建新的资源组。在以下命令中，提供要使用的参数值。

		azure group create <resourceGroup> <location>

	如果位置名称包含空格，请将其用引号引起来。例如“美国东部 2”。

5. 创建 Data Lake Store 帐户。

		azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## 在 Data Lake Store 中创建文件夹

可以在 Azure Data Lake Store 帐户下创建文件夹，用于管理和存储数据。使用以下命令，在 Data Lake Store 的根目录下创建一个名为“mynewfolder”的文件夹。

	azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

例如：

	azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## 将数据上传到 Data Lake Store

可以直接将数据上传到 Data Lake Store 的根级别，也可以上传到在帐户中创建的文件夹。下面的代码片段演示了如何将一些示例数据上传到上一节中创建的文件夹 (**mynewfolder**)。

如果正在查找一些示例数据进行上传，可以从 [Azure Data Lake Git 存储库](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)获取 **Ambulance Data** 文件夹。下载文件，将其存储在计算机的本地目录，如 C:\\sampledata。

	azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

例如：

	azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## 列出 Data Lake Store 中的文件

使用以下命令列出 Data Lake Store 帐户中的文件。

	azure datalake store filesystem list <dataLakeStoreAccountName> <path>

例如：

	azure datalake store filesystem list mynewdatalakestore /mynewfolder

命令的输出如下所示：

	info:    Executing command datalake store filesystem list
	data:    accessTime: 1446245025257
	data:    blockSize: 268435456
	data:    group: NotSupportYet
	data:    length: 1589881
	data:    modificationTime: 1446245105763
	data:    owner: NotSupportYet
	data:    pathSuffix: vehicle1_09142014.csv
	data:    permission: 777
	data:    replication: 0
	data:    type: FILE
	data:    ------------------------------------------------------------------------------------
	info:    datalake store filesystem list command OK

## 重命名、下载以及删除 Data Lake Store 中的数据

* 使用以下命令，**重命名文件**：

    	azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

	例如：

		azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* 使用以下命令，**下载文件**。请确保指定的目标路径已存在。

		azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

	例如：

		azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* 使用以下命令，**删除文件**：

		azure datalake store filesystem delete <dataLakeStoreAccountName> <path>

	例如：

		azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv

	出现提示时，输入 **Y** 删除项目。

## 查看 Data Lake Store 中文件夹的访问控制列表

使用以下命令查看 Data Lake Store 文件夹中的 ACL。当前版本只允许在 Data Lake Store 的根目录中设置 ACL。因此，下面的路径参数将始终为根路径 (/)。

	azure datalake store permissions show <dataLakeStoreName> <path>

例如：

	azure datalake store permissions show mynewdatalakestore /


## 删除 Data Lake Store 帐户

使用以下命令删除 Data Lake Store 帐户。

	azure datalake store account delete <dataLakeStoreAccountName>

例如：

	azure datalake store account delete mynewdatalakestore

出现提示时，输入 **Y** 删除帐户。


## 后续步骤

- [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
- [配合使用 Azure Data Lake Analytics 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [配合使用 Azure HDInsight 和 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md

<!---HONumber=AcomDC_0921_2016-->