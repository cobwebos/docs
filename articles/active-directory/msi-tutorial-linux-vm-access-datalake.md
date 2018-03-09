---
title: "使用 Linux VM 的托管服务标识访问 Azure Data Lake Store"
description: "本教程介绍如何使用 Linux VM 的托管服务标识 (MSI) 访问 Azure Data Lake Store。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: fdf1c49c644a97ff2f66a8b217ee54896ed54131
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2018
---
# <a name="use-managed-service-identity-for-a-linux-vm-to-access-azure-data-lake-store"></a>使用 Linux VM 的托管服务标识访问 Azure Data Lake Store

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教程介绍如何使用 Linux 虚拟机 (VM) 的托管服务标识访问 Azure Data Lake Store。 Azure 通过 MSI 自动管理创建的标识。 可以使用 MSI 在支持 Azure Active Directory (Azure AD) 身份验证的服务中进行身份验证，而无需在代码中插入凭据。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Linux VM 上启用 MSI。 
> * 授予 VM 对 Azure Data Lake Store 的访问权限。
> * 使用 VM 标识获取访问令牌，并使用它访问 Azure Data Lake Store。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>在新的资源组中创建 Linux 虚拟机

本教程将新建一个 Linux VM。 另外，还可以在现有 VM 上启用 MSI。

