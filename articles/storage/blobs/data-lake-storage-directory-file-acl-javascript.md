---
title: 将 JavaScript 用于文件 & Azure Data Lake Storage Gen2 中的 Acl
description: 使用适用于 JavaScript 的 Azure Storage Data Lake 客户端库在启用了分层命名空间 (HNS) 的存储帐户中管理目录和文件以及目录访问控制列表 (ACL)。
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 04d0d23bdbdaeda6a4823c900badb3133ba9eeae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061542"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>使用 JavaScript 管理 Azure Data Lake Storage Gen2 中的目录、文件和 Acl

本文介绍了如何使用 JavaScript 在启用了分层命名空间 (HNS) 的存储帐户中创建和管理目录、文件与权限。 

[包（节点包管理器）](https://www.npmjs.com/package/@azure/storage-file-datalake) | [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
> * 一个已启用分层命名空间 (HNS) 的存储帐户。 按[这些](data-lake-storage-quickstart-create-account.md)说明创建一个。
> * 如果在 node.js 应用程序中使用此包，则需要 node.js 8.0.0 或更高版本。

## <a name="set-up-your-project"></a>设置项目

通过打开终端窗口，然后键入以下命令，安装适用于 JavaScript 的 Data Lake 客户端库。

```javascript
npm install @azure/storage-file-datalake
```

将此`storage-file-datalake`语句放置在代码文件的顶部，以导入包。 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>连接到帐户 

若要使用本文中的代码片段，需创建一个表示存储帐户的 **DataLakeServiceClient** 实例。 

### <a name="connect-by-using-an-account-key"></a>使用帐户密钥进行连接

这是连接到帐户的最简单方法。 

此示例使用帐户密钥创建一个**DataLakeServiceClient**实例。

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> 此授权方法仅适用于 Node.js 应用程序。 如果打算在浏览器中运行代码，则可以使用 Azure Active Directory (AD) 进行授权。 

### <a name="connect-by-using-azure-active-directory-ad"></a>使用 Azure Active Directory （AD）进行连接

可以使用适用于[JS 的 Azure 标识客户端库](https://www.npmjs.com/package/@azure/identity)通过 Azure AD 对应用程序进行身份验证。

此示例使用客户端 ID、客户端机密和租户 ID 创建一个**DataLakeServiceClient**实例。  若要获取这些值，请参阅[从 Azure AD 获取用于从客户端应用程序授权请求的令牌](../common/storage-auth-aad-app.md)。

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> 有关更多示例，请参阅[适用于 JS 的 Azure 标识客户端库](https://www.npmjs.com/package/@azure/identity)文档。

## <a name="create-a-file-system"></a>创建文件系统

文件系统充当文件的容器。 可以通过获取**FileSystemClient**实例，然后调用**FileSystemClient**方法来创建一个。

此示例创建名为 `my-file-system` 的文件系统。 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>创建目录

通过获取**DirectoryClient**实例，然后调用**DirectoryClient**方法来创建目录引用。

此示例将名为 `my-directory` 的目录添加到文件系统。 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>重命名或移动目录

通过调用**DirectoryClient**方法重命名或移动目录。 以参数形式传递所需目录的路径。 

此示例将某个子目录重命名为名称 `my-directory-renamed`。

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

此示例将名为 `my-directory-renamed` 的目录移到名为 `my-directory-2` 的目录的子目录中。 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>删除目录

通过调用**DirectoryClient**方法删除目录。

此示例删除名为 `my-directory` 的目录。   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>管理目录 ACL

此示例获取并设置名为 `my-directory` 的目录的 ACL。 此示例为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取访问权限。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已向应用程序用来授权访问的安全主体分配了[存储 Blob 数据所有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

## <a name="upload-a-file-to-a-directory"></a>将文件上传到目录

首先，请阅读文件。 此示例使用 node.js `fs`模块。 然后，通过创建**FileClient**实例，然后调用**FileClient**方法，在目标目录中创建文件引用。 通过调用**FileClient**方法上传文件。 请确保通过调用**FileClient**方法完成上传。

此示例将文本文件上传到名为 `my-directory` 的目录。

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>管理文件 ACL

此示例获取并设置名为 `upload-file.txt` 的文件的 ACL。 此示例为拥有用户提供读取、写入和执行权限，为拥有组授予读取和执行权限，并为所有其他用户提供读取访问权限。

> [!NOTE]
> 如果你的应用程序通过使用 Azure Active Directory (Azure AD) 来授予访问权限，请确保已向应用程序用来授权访问的安全主体分配了[存储 Blob 数据所有者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)。 若要详细了解如何应用 ACL 权限以及更改它们所带来的影响，请参阅 [Azure Data Lake Storage Gen2 中的访问控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>从目录下载

首先，创建表示要下载的文件的**FileSystemClient**实例。 使用**FileSystemClient**方法读取该文件。 然后写入文件。 此示例使用 node.js `fs`模块来执行此操作。 

> [!NOTE]
> 此下载文件的方法仅适用于 node.js 应用程序。 如果计划在浏览器中运行代码，请参阅[Azure 存储文件 Data Lake 适用于 JavaScript 的客户端库](https://www.npmjs.com/package/@azure/storage-file-datalake)自述文件，了解如何在浏览器中执行此操作的示例。 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>列出目录内容

此示例将打印位于名为`my-directory`的目录中的每个目录和文件的名称。

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>另请参阅

* [包（节点包管理器）](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [提供反馈](https://github.com/Azure/azure-sdk-for-java/issues)