---
title: "更改默认的 blob 路径 | Microsoft Docs"
description: "了解如何设置 Azure 函数以重命名 blob 文件路径（个人预览版）"
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
ms.openlocfilehash: 057d4d7370207859617eb63238bf425bfa6d3e16
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="change-a-blob-path-from-the-default-path-private-preview"></a>更改默认的 blob 路径（个人预览版）

本文介绍如何设置 Azure 函数以重命名默认 blob 文件路径。 

## <a name="prerequisites"></a>先决条件

确保具有已在资源组中的混合数据资源中正确配置的作业定义。

## <a name="create-an-azure-function"></a>创建 Azure 函数

若要创建 Azure 函数，请执行以下操作：

1. 转到 [Azure 门户](http://portal.azure.com/)。

2. 在左窗格的顶部，单击“新建”。 

3. 在“搜索”框中，输入“Function App”，并按 Enter。

    ![在搜索框中输入“Function App”](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

4. 在“结果”列表中，单击“Function App”。

    ![在结果列表中选择函数应用资源](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

    “Function App”窗口随即打开。

5. 单击“创建”。

    ![“Function App”窗口“创建”按钮](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

6. 在“Function App”配置边栏选项卡中，执行以下操作：

    a. 在“应用名称”框中，输入应用名称。
    
    b. 在“订阅”框中，输入订阅的名称。

    c. 在“资源组”下，单击“新建”，并输入资源组的名称。

    d. 在“宿主计划”框中，输入“消耗计划”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 在“位置”框中，输入位置。

    f. 在“存储帐户”下，选择现有存储帐户，或创建新存储帐户。 函数在内部使用一个存储帐户。

    ![输入新的 Function App 配置数据](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

7. 单击“创建”。  
    随即创建函数应用。

8. 在左窗格中，单击“更多服务”，并执行以下操作：
    
    a. 在“筛选器”框中，输入“应用服务”。
    
    b. 单击“应用服务”。 

    ![左窗格中的“更多服务”链接](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

9. 在应用服务的列表中，单击函数应用的名称。  
    函数应用页面随即打开。

10. 在左窗格中，单击“新建函数”，并执行以下操作： 

    a. 在“语言”列表中，选择“C#”。
    
    b. 在模板磁贴数组中，选择“QueueTrigger-CSharp”。

    c. 在“为函数命名”框中，为函数输入名称。

    d. 在“队列名称”框中，输入数据转换作业定义名称。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 在“存储帐户连接”下，单击“新建”，并选择与数据转换作业对应的帐户。  
        记下连接名称。 稍后在 Azure 函数中需要该名称。

       ![创建新的 C# 函数](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

    f. 单击“创建”。  
    “函数”窗口随即打开。

11. 在“函数”窗口中，运行 _.csx_ 文件中，并执行以下操作：

    a. 粘贴以下代码：

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

    b. 将第 11 行中的 **STORAGE_CONNECTIONNAME** 替换为存储帐户连接（参考点 8c）。

    c. 在左上方单击“保存”。

    ![保存函数](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. 若要完成函数，请通过执行以下操作来添加一个或多个文件：

    a. 单击“查看文件”。

       ![“查看文件”链接](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    b. 单击 **“添加”**。
    
    c. 输入“project.json”，并按 Enter。
    
    d.单击“下一步”。 在 **project.json** 文件中，粘贴以下代码：

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

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 单击“保存” 。

已创建 Azure 函数。 数据转换作业每次生成新 blob 时，都会触发此函数。

## <a name="next-steps"></a>后续步骤

[使用 StorSimple Data Manager UI 转换数据](storsimple-data-manager-ui.md)