1. 选择 Azure 门户左上角的“新建”按钮。
2. 选择“计算”，然后选择“Ubuntu Server 16.04 LTS”。
3. 输入虚拟机信息。 对于“身份验证类型”，选择“SSH 公钥”或“密码”。 使用创建的凭据可以登录 VM。

   ![用于创建虚拟机的“基本信息”窗格](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 在“订阅”列表中，选择虚拟机的订阅。
5. 若要选择要在其中创建虚拟机的新资源组，请选择“资源组” > “新建”。 完成后，选择“确定”。
6. 选择 VM 大小。 若要查看更多的大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 在设置窗格中保留默认值，然后选择“确定”。

## <a name="enable-msi-on-your-vm"></a>在 VM 上启用 MSI

可以使用虚拟机的 MSI 从 Azure AD 获取访问令牌，而无需在代码中插入凭据。 启用 MSI 会在 VM 上安装 MSI VM 扩展，并在 Azure 资源管理器中启用 MSI。  

1. 对于“虚拟机”，请选择要在其上启用 MSI 的虚拟机。
2. 在左窗格中，选择“配置”。
3. 此时会显示“托管服务标识”。 若要注册并启用 MSI，请选择“是”。 若要禁用 MSI，请选择“否”。
   ![“注册到 Azure Active Directory”选项](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)
4. 选择“保存”。
5. 若要检查此 Linux VM 上安装了哪些扩展，请选择“扩展”。 如果 MSI 已启用，列表中会显示“ManagedIdentityExtensionforLinux”。

   ![扩展列表](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>授予 VM 对 Azure Data Lake Store 的访问权限

现在可以授予 VM 对 Azure Data Lake Store 中的文件和文件夹的访问权限。 对于此步骤，可以使用现有的 Data Lake Store 实例，或创建新的实例。 若要使用 Azure 门户创建 Data Lake Store 实例，请遵循 [Azure Data Lake Store 快速入门](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)。 [Azure Data Lake Store 文档](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)中还提供了有关使用 Azure CLI 和 Azure PowerShell 执行这些操作的快速入门。

在 Data Lake Store 中新建文件夹，并为 MSI 授予读取、写入和执行该文件夹中的文件的权限：

1. 在 Azure 门户的左窗格中，选择“Data Lake Store”。
2. 选择要使用的 Data Lake Store 实例。
3. 在命令栏上选择“数据资源管理器”。
4. 将选择 Data Lake Store 实例的根文件夹。 在命令栏上选择“访问”。
5. 选择“添加”。  在“选择”框中，输入 VM 的名称 -- 例如 **DevTestVM**。 从搜索结果中选择自己的 VM，然后单击“选择”。
6. 单击“选择权限”。  选择“读取”和“执行”，并以“仅为访问权限”的形式添加到“此文件夹”。 选择“确定”。  权限应已成功添加。
7. 关闭“访问”窗格。
8. 本教程将新建一个文件夹。 在命令栏上选择“新建文件夹”并为新文件夹命名，例如 **TestFolder**。  选择“确定”。
9. 选择创建的文件夹，然后在命令栏上选择“访问”。
10. 与执行步骤 5 时一样，选择“添加”。 在“选择”框中，输入 VM 的名称。 从搜索结果中选择自己的 VM，然后单击“选择”。
11. 与执行步骤 6 时一样，选择“选择权限”。 选择“读取”、“写入”和“执行”，并以“访问权限条目和默认访问权限条目”的形式添加到“此文件夹”。 选择“确定”。  权限应已成功添加。

MSI 现在可以对所创建文件夹中的文件执行所有操作。 若要详细了解管理 Data Lake Store 的访问权限，请参阅 [Data Lake Store 中的访问控制](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control)。

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>获取访问令牌并调用 Data Lake Store 文件系统

Azure Data Lake Store 原生支持 Azure AD 身份验证，因此可以直接接受通过 MSI 获取的访问令牌。 若要在 Data Lake Store 文件系统中进行身份验证，请将 Azure AD 颁发的访问令牌发送到 Data Lake Store 文件系统终结点。 授权标头的访问令牌采用“Bearer \<ACCESS_TOKEN_VALUE\>”格式。  若要详细了解 Data Lake Store 对 Azure AD 身份验证的支持情况，请参阅[使用 Azure Active Directory 在 Data Lake Store 中进行身份验证](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)。

本教程通过使用 cURL 发出 REST 请求，对 Data Lake Store 文件系统的 REST API 进行身份验证。

> [!NOTE]
> Data Lake Store 文件系统的客户端 SDK 目前不支持托管服务标识。

若要完成这些步骤，需要使用 SSH 客户端。 如果使用的是 Windows，可以在[适用于 Linux 的 Windows 子系统](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 客户端。 如需有关配置 SSH 客户端密钥的帮助，请参阅[如何在 Azure 上将 SSH 密钥与 Windows 配合使用](../virtual-machines/linux/ssh-from-windows.md)或[如何创建和使用适用于 Azure 中 Linux VM 的 SSH 公钥和私钥对](../virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在门户中，浏览到自己的 Linux VM。 在“概述”中，选择“连接”。  
2. 使用所选的 SSH 客户端连接到该 VM。 
3. 在终端窗口中，使用 cURL 向本地 MSI 终结点发出请求，获取访问 Data Lake Store 系统所需的访问令牌。 Data Lake Store 资源标识符是“https://datalake.azure.net/”。  请务必在资源标识符中包含尾部反斜杠。
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
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

4. 使用 cURL 向 Data Lake Store 文件系统 REST 终结点发出请求，在根文件夹中列出文件夹。 使用此方法可轻松检查是否所有内容都已正确配置。 复制上一步中的访问令牌值。 身份验证标头中“Bearer”字符串的首字母“B”必须大写。 在 Azure 门户的“Data Lake Store”窗格中的“概述”部分，可以找到 Data Lake Store 实例的名称。

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

祝贺你！ 现已成功使用 Linux VM 的 MSI 在 Data Lake Store 文件系统中完成身份验证。

## <a name="related-content"></a>相关内容

- 有关 MSI 的概述，请参阅[托管服务标识概述](../active-directory/msi-overview.md)。
- 对于管理操作，Data Lake Store 使用 Azure 资源管理器。  有关使用 MSI 在资源管理器中进行身份验证的详细信息，请参阅[使用 Linux VM 托管服务标识 (MSI) 访问资源管理器](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm)。
- 详细了解如何[使用 Azure Active Directory 在 Data Lake Store 中进行身份验证](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)。
- 详细了解如何[使用 REST API](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api) 或 [WebHDFS 文件系统 API](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis) 在 Azure Data Lake Store 中执行文件系统操作。
- 详细了解 [Data Lake Store 中的访问控制](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control)。

使用以下评论部分提供反馈，帮助我们改进内容。