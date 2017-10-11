---
title: "使用 Azure 自动化触发作业 | Microsoft Docs"
description: "了解如何使用 Azure 自动化触发 StorSimple Data Manager 作业（个人预览版）"
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
ms.openlocfilehash: 9691408bcd80afb6eba534f26749b76dd3bfe315
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="use-azure-automation-to-trigger-a-job-private-preview"></a>使用 Azure 自动化触发作业（个人预览版）

本文介绍如何使用 Azure 自动化触发 StorSimple Data Manager 作业。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保具备以下条件：

*   已安装 Azure PowerShell。 [下载 Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。
*   用于初始化数据转换作业的配置设置（用于获取这些设置的说明在此处提供）。
*   已在资源组中的混合数据资源中正确配置的作业定义。
*   从 GitHub 存储库下载 `DataTransformationApp.zip` [zip](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) 文件。
*   从 GitHub 存储库下载 `Get-ConfigurationParams.ps1` [脚本](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Get-ConfigurationParams.ps1)。
*   从 GitHub 存储库下载 `Trigger-DataTransformation-Job.ps1` [脚本](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1)。

## <a name="step-by-step"></a>分步指南

### <a name="get-azure-active-directory-permissions-for-the-automation-job-to-run-the-job-definition"></a>获取自动化作业的 Azure Active Directory 权限，以运行作业定义

1. 若要检索 Active Directory 的配置参数，请执行以下步骤：

    1. 在本地计算机中打开 Windows PowerShell。 确保安装了 [Azure PowerShell](https://azure.microsoft.com/downloads/)。
    1. 运行 `Get-ConfigurationParams.ps1` 脚本（在之前下载的文件中）。 在 PowerShell 窗口中键入以下命令：

        ```
        .\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```

        ActiveDirectoryKey 是稍后要使用的密码。 输入你选择的密码。 AppName 可以是任意字符串。

2. 此脚本输出以下值，触发自动化 runbook 时应使用这些值。 记下这些值。

    - 客户端 ID
    - 租户 ID
    - Active Directory 密钥（与上面输入的密钥相同）
    - 订阅 ID

### <a name="set-up-the-automation-account"></a>设置自动化帐户

1. 登录到 Azure，并打开自动化帐户。
2. 单击“资产”磁贴以打开资产列表。
3. 单击“模块”磁贴以打开模块列表。
4. 单击“+ 添加模块”按钮，随即启动“添加模块”边栏选项卡。

    ![自动化帐户设置](./media/storsimple-data-manager-job-using-automation/add-module1m.png)

5. 从本地计算机中选择 `DataTransformationApp.zip` 文件后，单击“确定”导入模块。

   Azure 自动化将模块导入帐户时，将提取有关该模块的元数据。 此操作可能需要几分钟。

   ![自动化帐户设置](./media/storsimple-data-manager-job-using-automation/add-module2m.png)

   

6. 将收到正在部署该模块的通知；完成此过程后，还会收到另一个通知。  也可在“模块”磁贴中检查状态。

### <a name="to-import-the-runbook-that-triggers-the-job-definition"></a>导入触发作业定义的 runbook

1. 在 Azure 门户中，打开自动化帐户。
2. 单击“Runbook”磁贴打开 runbook 的列表。
3. 单击“+ 添加 runbook”，并单击“导入现有的 runbook”。

   ![导入现有的 runbook](./media/storsimple-data-manager-job-using-automation/import-a-runbook.png)

4. 单击“Runbook 文件”，并选择文件以导入 `Trigger-DataTransformation-Job.ps1`。
5. 单击“创建”以导入 runbook。 新的 runbook 会出现在自动化帐户的 runbook 列表中。
7. 单击 **Trigger-DataTransformation-Job** runbook，并单击“编辑”。
8. 系统提示确认时，请单击“发布”，并单击“是”。


### <a name="to-run-the-runbook"></a>运行 runbook：
1. 在 Azure 门户中，打开自动化帐户。
2. 单击“Runbook”磁贴打开 Runbook 的列表。
3. 单击 **Trigger-DataTransformation-Job**。
4. 单击“启动”以启动 Runbook **ResourceGroupName** 。

   ![启动 Runbook](./media/storsimple-data-manager-job-using-automation/run-runbook1m.png)

5. 在“启动 runbook”边栏选项卡中，输入所有参数。 单击“确定”提交数据转换作业。

   ![启动 Runbook](./media/storsimple-data-manager-job-using-automation/run-runbook2m.png)


## <a name="next-steps"></a>后续步骤

[使用 StorSimple Data Manager UI 转换数据](storsimple-data-manager-ui.md)。