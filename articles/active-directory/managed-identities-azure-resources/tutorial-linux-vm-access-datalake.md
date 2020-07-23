---
title: 教程`:`使用托管标识访问 Azure Data Lake Store - Linux - Azure AD
description: 本教程介绍了如何使用 Linux VM 系统分配的托管标识访问 Azure Data Lake Store。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0fe442741ae0b8fa817c9ea177ff244a413720e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "75888509"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>教程：使用 Linux VM 系统分配的托管标识访问 Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教程介绍如何使用 Linux 虚拟机 (VM) 的系统分配托管标识访问 Azure Data Lake Store。 学习如何： 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 授予 VM 对 Azure Data Lake Store 的访问权限。
> * 使用 VM 的系统分配的托管标识获取访问令牌，用以访问 Azure Data Lake Store。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-access"></a>授予访问权限

此部分介绍如何授予 VM 对 Azure Data Lake Store 中的文件和文件夹的访问权限。 对于此步骤，可以使用现有的 Data Lake Store 实例，或创建新的实例。 若要使用 Azure 门户创建 Data Lake Store 实例，请遵循 [Azure Data Lake Store 快速入门](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)。 [Azure Data Lake Store 文档](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)中还提供了有关使用 Azure CLI 和 Azure PowerShell 执行这些操作的快速入门。

在 Data Lake Store 中，新建一个文件夹，并向 Linux VM 系统分配的托管标识授予在该文件夹中读取、写入和执行文件的权限：

1. 在 Azure 门户的左窗格中，选择“Data Lake Store”  。
2. 选择要使用的 Data Lake Store 实例。
3. 在命令栏上选择“数据资源管理器”  。
4. 将选择 Data Lake Store 实例的根文件夹。 在命令栏上选择“访问”。 
5. 选择 **添加** 。  在“选择”框中，输入 VM 的名称 -- 例如 **DevTestVM** 。 从搜索结果中选择自己的 VM，然后单击“选择”  。
6. 单击“选择权限”  。  选择“读取”和“执行”，并以“仅为访问权限”的形式添加到“此文件夹”     。 选择“确定”  。  权限应已成功添加。
7. 关闭“访问”窗格  。
8. 本教程将新建一个文件夹。 在命令栏上选择“新建文件夹”并为新文件夹命名，例如 **TestFolder** 。  选择“确定”  。
9. 选择创建的文件夹，然后在命令栏上选择“访问”。 
10. 与执行步骤 5 时一样，选择“添加”。  在“选择”框中，输入 VM 的名称。  从搜索结果中选择自己的 VM，然后单击“选择”  。
11. 与执行步骤 6 时一样，选择“选择权限”。  选择“读取”、“写入”和“执行”，并以“访问权限条目和默认访问权限条目”的形式添加到“此文件夹”      。 选择“确定”  。  权限应已成功添加。

Azure 资源的托管标识现在可以对你创建的文件夹中的文件执行所有操作。 若要详细了解管理 Data Lake Store 的访问权限，请参阅 [Data Lake Store 中的访问控制](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control)。

## <a name="get-an-access-token"></a>获取访问令牌 

此部分介绍如何获取访问令牌并调用 Data Lake Store 文件系统。 Azure Data Lake Store 原本就支持 Azure AD 身份验证，因此可以直接接受使用 Azure 资源的托管标识获取的访问令牌。 若要在 Data Lake Store 文件系统中进行身份验证，请将 Azure AD 颁发的访问令牌发送到 Data Lake Store 文件系统终结点。 授权标头的访问令牌采用“Bearer \<ACCESS_TOKEN_VALUE\>”格式。  若要详细了解 Data Lake Store 对 Azure AD 身份验证的支持情况，请参阅[使用 Azure Active Directory 在 Data Lake Store 中进行身份验证](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)。

本教程通过使用 cURL 发出 REST 请求，对 Data Lake Store 文件系统的 REST API 进行身份验证。

> [!NOTE]
> Data Lake Store 文件系统的客户端 SDK 目前不支持 Azure 资源的托管标识。

若要完成这些步骤，需要使用 SSH 客户端。 如果使用的是 Windows，可以在[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 客户端。 如需有关配置 SSH 客户端密钥的帮助，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](../../virtual-machines/linux/ssh-from-windows.md)或[如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对](../../virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在门户中，浏览到自己的 Linux VM。 在“概述”中，选择“连接”。    
2. 使用所选的 SSH 客户端连接到该 VM。 
3. 在终端窗口中，使用 cURL 向 Azure 资源终结点的本地托管标识发出请求，获取访问 Data Lake Store 系统所需的访问令牌。 Data Lake Store 的资源标识符是 `https://datalake.azure.net/`。  请务必在资源标识符中包含尾部反斜杠。
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   成功响应将返回对 Data Lake Store 进行身份验证需使用的访问令牌：

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. 使用 cURL 向 Data Lake Store 文件系统 REST 终结点发出请求，在根文件夹中列出文件夹。 使用此方法可轻松检查是否所有内容都已正确配置。 复制上一步中的访问令牌值。 身份验证标头中“Bearer”字符串的首字母“B”必须大写。 在 Azure 门户的“Data Lake Store”窗格中的“概述”部分，可以找到 Data Lake Store 实例的名称   。

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   成功响应如下所示：

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. 现在可以尝试将文件上传到 Data Lake Store 实例。 首先，创建要上传的文件。

   ```bash
   echo "Test file." > Test1.txt
   ```

6. 使用 cURL 向 Data Lake Store 文件系统的 REST 终结点发出请求，将文件上传到前面创建的文件夹。 上传涉及到重定向，cURL 会自动遵循此重定向。 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    成功响应如下所示：

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

使用 Data Lake Store 文件系统的其他 API 可以在文件中追加内容、下载文件以及执行其他操作。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用 Linux VM 系统分配的托管标识来访问 Azure Data Lake Store。 若要了解有关 Azure Data Lake Store 的详细信息，请参阅：

> [!div class="nextstepaction"]
>[Azure Data Lake Store](/azure/data-lake-store/data-lake-store-overview)
