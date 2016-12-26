---
title: "通过用于 Node.js 的 Azure SDK 实现 Azure Data Lake Store 入门 | Microsoft Docs"
description: "了解如何使用 Node.js 运行 Data Lake Store 帐户和文件系统。"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 2fee173c-69ae-4e1d-8773-48618cda9e16
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/21/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: c157da7bf53e2d0762624e8e71e56e956db04a24
ms.openlocfilehash: 968e4039b2c94d67560fafb245b6558c70b6d2e3


---
# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>通过 Azure SDK for Node.js 实现 Azure Data Lake Store 入门
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

了解如何使用 Node.js 的 Azure SDK 来创建 Azure Data Lake Store 帐户以及执行基本操作，如创建文件夹、上传和下载数据文件、删除帐户等。有关 Data Lake Store 的详细信息，请参阅 [Data Lake Store 概述](data-lake-store-overview.md)。 目前，此 SDK 支持

* **Node.js 版本：0.10.0 或更高版本**
* **帐户的 REST API 版本：2015 年 10 月 1 日预览版**
* **文件系统：2015-10-01-preview 的 REST API 版本**

## <a name="prerequisites"></a>先决条件
在开始阅读本文前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **创建 Azure Active Directory 应用程序**。 使用 Azure AD 应用程序对 Data Lake Store 应用程序进行 Azure AD 身份验证。 有不同的方式可进行 Azure AD 身份验证，即“最终用户身份验证”或“服务到服务身份验证”。 有关如何进行身份验证的说明和详细信息，请参阅[使用 Azure Active Directory 进行 Data Lake Store 身份验证](data-lake-store-authenticate-using-active-directory.md)。

## <a name="how-to-install"></a>安装方法
```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>使用 Azure Active Directory 进行身份验证
下面的代码段显示了使用 Azure AD 进行 Data Lake Store 身份验证的两个不同的方式。 有关多种用于进行 Data Lake Store 身份验证方式的详细讨论，请参阅[使用 Azure Active Directory 进行 Data Lake Store 身份验证](data-lake-store-authenticate-using-active-directory.md)。

下面的代码段也需要 Azure AD 域名称、Azure AD 应用客户端 ID 等输入。所有这些详细信息可从必须创建的 Azure AD 应用程序（其详细信息也包含在上面的链接中）中检索。

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>创建 Data Lake Store 客户端
```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>创建 Data Lake Store 帐户
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="create-a-file-with-content"></a>创建具有内容的文件
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>获取文件和文件夹列表
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>另请参阅
* [Microsoft Azure SDK for Node.js](https://github.com/azure/azure-sdk-for-node)
* [Microsoft Azure SDK for Node.js - Data Lake Analytics Management](https://www.npmjs.com/package/azure-arm-datalake-analytics)（Microsoft Azure SDK for Node.js - Data Lake Analytics 管理）




<!--HONumber=Nov16_HO4-->


