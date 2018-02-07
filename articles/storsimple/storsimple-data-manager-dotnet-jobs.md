---
title: "使用 .NET SDK 运行 Microsoft Azure StorSimple Data Manager 作业 | Microsoft 文档"
description: "了解如何使用 .NET SDK 启动 StorSimple Data Manager 作业"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: d15a5cbda2f0c2a363b40e94c38fed6631aa81b5
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>使用 .Net SDK 启动数据转换

## <a name="overview"></a>概述

本文介绍如何使用 StorSimple Data Manager 服务中的数据转换功能来转换 StorSimple 设备数据。 已转换的数据之后可供云中其他 Azure 服务使用。

可以通过两种方式启动数据转换作业：

 - 使用 .NET SDK
 - 使用 Azure 自动化 runbook
 
 本文详细说明如何创建示例 .NET 控制台应用程序以启动数据转换作业，然后跟踪它的完成情况。 若要了解有关如何通过自动化启动数据转换的详细信息，请转到[使用 Azure 自动化 runbook 触发数据转换作业](storsimple-data-manager-job-using-automation.md)。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保具备以下条件：
*   运行以下软件的计算机：

    - Visual Studio 2012、2013、2015 或 2017。

    - Azure Powershell。 [下载 Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。
*   资源组中的 StorSimple 数据管理器中存在正确配置的作业定义。
*   所有必需的 dll。 从 [GitHub 存储库](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls)下载这些 dll。
*   GitHub 存储库中的 [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) 脚本。

## <a name="step-by-step-procedure"></a>分步过程

执行以下步骤以使用 .NET 启动数据转换作业。

1. 若要检索配置参数，请执行以下步骤：
    1. 在 `C:\DataTransformation` 位置通过 GitHub 存储库脚本下载 `Get-ConfigurationParams.ps1`。
    1. 运行 GitHub 存储库中的 `Get-ConfigurationParams.ps1` 脚本。 输入以下命令：

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        可以为 ActiveDirectoryKey 和 AppName 传入任何值。

2. 此脚本输出以下值：
    * 客户端 ID
    * 租户 ID
    * Active Directory 密钥（与上面输入的密钥相同）
    * 订阅 ID

        ![配置参数脚本输出](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. 使用 Visual Studio 2012、2013 或 2015 创建 C# .NET 控制台应用程序。

    1. 启动 **Visual Studio 2012/2013/2015**。
    1. 选择“文件”>“新建”>“项目”。

        ![创建项目 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. 选择“已安装”>“模板”>“Visual C#”>“控制台应用程序”。
    3. 对于**名称**，输入“DataTransformationApp”。
    4. 对于**位置**，选择 **C:\DataTransformation**。
    6. 单击“确定”以创建该项目  。

        ![创建项目 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4.  现在，将 [dlls 文件夹](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls)中存在的所有 dll 添加为已创建的项目中的**引用**。 若要添加 dll 文件，请执行以下操作：

    1. 在 Visual Studio 中，转到“视图”>“解决方案资源管理器”。
    2. 单击“数据转换应用”项目左侧的箭头。 单击“引用”，并右键单击“添加引用”。
    
        ![添加 dll 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

    3. 浏览到包文件夹的位置，选择所有 dll，单击“添加”，然后单击“确定”。

        ![添加 dll 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. 将以下 **using** 语句添加到项目中的源文件 (Program.cs)。

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. 以下代码初始化数据转换作业实例。 在 **Main 方法**中添加此代码。 将配置参数的值替换为前面获取的值。 插入**资源组名称**和**资源名称**的值。 **资源组名称**与在其中配置作业定义的 StorSimple 数据管理器关联。 **资源名称**是 StorSimple 数据管理器服务的名称。

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. 指定需要使用其运行作业定义的参数

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    （或者）

    如果要在运行时更改作业定义参数，则添加以下代码：

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. 初始化后，添加以下代码可根据作业定义触发数据转换作业。 插入相应的**作业定义名称**。

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    粘贴了代码之后，生成解决方案。 下面是用于初始化数据转换作业实例的代码片段的屏幕截图。

   ![用于初始化数据转换作业的代码片段](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. 此作业会转换与 StorSimple 卷中的根目录和文件筛选器匹配的数据，并将其放入指定容器/文件共享中。 转换文件时，会向与作业定义同名的存储队列（与容器/文件共享在同一存储帐户中）中添加一个消息。 此消息可用作启动文件的任何进一步处理的触发器。

10. 触发作业后，可以使用以下代码可跟踪作业的完成情况。 对于作业运行，不是必须添加此代码。

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
 下面是用于触发使用 .NET 的作业的完整代码示例的屏幕截图。

 ![用于触发 .NET 作业的完整代码片段](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>后续步骤

[使用 StorSimple Data Manager UI 转换数据](storsimple-data-manager-ui.md)。
