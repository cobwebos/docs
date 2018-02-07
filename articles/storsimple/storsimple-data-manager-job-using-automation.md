---
title: "在 StorSimple 数据管理器中使用 Azure 自动化来启动作业 | Microsoft Docs"
description: "了解如何使用 Azure 自动化来触发 StorSimple 数据管理器作业"
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
ms.openlocfilehash: 1e5fcbee664271058ac1c7fa80bb285e09b8579a
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2018
---
# <a name="use-azure-automation-to-trigger-a-job"></a>使用 Azure 自动化来触发作业

本文介绍如何使用 StorSimple Data Manager 服务中的数据转换功能来转换 StorSimple 设备数据。 可以通过两种方式启动数据转换作业： 

 - 使用 .NET SDK
 - 使用 Azure 自动化 Runbook
 
本文详细介绍了如何创建 Azure 自动化 Runbook，然后使用它来启动数据转换作业。 若要了解有关如何通过 .NET SDK 启动数据转换的详细信息，请转到[使用 .NET SDK 来触发数据转换作业](storsimple-data-manager-dotnet-jobs.md)。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保具备以下条件：

*   安装在客户端计算机上的 Azure PowerShell。 [下载 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。
*   资源组内 StorSimple 数据管理器服务中正确配置的作业定义。
*   从 GitHub 存储库下载 [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) 文件。 
*   从 GitHub 存储库下载 [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) 脚本。

## <a name="step-by-step-procedure"></a>分步过程

### <a name="set-up-the-automation-account"></a>设置自动化帐户

1. 在 Azure 门户中创建 Azure 运行方式自动化帐户。 为此，请转到“Azure Marketplace”>“所有”，并搜索“自动化”。 选择“自动化帐户”。

    ![创建自动化运行方式帐户](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. 若要添加新的自动化帐户，请单击“+ 添加”。

    ![创建自动化运行方式帐户](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. 在“添加自动化”中：

    1. 提供自动化帐户的“名称”。
    2. 选择已链接到 StorSimple 数据管理器服务的“订阅”。
    3. 创建新的资源组，或选择现有的资源组。
    4. 选择“位置” 。
    5. 保留选择默认的“创建运行方式帐户”选项。
    6. 若要在仪表板上获取快速访问的链接，请选中“固定到仪表板”。 单击“创建”。

    ![创建自动化运行方式帐户](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
    成功创建自动化帐户后，会收到通知。
    
    ![有关自动化帐户部署的通知](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

    有关详细信息，请参阅[创建运行方式帐户](../automation/automation-create-runas-account.md)。

3. 在新建的帐户中，转到“共享资源”>“模块”并单击“+ 添加模块”。

    ![导入模块 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. 从本地计算机浏览到 `DataTransformationApp.zip` 文件的位置，选择并打开模块。 单击“确定”导入模块。

    ![导入模块 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Azure 自动化将模块导入帐户时，将提取有关该模块的元数据。 此操作可能需要几分钟。

   ![导入模块 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. 将收到正在部署该模块的通知；完成此过程后，还会收到另一个通知。  将“模块”中的状态更改为“可用”。

    ![导入模块 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>导入、发布和运行自动化 Runbook

执行以下步骤来导入、发布和运行 Runbook 以触发作业定义。

1. 在 Azure 门户中，打开自动化帐户。 转到“过程自动化”>“Runbook”并单击“+ 添加 Runbook”。

    ![添加 Runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. 在“添加 Runbook”中，单击“导入现有的 Runbook”。

3. 指向 Runbook 文件的 Azure PowerShell 脚本文件 `Trigger-DataTransformation-Job.ps1`。 系统会自动选择 Runbook 类型。 提供 Runbook 的名称，并选择性地提供说明。 单击“创建”。

    ![添加 Runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. 新的 runbook 会出现在自动化帐户的 runbook 列表中。 选择并单击此 Runbook。

    ![添加 Runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. 编辑 Runbook 并单击“测试”窗格。

    ![添加 Runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. 提供参数，如 StorSimple 数据管理器服务的名称、已关联的资源组和作业定义的名称。 **开始**测试。 完成运行后，会生成报告。 有关详细信息，请转到如何[测试 Runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook)。

    ![添加 Runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. 在测试窗格中检查 Runbook 的输出。 如果对结果满意，请关闭窗格。 单击“发布”，出现确认提示时，请确认并发布该 Runbook。

    ![添加 Runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. 返回到“Runbook”并选择新创建的 Runbook。

    ![添加 Runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. 启动 Runbook。 在“启动 Runbook”中，输入所有参数。 单击“确定”，提交并启动数据转换作业。

10. 若要在 Azure 门户中监视作业进程，请转到 StorSimple 数据管理器服务中的“作业”。 选择并单击作业，查看作业详细信息。

    ![添加 Runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>后续步骤

[使用 StorSimple Data Manager UI 转换数据](storsimple-data-manager-ui.md)。