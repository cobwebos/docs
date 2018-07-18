---
title: 更改默认的 blob 路径 | Microsoft Docs
description: 了解如何设置 Azure 函数以重命名 blob 文件路径
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: f73d9dcedee5165af752b9e10fb70de860e8e98b
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862394"
---
# <a name="change-a-blob-path-from-the-default-path"></a>更改默认的 blob 路径

当 StorSimple 数据管理器服务转换数据时，默认情况下，它会将转换后的 blob 放置在创建目标存储库期间指定的存储容器中。 blob 到达此位置时，可能需要将这些 blob 移动到备用位置。 本文介绍如何设置 Azure 函数以重命名默认 blob 文件路径，从而将 blob 移动到不同的位置。

## <a name="prerequisites"></a>先决条件

确保 StorSimple 数据管理器服务中具有正确配置的作业定义。

## <a name="create-an-azure-function"></a>创建 Azure 函数

要创建 Azure 函数，请执行下列步骤：

1. 转到 [Azure 门户](http://portal.azure.com/)。

2. 单击“+ 创建资源”。 在“搜索”框中，输入“Function App”，并按 Enter。 在显示的应用列表中，选中并单击“Function App”。

    ![在搜索框中输入“Function App”](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. 单击“创建”。

    ![“Function App”窗口“创建”按钮](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. 在“Function App”配置边栏选项卡中，执行以下步骤：

    1. 提供一个唯一的应用名称。
    2. 从下拉列表中，选择“订阅”。 此订阅应与和 StorSimple 数据管理器服务关联的订阅相同。
    3. 选择“新建”资源组。
    4. 在“托管计划”下拉列表中，选择“使用计划”。
    5. 指定函数运行的位置。 需要选择 StorSimple 数据管理器服务以及与作业定义相关联的存储帐户所在区域。
    6. 选择一个现有存储帐户，或创建一个新的存储帐户。 函数在内部使用一个存储帐户。

        ![输入新的 Function App 配置数据](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. 单击“创建”。 随即创建函数应用。
     
        ![创建的 Function App](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. 选择**函数**，然后单击 **+ 新建函数**。

    ![单击“+ 新建函数”](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. 对于语言，选择“C#”。 在模板磁贴数组的“QueueTrigger-CSharp”磁贴中，选择“C#”。

7. 在“队列触发器”中：

    1. 输入函数的名称。
    2. 在“队列名称”框中，输入数据转换作业定义名称。
    3. 在“存储帐户连接”下，单击“新建”。 从存储帐户列表中，选择与你的作业定义关联的帐户。 记下连接名称（已突出显示）。 稍后在 Azure 函数中需要该名称。

        ![创建新的 C# 函数](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. 单击“创建”。 随即创建函数。

     
10. 在“函数”窗口中，运行 _.csx_ 文件。

    ![创建新的 C# 函数](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    执行以下步骤。

    1. 粘贴以下代码：

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
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
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

    2. 将第 11 行中的 STORAGE_CONNECTIONNAME 替换为存储帐户连接（参考步骤 7c）。

        ![复制存储连接名称](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. “保存”函数。

        ![保存函数](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. 若要完成函数，请通过执行以下步骤来添加一个或多个文件：

    1. 单击“查看文件”。

       ![“查看文件”链接](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. 单击“+ 添加”。
        
        ![“查看文件”链接](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. 输入“project.json”，并按 Enter。 在 **project.json** 文件中，粘贴以下代码：

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

    
    4. 单击“ **保存**”。

        ![“查看文件”链接](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

已创建 Azure 函数。 数据转换作业每次生成新 blob 时，都会触发此函数。

## <a name="next-steps"></a>后续步骤

[使用 StorSimple Data Manager UI 转换数据](storsimple-data-manager-ui.md)
