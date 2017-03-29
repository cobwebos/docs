---
title: "更改默认的 blob 路径 | Microsoft Docs"
description: "了解如何设置 Azure 函数以重命名 Blob 文件路径（个人预览版）"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8a06e4b3f482943f55e998c3c68857d3530ff98a
ms.lasthandoff: 03/21/2017

---

# <a name="change-blob-path-from-the-default-private-preview"></a>更改默认的 blob 路径（个人预览版）

本文介绍如何设置 Azure 函数以重命名默认 Blob 文件路径。 

## <a name="prerequisites"></a>先决条件

在开始之前，请确保具备以下条件：
* 已在资源组中的混合数据资源中正确配置的作业定义。

## <a name="create-an-azure-function"></a>创建 Azure 函数

执行下列步骤创建 Azure 函数。

#### <a name="to-create-an-azure-function"></a>创建 Azure 函数

1. 转到 [Azure 门户](http://portal.azure.com/)。

2. 单击左上角的“+ 新建”。 在“搜索”文本框中键入“Function App”，然后按“Enter”。

    ![转到 Function App 资源](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

3. 单击结果中的“Function App”。

    ![选择 Function App 资源](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

4. 打开“Function App”窗口并单击“创建”。

    ![创建新的 Function App](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

5. 在“配置”边栏选项卡中，输入所有输入信息，然后单击“创建”。

    1. 应用程序名称
    2. 订阅
    3. 资源组
    4. 托管计划 - **消耗计划**
    5. 位置
    6. 存储帐户 - 使用现有存储帐户，或创建一个新的存储帐户。 函数在内部使用一个存储帐户。

        ![输入新的 Function App 配置数据](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

6. 创建 Function App 后，在左下角导航到“更多服务 >”。 在“筛选器”文本框中键入“应用程序服务”，然后单击“应用程序服务”。

    ![更多服务 >](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

7. 单击应用程序服务列表中的“Function App 名称”。

8. 单击“+ 新建函数”。 在“语言”下拉列表中选择“C#”。 在模板列表中选择“QueueTrigger-CSharp”选项。 输入所有的输入信息。

   1. 名称 - 提供函数的名称。
   2. 队列名称 - 输入**数据转换作业定义名称**。
   3. 存储帐户连接 - 单击“新建”选项。 选择对应于数据转换作业的帐户。
      
      记下 `Connection name`。 稍后在 Azure 函数中需要此名称。

   4. 单击“创建”按钮。

       ![新建 C Sharp 函数 >](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

9. 在“函数”窗口中，运行 _.csx_ 文件。 复制并粘贴以下代码：

    ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            log.Info($"Blob Uri: {myQueueItem.TargetLocation}");
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            string uriString = "windows.net/";
            string containerName = myQueueItem.TargetLocation;
            containerName = containerName.Substring(containerName.IndexOf(uriString) + uriString.Length);
            containerName = containerName.Substring(0, containerName.IndexOf("/"));
            //log.Info($"Container name: {containerName}");

            CloudBlobContainer container = blobClient.GetContainerReference(containerName);

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            string containerUri = string.Format("{0}/", container.Uri.ToString());  // Reading container Uri
            string blobUri =  myQueueItem.TargetLocation;
            string blobName = blobUri.Replace(containerUri, string.Empty).Replace("%20", " "); // Reading existing file path after container name
            string newBlobName = blobName.Substring(blobName.IndexOf(string.Format("{0}/", container.Name)) + container.Name.Length + 1); // Reading actual File path after container name

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            CloudBlockBlob blobCopy = container.GetBlockBlobReference(newBlobName);
            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy the blob
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }
    
    ```

   1. 将第 12 行中的 **STORAGE_CONNECTIONNAME** 替换为存储帐户连接（参考点 8c）。
   2. 单击左上角的“保存”按钮。

       ![保存函数 >](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

10.  单击右上角的“查看文件”。

    ![查看文件](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

   1. 单击“+ 添加”。 键入“project.json”，然后按“Enter”。
   2. 复制以下代码并将其粘贴到 **project.json** 文件中。

        ```
            {
            "frameworks": {
                "net46":{
                "dependencies": {
                    "windowsazure.storage": "8.1.1"
                }
                }
            }
            }
        
        ```

   2. 单击“保存” 。

已创建 Azure 函数。 数据转换作业每次生成新的 blob 时，都会触发此函数。

## <a name="next-steps"></a>后续步骤

[使用 StorSimple Data Manager UI 转换数据](storsimple-data-manager-ui.md)。

