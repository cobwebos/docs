---
title: 使用 Windows VM 系统分配的托管标识访问 Azure 存储
description: 本教程将指导你完成使用 Windows VM 系统分配的托管标识访问 Azure 存储的过程。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2018
ms.author: daveba
ms.openlocfilehash: 3a0215f5905f152d568522b95e41492bc7658a52
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338356"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage"></a>教程：使用 Windows VM 系统分配的托管标识访问 Azure 存储

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教程介绍了如何使用 Windows 虚拟机 (VM) 的系统分配的托管标识来访问 Azure 存储。 学习如何：

> [!div class="checklist"]
> * 在存储帐户中创建 Blob 容器
> * 向 Windows VM 的系统分配的托管标识授予对存储帐户的访问权限 
> * 获取访问令牌并使用它来调用 Azure 存储 

> [!NOTE]
> 适用于 Azure 存储的 Azure Active Directory 身份验证当前处于公共预览版。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [登录到 Azure 门户](https://portal.azure.com)

- [创建 Windows 虚拟机](/azure/virtual-machines/windows/quick-create-portal)

- [在虚拟机上启用系统分配的托管标识](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

## <a name="create-a-storage-account"></a>创建存储帐户 

在本部分中，创建一个存储帐户。 

1. 单击 Azure 门户左上角的“+ 创建资源”按钮。
2. 单击“存储”，然后单击“存储帐户 - Blob、文件、表、队列”。
3. 在“名称”下，输入存储帐户的名称。  
4. “部署模型”和“帐户类型”应分别设置为“资源管理器”和“存储(常规用途 v1)”。 
5. 确保“订阅”和“资源组”与上一步中创建 VM 时指定的名称匹配。
6. 单击“创建”。

    ![新建存储帐户](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>创建 blob 容器，并将文件上传到存储帐户

文件需要 blob 存储，因此你需要创建用于存储文件的 blob 容器。 然后将文件上传到新存储帐户中的 blob 容器。

1. 导航回新创建的存储帐户。
2. 单击“Blob 服务”下的“容器”。
3. 单击页面顶部的“+ 容器”。
4. 在“新建容器”下，为容器输入一个名称，在“公共访问级别”下，保留默认值。

    ![创建存储容器](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. 使用你选择的编辑器，在本地计算机上创建一个标题为 *hello world.txt* 的文件。  打开该文件并添加文本“Hello world! :）”（不包括引号），然后保存该文件。 
6. 通过单击容器名称并单击“上传”，将该文件上传到新创建的容器。
7. 在“上传 blob”窗格中，在“文件”下，单击文件夹图标并浏览到本地计算机上的文件 **hello_world.txt**，选择该文件，然后单击“上传”。
    ![上传文本文件](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>向 VM 授予对 Azure 存储容器的访问权限 

可以使用 VM 的系统分配的托管标识检索 Azure 存储 blob 中的数据。   

1. 导航回新创建的存储帐户。  
2. 单击左侧面板中的“访问控制(IAM)”链接。  
3. 单击页面顶部的“+ 添加”，为 VM 添加新的角色分配。
4. 在“角色”下，从下拉列表中，选择“存储 Blob 数据读取器(预览)”。 
5. 在下一个下拉列表中，在“将访问权限分配到”下，选择“虚拟机”。  
6. 接下来，确保“订阅”下拉列表中列出了正确的订阅，然后将“资源组”设置为“所有资源组”。  
7. 在“选择”下，选择你的 VM 并单击“保存”。 

    ![分配权限](~/articles/active-directory/managed-service-identity/media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>获取访问令牌并使用它来调用 Azure 存储 

Azure 存储原本就支持 Azure AD 身份验证，因此可以直接接受使用托管标识获取的访问令牌。 在某种程度上，这是将 Azure 存储与 Azure AD 集成，不同于在连接字符串中提供凭据。

下面是一个 .Net 代码示例，它使用访问令牌打开到 Azure 存储的连接，然后读取之前创建的文件的内容。 此代码必须在 VM 上运行才能访问 VM 的托管标识终结点。 使用访问令牌方法需要 .Net Framework 4.6 或更高版本。 相应地替换 `<URI to blob file>` 的值。 可以通过以下方式获取此值：在“概述”页上的“属性”下，导航到你创建并上传到 blob 存储的文件，然后复制 **URL**。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");
           
            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);
        
            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }            
    }
}
```

响应包含文件内容：

`Hello world! :)`

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何启用 Windows VM 的系统分配的标识以访问 Azure 存储。  若要详细了解 Azure 存储，请参阅：

> [!div class="nextstepaction"]
> [Azure 存储](/azure/storage/common/storage-introduction)



