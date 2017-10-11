---
title: "使用 .NET SDK 运行 Microsoft Azure StorSimple Data Manager 作业 | Microsoft 文档"
description: "了解如何使用 .NET SDK 启动 StorSimple Data Manager 作业（个人预览版）"
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
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 44d243a034b20b99faf284c8615e470bc6f9d020
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="use-the-net-sdk-to-initiate-data-transformation-private-preview"></a>使用 .Net SDK 启动数据转换（个人预览版）

## <a name="overview"></a>概述

本文介绍如何使用 StorSimple Data Manager 服务中的数据转换功能来转换 StorSimple 设备数据。 已转换的数据之后可供云中其他 Azure 服务使用。 本文还包含一个演练，帮助创建示例 .NET 控制台应用程序以启动数据转换作业，并跟踪它的完成情况。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保具备以下条件：
*   系统已安装 Visual Studio 2012、2013、2015 或 2017。
*   已安装 Azure PowerShell。 [下载 Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。
*   用于初始化数据转换作业的配置设置（用于获取这些设置的说明在此处提供）。
*   已在资源组中的混合数据资源中正确配置的作业定义。
*   所有必需的 dll。 从 [GitHub 存储库](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls)下载这些 dll。
*   github 存储库中的 `Get-ConfigurationParams.ps1` [脚本](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)。

## <a name="step-by-step"></a>分步指南

执行以下步骤以使用 .NET 启动数据转换作业。

1. 若要检索配置参数，请执行以下步骤：
    1. 在 `C:\DataTransformation` 位置通过 github 存储库脚本下载 `Get-ConfigurationParams.ps1`。
    1. 运行 github 存储库中的 `Get-ConfigurationParams.ps1` 脚本。 输入以下命令：

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        可以为 ActiveDirectoryKey 和 AppName 传入任何值。


2. 此脚本输出以下值：
    * 客户端 ID
    * 租户 ID
    * Active Directory 密钥（与上面输入的密钥相同）
    * 订阅 ID

3. 使用 Visual Studio 2012、2013 或 2015 创建 C# .NET 控制台应用程序。

    1. 启动 **Visual Studio 2012/2013/2015**。
    1. 单击“文件”，指向“新建”并单击“项目”。
    2. 展开“模板”，并选择“Visual C#”。
    3. 从右侧项目类型列表中选择“控制台应用程序”。
    4. 对于**名称**，输入“DataTransformationApp”。
    5. 对于**位置**，选择 **C:\DataTransformation**。
    6. 单击“确定”以创建该项目  。

4.  现在，将 [dll](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) 文件夹中存在的所有 DLL 添加为已创建的项目中的**引用**。 若要下载 dll 文件，请执行以下操作：

    1. 在 Visual Studio 中，转到“视图”>“解决方案资源管理器”。
    1. 单击“数据转换应用”项目左侧的箭头。 单击“引用”，并右键单击“添加引用”。
    2. 浏览到程序包文件夹的位置，选择所有 DLL，单击“添加”，并单击“确定”。

5. 将以下 **using** 语句添加到项目中的源文件 (Program.cs)。

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```


6. 以下代码初始化数据转换作业实例。 在 **Main 方法**中添加此代码。 将配置参数的值替换为前面获取的值。 插入**资源组名称**和**混合数据资源名称**的值。 **资源组名称**是托管已配置作业定义的混合数据资源的资源组名称。

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

    ```

9. 此作业会将 StorSimple 卷上的根目录下存在的匹配文件上传到指定的容器。 上传文件时，会丢弃与作业定义同名的队列（与容器在同一存储帐户中）中的消息。 此消息可用作启动文件的任何进一步处理的触发器。

10. 触发作业后，添加以下代码可跟踪作业的完成情况。

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


## <a name="next-steps"></a>后续步骤

[使用 StorSimple Data Manager UI 转换数据](storsimple-data-manager-ui.md)。
