---
title: 如何计划 Azure SSIS 集成运行时 | Microsoft Docs
description: 本文介绍如何使用 Azure 自动化和数据工厂来计划 Azure SSIS 集成运行时的启动和停止。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/16/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f83715d2a382db271686210d9df285c255c09216
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113971"
---
# <a name="how-to-start-and-stop-the-azure-ssis-integration-runtime-on-a-schedule"></a>如何按计划启动和停止 Azure SSIS 集成运行时
本文介绍如何使用 Azure 自动化和 Azure 数据工厂来计划 Azure SSIS 集成运行时 (IR) 的启动和停止。 运行 Azure SSIS (SQL Server Integration Services) 集成运行时 (IR) 会产生相关的费用。 因此我们通常希望，只有需要在 Azure 中运行 SSIS 包时才运行 IR，在不需要该包时停止 IR。 可以使用数据工厂 UI 或 Azure PowerShell 来[手动启动或停止 Azure SSIS IR](manage-azure-ssis-integration-runtime.md)。

例如，可以使用 Webhook 将 Web 活动创建到 Azure 自动化 PowerShell runbook，并在它们之间链接执行 SSIS 包活动。 Web 活动可以在运行包之前和之后及时启动和停止 Azure-SSIS IR。 有关执行 SSIS 包活动的详细信息，请参阅[在 Azure 数据工厂中使用 SSIS 活动运行 SSIS 包](how-to-invoke-ssis-package-ssis-activity.md)。

## <a name="overview-of-the-steps"></a>步骤概述

下面是本文所述的概要步骤：

1. **创建并测试 Azure 自动化 Runbook。** 此步骤使用脚本创建一个 PowerShell Runbook 用于启动或停止 Azure SSIS IR。 然后，在“启动”和“停止”方案中测试该 Runbook，并确认 IR 是否已启动或停止。 
2. **为 Runbook 创建两个计划。** 对于第一个计划，请在 Runbook 中将“启动”配置为操作。 对于第二个计划，请在 Runbook 中将“停止”配置为操作。 针对这两个计划，指定运行 Runbook 的频率。 例如，可将第一个 Runbook 计划为在每天的上午 8 点运行，将第二个 Runbook 计划为在每天的晚上 11 点运行。 第一个 Runbook 在运行时将启动 Azure SSIS IR。 第二个 Runbook 在运行时将停止 Azure SSIS IR。 
3. **为 Runbook 创建两个 Webhook**，一个用于“启动”操作，另一个用于“停止”操作。 在数据工厂管道中配置 Web 活动时，将使用这些 Webhook 的 URL。 
4. **创建数据工厂管道**。 创建的管道由三个活动组成。 第一个 **Web** 活动调用第一个 Webhook 来启动 Azure SSIS IR。 **存储过程**活动运行一个 SQL 脚本来运行 SSIS 包。 第二个 **Web** 活动停止 Azure SSIS IR。 有关使用存储过程活动从数据工厂管道调用 SSIS 包的详细信息，请参阅[调用 SSIS 包](how-to-invoke-ssis-package-stored-procedure-activity.md)。 然后，创建一个计划触发器，用于将管道计划为按指定的频率运行。

## <a name="prerequisites"></a>先决条件
如果尚未预配 Azure SSIS 集成运行时，请遵照[此教程](tutorial-create-azure-ssis-runtime-portal.md)中的说明预配。 

## <a name="create-and-test-an-azure-automation-runbook"></a>创建并测试 Azure 自动化 Runbook
在本部分执行以下步骤： 

1. 创建 Azure 自动化帐户。
2. 在 Azure 自动化帐户中创建 PowerShell Runbook。 与 Runbook 关联的 PowerShell 脚本会根据针对“操作”参数指定的命令来启动或停止 Azure SSIS IR。 
3. 在启动和停止方案中测试 Runbook，以确认它是否正常工作。 

