---
title: "REST API：Azure Data Lake Store 中的文件系统操作 | Microsoft Docs"
description: "使用 WebHDFS REST API 对 Data Lake Store 执行文件系统操作"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 62cfc713c3b7ce8f7c0a7ff4dd5e80802f1843b7
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-rest-api"></a>使用 REST API 在 Azure Data Lake Store 上进行的文件系统操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

本文介绍如何使用 WebHDFS REST API 和 Data Lake Store REST API 对 Azure Data Lake Store 执行文件系统操作。 若要了解如何使用 REST API 在 Data Lake Store 上执行帐户管理操作，请参阅[在 Data Lake Store 上使用 REST API 进行的帐户管理操作](data-lake-store-get-started-rest-api.md)。

## <a name="prerequisites"></a>先决条件
* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure Data Lake Store 帐户**。 遵照[通过 Azure 门户使用 Azure Data Lake Store](data-lake-store-get-started-portal.md) 中的说明。

* **[cURL](http://curl.haxx.se/)**。 本文使用 cURL 演示如何对 Data Lake Store 帐户进行 REST API 调用。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>如何使用 Azure Active Directory 进行身份验证？
可以通过两种方法使用 Azure Active Directory 进行身份验证。

* 有关应用程序的最终用户身份验证（交互式），请参阅[使用 .NET SDK 通过 Data Lake Store 进行最终用户身份验证](data-lake-store-end-user-authenticate-rest-api.md)。
* 有关应用程序的服务到服务身份验证（非交互式），请参阅[使用 .NET SDK 通过 Data Lake Store 进行服务到服务身份验证](data-lake-store-service-to-service-authenticate-rest-api.md)。


## <a name="create-folders"></a>创建文件夹
此操作基于 [此处](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory)定义的 WebHDFS REST API 调用。

使用以下 cURL 命令。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

在上述命令中，将 \<`REDACTED`\> 替换为前面检索的授权令牌。 此命令在 Data Lake Store 帐户的根文件夹下创建名为 **mytempdir** 的目录。

如果操作成功完成，则会看到类似于以下代码片段的响应：

    {"boolean":true}

## <a name="list-folders"></a>列出文件夹
此操作基于 [此处](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory)定义的 WebHDFS REST API 调用。

使用以下 cURL 命令。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

在上述命令中，将 \<`REDACTED`\> 替换为前面检索的授权令牌。

如果操作成功完成，则会看到类似于以下代码片段的响应：

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>上传数据
此操作基于 [此处](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File)定义的 WebHDFS REST API 调用。

使用以下 cURL 命令。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

在上述语法中，**-T** 参数是要上传的文件的位置。

输出与以下代码片段类似：
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>读取数据
此操作基于 [此处](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File)定义的 WebHDFS REST API 调用。

从 Data Lake Store 帐户中读取数据的过程由两个步骤组成。

* 首先，针对终结点 `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`提交 GET 请求。 此调用返回要将下一个 GET 请求提交到的位置。
* 然后，针对终结点 `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true` 提交 GET 请求。 此调用显示文件的内容。

但是，由于第一和第二个步骤之间的输入参数没有任何差异，因此可以使用 `-L` 参数来提交第一个请求。 `-L` 选项本质上是将两个请求合并成一个，让 cURL 对新位置重做请求。 最后会显示所有请求调用的输出，如以下代码片段所示。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

应会看到类似于以下代码片段的输出：

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>重命名文件
此操作基于 [此处](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory)定义的 WebHDFS REST API 调用。

使用以下 cURL 命令重命名文件。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

应会看到类似于以下代码片段的输出：

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>删除文件
此操作基于 [此处](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory)定义的 WebHDFS REST API 调用。

使用以下 cURL 命令删除文件。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

应该看到如下输出：

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>后续步骤
* [Data Lake Store 上的帐户管理操作（使用 REST API）](data-lake-store-get-started-rest-api.md)

## <a name="see-also"></a>另请参阅
* [Azure Data Lake Store REST API 参考](https://docs.microsoft.com/rest/api/datalakestore/)
* [Open Source Big Data applications compatible with Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)

