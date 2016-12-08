---
title: "通过 REST API 开始使用 Data Lake Store | Microsoft 文档"
description: "使用 WebHDFS REST API 对 Data Lake Store 执行操作"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/21/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: c157da7bf53e2d0762624e8e71e56e956db04a24
ms.openlocfilehash: 57af74321c453733daadc1b295dd3df95d0fd052


---
# <a name="get-started-with-azure-data-lake-store-using-rest-apis"></a>通过 REST API 开始使用 Azure Data Lake Store
> [!div class="op_single_selector"]
> * [门户](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

本文介绍如何使用 WebHDFS REST API 和 Data Lake Store REST API 对 Azure Data Lake Store 执行帐户管理和文件系统操作。 Azure Data Lake Store 公开自身的 REST API 用于帐户管理操作。 但是，由于 Data Lake Store 与 HDFS 和 Hadoop 生态系统兼容，因此支持使用 WebHDFS REST API 执行文件系统操作。

> [!NOTE]
> 有关 Data Lake Store 的 REST API 支持的详细信息，请参阅 [Azure Data Lake Store REST API Reference](https://msdn.microsoft.com/library/mt693424.aspx)（Data Lake Store REST API 参考）。
> 
> 

## <a name="prerequisites"></a>先决条件
* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **创建 Azure Active Directory 应用程序**。 使用 Azure AD 应用程序对 Data Lake Store 应用程序进行 Azure AD 身份验证。 有不同的方式可进行 Azure AD 身份验证，即“最终用户身份验证”或“服务到服务身份验证”。 有关如何进行身份验证的说明和详细信息，请参阅[使用 Azure Active Directory 进行 Data Lake Store 身份验证](data-lake-store-authenticate-using-active-directory.md)。
* [cURL](http://curl.haxx.se/)。 本文使用 cURL 演示如何对 Data Lake Store 帐户进行 REST API 调用。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>如何使用 Azure Active Directory 进行身份验证？
可以通过两种方法使用 Azure Active Directory 进行身份验证。

### <a name="end-user-authentication-interactive"></a>最终用户身份验证（交互式）
在此方案中，应用程序提示用户登录，所有操作在用户的上下文中执行。 运行以下步骤进行交互式身份验证。

1. 通过应用程序将用户重定向至以下 URL：
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<REDIRECT-URI> 需要经过编码才能在 URL 中使用。 因此，对于 https://localhost，请使用 `https%3A%2F%2Flocalhost`）
   > 
   > 
   
    在本教程中，可以替换上述 URL 中的占位符值，并将此值粘贴到 Web 浏览器地址栏中。 随后用户将重定向，以便使用 Azure 登录名进行身份验证。 成功登录后，响应将显示在浏览器地址栏中。 响应格式如下：
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>
2. 捕获响应中的授权代码。 在本教程中，可以从 Web 浏览器的地址栏复制授权代码，然后在 POST 请求中将其传递给令牌终结点，如下所示：
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > 在此情况下，\<REDIRECT-URI> 不需要编码。
   > 
   > 
3. 响应是一个 JSON 对象，包含访问令牌（例如 `"access_token": "<ACCESS_TOKEN>"`）和刷新令牌（例如 `"refresh_token": "<REFRESH_TOKEN>"`）。 应用程序在访问 Azure Data Lake Store 时使用访问令牌，在访问令牌过期时使用刷新令牌获取另一个访问令牌。
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
4. 访问令牌过期时，可以使用刷新令牌请求新的访问令牌，如下所示：
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<CLIENT-ID> \
             -F refresh_token=<REFRESH-TOKEN>

有关交互式用户身份验证的详细信息，请参阅 [Authorization code grant flow](https://msdn.microsoft.com/library/azure/dn645542.aspx)（授权代码授予流）。

### <a name="service-to-service-authentication-non-interactive"></a>服务到服务身份验证（非交互式）
在此方案中，应用程序提供自身的凭据来执行操作。 为此，必须发出 POST 请求，如下所示。 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

此请求的输出包含授权令牌（在以下输出中以 `access-token` 表示），随后要连同 REST API 调用传递该令牌。 将此身份验证令牌保存在文本文件中；本文稍后要用到此令牌。

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

本文使用 **非交互式** 方法。 有关非交互式（服务到服务的调用）的详细信息，请参阅 [Service to service calls using credentials](https://msdn.microsoft.com/library/azure/dn645543.aspx)（使用凭据执行服务到服务的调用）。

## <a name="create-a-data-lake-store-account"></a>创建 Data Lake Store 帐户
此操作基于 [此处](https://msdn.microsoft.com/library/mt694078.aspx)定义的 REST API 调用。

使用以下 cURL 命令。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

在上述命令中，将 \<`REDACTED`\> 替换为前面检索的授权令牌。 此命令的请求有效负载包含在提供给上述 `-d` 参数的 **input.json** 文件中。 input.json 文件的内容如下所示：

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="create-folders-in-a-data-lake-store-account"></a>在 Data Lake Store 帐户中创建文件夹
此操作基于 [此处](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory)定义的 WebHDFS REST API 调用。

使用以下 cURL 命令。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

在上述命令中，将 \<`REDACTED`\> 替换为前面检索的授权令牌。 此命令在 Data Lake Store 帐户的根文件夹下创建名为 **mytempdir** 的目录。

如果操作成功完成，将看到类似于下面的响应：

    {"boolean":true}

## <a name="list-folders-in-a-data-lake-store-account"></a>列出 Data Lake Store 帐户中的文件夹
此操作基于 [此处](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory)定义的 WebHDFS REST API 调用。

使用以下 cURL 命令。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

在上述命令中，将 \<`REDACTED`\> 替换为前面检索的授权令牌。

如果操作成功完成，将看到类似于下面的响应：

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
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## <a name="upload-data-into-a-data-lake-store-account"></a>将数据上载到 Data Lake Store 帐户中
此操作基于 [此处](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File)定义的 WebHDFS REST API 调用。

使用 WebHDFS REST API 上载数据的过程由两个步骤组成，如下所述。

1. 在不发送要上载的文件数据的情况下提交 HTTP PUT 请求。 在以下命令中，将 **\<yourstorename>** 替换为 Data Lake Store 名称。
   
        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE
   
    此命令的输出包含临时重定向 URL，如下所示。
   
        HTTP/1.1 100 Continue
   
        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...
2. 现在，必须针对响应中为 **Location** 属性列出的 URL 提交另一个 HTTP PUT 请求。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。
   
        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true
   
    输出将如下所示：
   
        HTTP/1.1 100 Continue
   
        HTTP/1.1 201 Created
        ...
        ...

## <a name="read-data-from-a-data-lake-store-account"></a>从 Data Lake Store 帐户中读取数据
此操作基于 [此处](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File)定义的 WebHDFS REST API 调用。

从 Data Lake Store 帐户中读取数据的过程由两个步骤组成。

* 首先，针对终结点 `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`提交 GET 请求。 这会返回要将下一个 GET 请求提交到的位置。
* 然后，针对终结点 `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`提交 GET 请求。 这会显示文件的内容。

但是，由于第一和第二个步骤之间的输入参数没有任何差异，因此可以使用 `-L` 参数来提交第一个请求。 `-L` 选项本质上会将两个请求合并成一个，让 cURL 对新位置重做请求。 最后会显示所有请求调用的输出，如下所示。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

你应该会看到与下面类似的输出：

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file-in-a-data-lake-store-account"></a>重命名 Data Lake Store 帐户中的文件
此操作基于 [此处](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory)定义的 WebHDFS REST API 调用。

使用以下 cURL 命令重命名文件。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

你应该会看到与下面类似的输出：

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file-from-a-data-lake-store-account"></a>从 Data Lake Store 帐户中删除文件
此操作基于 [此处](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory)定义的 WebHDFS REST API 调用。

使用以下 cURL 命令删除文件。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

你应该看到如下输出：

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-data-lake-store-account"></a>删除 Data Lake Store 帐户
此操作基于 [此处](https://msdn.microsoft.com/library/mt694075.aspx)定义的 REST API 调用。

使用以下 cURL 命令删除 Data Lake Store 帐户。 将 **\<yourstorename>** 替换为 Data Lake Store 名称。

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

你应该看到如下输出：

    HTTP/1.1 200 OK
    ...
    ...

## <a name="see-also"></a>另请参阅
* [Open Source Big Data applications compatible with Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)




<!--HONumber=Nov16_HO4-->