### <a name="create-an-azure-automation-account"></a>创建 Azure 自动化帐户
如果你没有 Azure 自动化帐户，请遵照此步骤中的说明创建一个。 有关详细步骤，请参阅[创建 Azure 自动化帐户](../automation/automation-quickstart-create-account.md)。 在执行此步骤的过程中，创建一个 **Azure 运行方式**帐户（Azure Active Directory 中的服务主体），并将其添加到 Azure 订阅的**参与者**角色。 请确保该订阅包含 Azure SSIS IR 所在的数据工厂。 Azure 自动化使用此帐户在 Azure 资源管理器中进行身份验证，并对资源执行操作。 

1. 启动 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
2. 登录到 [Azure 门户](https://portal.azure.com/)。    
3. 在左侧菜单中选择“新建”，然后依次选择“监视 + 管理”、“自动化”。 

    ![“新建”->“监视 + 管理”->“自动化”](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. 在“添加自动化帐户”窗口中执行以下步骤： 

    1. 指定自动化帐户的**名称**。 
    2. 选择包含 Azure SSIS IR 所在的数据工厂的**订阅**。 
    3. 对于“资源组”，请选择“新建”以创建新的资源组，或选择“使用现有项”以选择现有的资源组。 
    4. 选择自动化帐户的**位置**。 
    5. 确认“创建运行方式帐户”设置为“是”。 随后将在 Azure Active Directory 中创建一个服务主体。 该服务主体已添加到 Azure 订阅的“参与者”角色
    6. 选择“固定到仪表板”，以便在门户的仪表板上显示它。 
    7. 选择**创建**。 

        ![“新建”->“监视 + 管理”->“自动化”](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. 仪表板和通知中会显示**部署状态**。 
    
    ![部署自动化](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. 成功创建自动化帐户后，会显示其主页。 

    ![自动化主页](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>导入数据工厂模块

1. 在“共享资源”部分的左侧菜单中选择“模块”，确认模块列表中是否包含“AzureRM.Profile”和“AzureRM.DataFactoryV2”。

    ![验证所需的模块](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  转到 [AzureRM.DataFactoryV2 模块](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/)的 PowerShell 库，选择“部署到 Azure 自动化”，选择自动化帐户，然后选择“确定”。 返回查看左侧菜单“共享资源”部分中的“模块”并等待，直到看到 AzureRM.DataFactoryV2 模块的“状态”更改为“可用”。

    ![验证数据工厂模块](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  转到 [AzureRM.Profile 模块](https://www.powershellgallery.com/packages/AzureRM.profile/)的 PowerShell 库，单击“部署到 Azure 自动化”，选择自动化帐户，然后选择“确定”。 返回查看左侧菜单“共享资源”部分中的“模块”并等待，直到看到 AzureRM.Profile 模块的“状态”更改为“可用”。

    ![验证配置文件模块](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-a-powershell-runbook"></a>创建 PowerShell Runbook
以下过程提供创建 PowerShell Runbook 的步骤。 与 Runbook 关联的脚本会根据针对“操作”参数指定的命令来启动/停止 Azure SSIS IR。 本部分不提供有关创建 Runbook 的所有细节。 有关详细信息，请参阅[创建 Runbook](../automation/automation-quickstart-create-runbook.md) 一文。

1. 切换到“Runbook”选项卡，在工具栏中选择“+ 添加 Runbook”。 

    ![“添加 Runbook”按钮](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. 选择“创建新的 Runbook”并执行以下步骤： 

    1. 对于“名称”，请键入 **StartStopAzureSsisRuntime**。
    2. 对于“Runbook 类型”，请选择“PowerShell”。
    3. 选择**创建**。
    
        ![“添加 Runbook”按钮](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. 将以下脚本复制/粘贴到 Runbook 脚本窗口。 使用工具栏上的“保存”和“发布”按钮保存并发布该 Runbook。 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

    ![编辑 PowerShell Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
5. 在工具栏上选择“启动”按钮来测试该 Runbook。 

    ![“启动 Runbook”按钮](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. 在“启动 Runbook”窗口中执行以下步骤： 

    1. 对于“资源组名称”，请输入包含 Azure SSIS IR 所在的数据工厂的资源组的名称。 
    2. 对于“数据工厂名称”，请输入包含 Azure SSIS IR 的数据工厂的名称。 
    3. 对于“AZURESSISNAME”，请输入 Azure SSIS IR 的名称。 
    4. 对于“操作”，请输入“启动”。 
    5. 选择“确定”。  

        ![“启动 Runbook”窗口](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. 在作业窗口中，选择“输出”磁贴。 在作业的输出窗口中，等到依次出现消息“##### 正在启动 #####”和“##### 已完成 #####”。 启动 Azure SSIS IR 大约需要 20 分钟。 关闭“作业”窗口并返回到“Runbook”窗口。

    ![Azure SSIS IR - 已启动](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  重复上述两个步骤，但这一次要使用“停止”作为“操作”的值。 在工具栏上选择“启动”按钮再次启动该 Runbook。 指定资源组名称、数据工厂名称和 Azure SSIS IR 名称。 对于“操作”，请输入“停止”。 

    在作业的输出窗口中，等到依次出现消息“##### 正在停止 #####”和“##### 已完成 #####”。 停止 Azure SSIS IR 所需的时间比启动 Azure SSIS IR 要短一些。 关闭“作业”窗口并返回到“Runbook”窗口。

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>为 Runbook 创建计划来启动/停止 Azure SSIS IR
在上一部分，我们已创建了一个可以启动或停止 Azure SSIS IR 的 Azure 自动化 Runbook。 在本部分，我们为 Runbook 创建两个计划。 配置第一个计划时，请为“操作”参数指定“启动”。 类似地，在配置第二个计划时，请为“操作”指定“停止”。 有关创建计划的详细步骤，请参阅[创建计划](../automation/automation-schedules.md#creating-a-schedule)。

1. 在“Runbook”窗口中选择“计划”，然后在工具栏上选择“+ 添加计划”。 

    ![Azure SSIS IR - 已启动](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. 在“计划 Runbook”窗口中执行以下步骤： 

    1. 选择“将计划链接到 Runbook”。 
    2. 选择“创建新计划”。
    3. 在“新建计划”窗口中，为“名称”键入“每日启动 IR”。 
    4. 在“启动”部分的时间输入框中，指定比当前时间稍晚几分钟的时间。 
    5. 对于“重复周期 ”，请选择“定期”。 
    6. 在“重复间隔”部分选择“天”。 
    7. 选择**创建**。 

        ![计划 Azure SSIS IR 的启动](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. 切换到“参数和运行设置”选项卡。指定资源组名称、数据工厂名称和 Azure SSIS IR 名称。 对于“操作”，请输入“启动”。 选择“确定”。 再次选择“确定”，在 Runbook 的“计划”页上查看该计划。 

    ![计划启动 Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. 重复上述两个步骤，创建名为“每日停止 IR”的计划。 这次指定的时间应该比“每日启动 IR”计划中指定的时间至少晚 30 分钟。 对于“操作”，请指定“停止”。 
5. 在“Runbook”窗口上的左侧菜单中选择“作业”。 此时，应会看到计划在指定的时间创建的作业及其状态。 另外，还可以看到有关作业的详细信息（例如其输出），这些信息与测试 Runbook 时看到的信息类似。 

    ![计划启动 Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. 完成测试后，请禁用计划，方法是对其进行编辑，并选择“已启用”旁边的“否”。 在左侧菜单中选择“计划”，选择“每日启动 IR”/“每日停止 IR”，然后“已启用”旁边的“否”。 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>创建 Webhook 用于启动和停止 Azure SSIS IR
遵照[创建 Webhook](../automation/automation-webhooks.md#creating-a-webhook) 中的说明为 Runbook 创建两个 Webhook。 分别指定“启动”和“停止”作为第一和第二个 Webhook 的操作。 将两个 Webhook 的 URL 保存到某个位置（例如文本文件或 OneNote 笔记本）。 在数据工厂管道中配置 Web 活动时，将要使用这些 URL。 下图显示了创建一个启动 Azure SSIS IR 的 Webhook 的示例：

1. 在“Runbook”窗口上的左侧菜单中选择“Webhook”，然后在工具栏上选择“+ 添加 Webhook”。 

    ![“Webhook”->“添加 Webhook”](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. 在“添加 Webhook”窗口中，选择“创建新的 Webhook”并执行以下操作： 

    1. 对于“名称”，请输入 **StartAzureSsisIR**。 
    2. 确认“已启用”设置为“是”。 
    3. 复制“URL”并将其保存到某个位置。 此步骤非常重要， 因为设置后无法查看该 URL。 
    4. 选择“确定”。 

        ![“新建 Webhook”窗口](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. 切换到“参数和运行设置”选项卡。指定资源组名称、数据工厂名称和 Azure SSIS IR 名称。 对于“操作”，请输入“启动”。 单击“确定”。 然后单击“创建”。 

    ![Webhook - 参数和运行设置](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. 重复上述三个步骤创建名为 **StopAzureSsisIR** 的另一个 Webhook。 不要忘记复制 URL。 指定参数和运行设置时，请为“操作”输入“停止”。 

现在应有两个 URL：一个是 **StartAzureSsisIR** Webhook 的 URL，另一个是 **StopAzureSsisIR** Webhook 的 URL。 可将 HTTP POST 请求发送到这些 URL，以启动/停止 Azure SSIS IR。 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>创建和计划用于启动/停止 IR 的数据工厂管道
本部分介绍如何使用 Web 活动来调用在上一部分创建 Webhook。

创建的管道由三个活动组成。 

1. 第一个 **Web** 活动调用第一个 Webhook 来启动 Azure SSIS IR。 
2. “执行 SSIS 包”活动或“存储过程”活动运行 SSIS 包。
3. 第二个 **Web** 活动调用 Webhook 来停止 Azure SSIS IR。 

创建并测试管道后，请创建一个计划触发器，并将其与管道相关联。 计划触发器定义管道的计划。 假设要创建一个计划在每天晚上 11 点运行的触发器。 该触发器在每天的晚上 11 点运行管道。 该管道启动 Azure SSIS IR、执行 SSIS 包，然后停止 Azure SSIS IR。 

### <a name="create-a-data-factory"></a>创建数据工厂

1. 登录到 [Azure 门户](https://portal.azure.com/)。    
2. 在左侧菜单中单击“新建”，并依次单击“数据 + 分析”、“数据工厂”。 
   
   ![新建 -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. 在“新建数据工厂”页中，输入 **MyAzureSsisDataFactory** 作为**名称**。 
      
     ![“新建数据工厂”页](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Azure 数据工厂的名称必须 **全局唯一**。 如果收到错误，请更改数据工厂的名称（例如改为 yournameMyAzureSsisDataFactory），并重新尝试创建。 有关数据工厂项目命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
       `Data factory name �MyAzureSsisDataFactory� is not available`
3. 选择要在其中创建数据工厂的 Azure **订阅**。 
4. 对于**资源组**，请执行以下步骤之一：
     
      - 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 
      - 选择“新建”，并输入资源组的名称。   
         
      若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
4. 选择“V2”作为“版本”。
5. 选择数据工厂的**位置**。 列表中只会显示支持创建数据工厂的位置。
6. 选择“固定到仪表板”。     
7. 单击“创建”。
8. 在仪表板上，会看到状态为“正在部署数据工厂”的以下磁贴。 

    ![“正在部署数据工厂”磁贴](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. 创建完成后，可以看到图中所示的“数据工厂”页。
   
   ![数据工厂主页](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. 单击“创作和监视”，在单独的选项卡中启动数据工厂用户界面 (UI)。

### <a name="create-a-pipeline"></a>创建管道

1. 在“入门”页中，选择“创建管道”。 

   ![“入门”页](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. 在“活动”工具箱中展开“常规”，将“Web”活动拖放到管道设计器图面。 在“属性”窗口的“常规”选项卡中，将活动名称更改为 **StartIR**。

   ![第一个 Web 活动 -“常规”选项卡](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. 在“属性”窗口中切换到“设置”选项卡，然后执行以下操作： 

    1. 对于“URL”，请粘贴用于启动 Azure SSIS IR 的 Webhook 的 URL。 
    2. 对于“方法”，请选择“POST”。 
    3. 对于“正文”，请输入 `{"message":"hello world"}`。 
   
        ![第一个 Web 活动 -“设置”选项卡](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)

4. 从“活动”工具箱的“常规”部分拖放“执行 SSIS 包”活动或“存储过程”活动。 将活动名称设置为 **RunSSISPackage**。 

5. 如果选择“执行 SSIS 包”活动，请按照[在 Azure 数据工厂中使用 SSIS 活动运行 SSIS 包](how-to-invoke-ssis-package-ssis-activity.md)中的说明完成活动创建。  请确保指定充足的重试尝试次数以等待 Azure-SSIS IR 可用，因为它的启动需要多达 30 分钟。 

    ![重试设置](media/how-to-schedule-azure-ssis-integration-runtime/retry-settings.png)

6. 如果选择“存储过程”活动，请按照[在 Azure 数据工厂中使用存储过程活动调用 SSIS 包](how-to-invoke-ssis-package-stored-procedure-activity.md)中的说明完成活动创建。 确保插入 Transact-SQL 脚本等待 Azure-SSIS IR 可用，因为它的启动需要多达 30 分钟。
    ```sql
    DECLARE @return_value int, @exe_id bigint, @err_msg nvarchar(150)

    -- Wait until Azure-SSIS IR is started
    WHILE NOT EXISTS (SELECT * FROM [SSISDB].[catalog].[worker_agents] WHERE IsEnabled = 1 AND LastOnlineTime > DATEADD(MINUTE, -10, SYSDATETIMEOFFSET()))
    BEGIN
        WAITFOR DELAY '00:00:01';
    END

    EXEC @return_value = [SSISDB].[catalog].[create_execution] @folder_name=N'YourFolder',
        @project_name=N'YourProject', @package_name=N'YourPackage',
        @use32bitruntime=0, @runincluster=1, @useanyworker=1,
        @execution_id=@exe_id OUTPUT 

    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1

    EXEC [SSISDB].[catalog].[start_execution] @execution_id = @exe_id, @retry_count = 0

    -- Raise an error for unsuccessful package execution, check package execution status = created (1)/running (2)/canceled (3)/
    -- failed (4)/pending (5)/ended unexpectedly (6)/succeeded (7)/stopping (8)/completed (9) 
    IF (SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id = @exe_id) <> 7 
    BEGIN
        SET @err_msg=N'Your package execution did not succeed for execution ID: '+ CAST(@execution_id as nvarchar(20))
        RAISERROR(@err_msg, 15, 1)
    END
    ```

7. 将“Web”活动连接到“执行 SSIS 包”或“存储过程”活动。 

    ![连接“Web”和“存储过程”活动](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-sproc.png)

8. 将另一个“Web”活动拖放到“执行 SSIS 包”活动或“存储过程”活动的右侧。 将活动名称设置为 **StopIR**。 
9. 在“属性”窗口中切换到“设置”选项卡，然后执行以下操作： 

    1. 对于“URL”，请粘贴用于停止 Azure SSIS IR 的 Webhook 的 URL。 
    2. 对于“方法”，请选择“POST”。 
    3. 对于“正文”，请输入 `{"message":"hello world"}`。  
10. 将“执行 SSIS 包”活动或“存储过程”活动连接到最近的“Web”活动。

    ![完整管道](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
11. 单击工具栏中的“验证”来验证管道设置。 单击 **>>** 按钮关闭“管道验证报告”。 

    ![验证管道](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>测试性运行管道

1. 在管道的工具栏上选择“测试运行”。 底部窗格中的“输出”窗口内会显示输出。 

    ![测试运行](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. 在 Azure 自动化帐户的“Runbook”页中，可以检查是否运行了作业来启动和停止 Azure SSIS IR。 

    ![Runbook 作业](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. 启动 SQL Server Management Studio。 在“连接到服务器”窗口中执行以下操作： 

    1. 对于“服务器名称”，请指定 **&lt;Azure SQL 数据库&gt;.database.windows.net**。
    2. 选择“选项Options >>”。
    3. 对于“连接到数据库”，请选择“SSISDB”。
    4. 选择“连接”。 
    5. 展开“Integration Services 目录” -> “SSISDB”-> 你的文件夹 ->“项目”-> 你的 SSIS 项目 ->“包”。 
    6. 右键单击 SSIS 包，并选择“报告” -> “标准报告” -> “所有执行”。 
    7. 验证 SSIS 包是否已运行。 

        ![验证 SSIS 包是否已运行](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>计划管道 
按预期方式运行管道后，可以创建一个触发器，用于按指定的频率运行此管道。 有关如何将计划触发器与管道相关联的详细信息，请参阅[按计划触发管道](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)。

1. 在管道的工具栏上，依次选择“触发器”、“新建/编辑”。 

    ![“触发器”->“新建/编辑”](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. 在“添加触发器”窗口中，选择“+ 新建”。

    ![“添加触发器”-“新建”](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. 在“新建触发器”中执行以下操作： 

    1. 对于“名称”，请指定触发器的名称。 在以下示例中，**Run daily** 就是触发器的名称。 
    2. 对于“类型”，请选择“计划”。 
    3. 对于“开始日期”，请选择开始日期和时间。 
    4. 对于“重复周期”，请指定触发器的频率。 在以下示例中，频率为每日一次。 
    5. 对于“结束时间”，可以选择“日期”选项来指定日期和时间。 
    6. 选择“已激活”。 将解决方案发布到数据工厂后，会立即激活该触发器。 
    7. 选择“**下一步**”。

        ![“触发器”->“新建/编辑”](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. 在“触发器运行参数”页中查看以下警告，然后选择“完成”。 
5. 在左窗格中选择“全部发布”，将解决方案发布到数据工厂。 

    ![全部发布](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-the-pipeline-and-trigger-in-the-azure-portal"></a>监视 Azure 门户中的管道和触发器

1. 若要监视触发器运行和管道运行，请使用左侧的“监视”选项卡。 有关详细步骤，请参阅[监视管道](quickstart-create-data-factory-portal.md#monitor-the-pipeline)。

    ![管道运行](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
2. 若要查看与管道运行关联的活动运行，请在“操作”列中选择第一个链接（“查看活动运行”）。 可以看到与管道中每个活动关联的三个活动运行（第一个“Web”活动、“存储过程”活动和第二个“Web”活动）。 若要切换回到管道运行视图，请选择顶部的“管道”链接。

    ![活动运行](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
3. 还可以通过在顶部“管道运行”旁边的下拉列表中选择“触发器运行”，来查看触发器运行。 

    ![触发器运行](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-the-pipeline-and-trigger-with-powershell"></a>使用 PowerShell 监视管道和触发器

使用如下示例脚本来监视管道和触发器。

1. 获取管道运行的状态。

  ```powershell
  Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
  ```

2. 获取有关触发器的信息。

  ```powershell
  Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
  ```

3. 获取触发器运行的状态。

  ```powershell
  Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
  ```

## <a name="next-steps"></a>后续步骤
请参阅以下博客文章：
-   [使用 ADF 管道中的 SSIS 活动来实现 ETL/ELT 工作流的现代化并对其进行扩展](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

参阅 SSIS 文档中的以下文章： 

- [在 Azure 上部署、运行和监视 SSIS 包](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [连接到 Azure 上的 SSIS 目录](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [在 Azure 上计划包执行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [使用 Windows 身份验证连接到本地数据源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
