---
title: "非工作时间启动/停止 VM [预览版] 解决方案 | Microsoft Docs"
description: "VM 管理解决方案可按计划启动和停止 Azure Resource Manager 虚拟机，并通过 Log Analytics 进行主动监视。"
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/07/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b0fec06e4a167e615381fca17def46923d9f0f1b


---
# <a name="startstop-vms-during-offhours-preview-solution-in-automation"></a>自动化中的非工作时间启动/停止 VM [预览版] 解决方案
在非工作时间启动/停止 VM [预览版] 解决方案根据用户定义的计划启动和停止 Azure Resource Manager 虚拟机，通过 OMS Log Analytics 提供成功启动和停止虚拟机的自动化见解。  

## <a name="prerequisites"></a>先决条件
* Runbook 使用 [Azure 运行方式帐户](automation-sec-configure-azure-runas-account.md)。  运行方式帐户是首选的身份验证方法，因为它使用证书身份验证，而不是可能会过期或经常更改的密码。  
* 此解决方案只能管理与自动化帐户位于同一订阅和资源组中的 VM。  
* 此解决方案只能部署到以下 Azure 区域：澳大利亚东南部、美国东部、东南亚和西欧。  管理 VM 计划的 Runbook 可以面向任何区域中的 VM。  
* 若要在启动和停止 VM Runbook 完成时发送电子邮件通知，需有一个 Office 365 企业级订阅。  

## <a name="solution-components"></a>解决方案组件
此解决方案由以下要导入和添加到自动化帐户的资源组成。

### <a name="runbooks"></a>Runbook
| Runbook | 说明 |
| --- | --- |
| CleanSolution-MS-Mgmt-VM |从订阅中删除解决方案时，此 Runbook 将删除所有包含的资源和计划。 |
| SendMailO365-MS-Mgmt |此 Runbook 通过 Office 365 Exchange 发送电子邮件。 |
| StartByResourceGroup-MS-Mgmt-VM |此 Runbook 旨在启动位于指定 Azure 资源组列表中的 VM（经典 VM 与基于 ARM 的 VM）。 |
| StopByResourceGroup-MS-Mgmt-VM |此 Runbook 旨在停止位于指定 Azure 资源组列表中的 VM（经典 VM 与基于 ARM 的 VM）。 |

<br>

### <a name="variables"></a>变量
| 变量 | 说明 |
| --- | --- |
| **SendMailO365-MS-Mgmt** Runbook | |
| SendMailO365-IsSendEmail-MS-Mgmt |指定 StartByResourceGroup-MS-Mgmt-VM 和 StopByResourceGroup-MS-Mgmt-VM Runbook 是否可以在完成时发送电子邮件通知。  选择 **True** 可启用电子邮件警报，选择 **False** 则为禁用。 默认值为 **False**。 |
| **StartByResourceGroup-MS-Mgmt-VM** Runbook | |
| StartByResourceGroup-ExcludeList-MS-Mgmt-VM |输入要从管理操作中排除的 VM 名称；使用分号 (;) 分隔名称。 值区分大小写并支持通配符（星号）。 |
| StartByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt |可以追加到电子邮件正文开头的文本。 |
| StartByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt |指定包含电子邮件 Runbook 的自动化帐户的名称。  **请勿修改此变量。** |
| StartByResourceGroup-SendMailO365-EmailRunbookName-MS-Mgmt |指定电子邮件 Runbook 的名称。  StartByResourceGroup-MS-Mgmt-VM 和 StopByResourceGroup-MS-Mgmt-VM Runbook 使用此变量发送电子邮件。  **请勿修改此变量。** |
| StartByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt |指定包含电子邮件 Runbook 的资源组的名称。  **请勿修改此变量。** |
| StartByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt |指定电子邮件的主题行。 |
| StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt |指定电子邮件的收件人。  使用分号 (;) 输入不同的名称。 |
| StartByResourceGroup-TargetResourceGroups-MS-Mgmt-VM |输入要从管理操作中排除的 VM 名称；使用分号 (;) 分隔名称。 值区分大小写并支持通配符（星号）。  默认值（星号）包含订阅中的所有资源组。 |
| StartByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM |指定包含此解决方案所要管理的 VM 的订阅。  这必须是此解决方案的自动化帐户所在的同一个订阅。 |
| **StopByResourceGroup-MS-Mgmt-VM** Runbook | |
| StopByResourceGroup-ExcludeList-MS-Mgmt-VM |输入要从管理操作中排除的 VM 名称；使用分号 (;) 分隔名称。 值区分大小写并支持通配符（星号）。 |
| StopByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt |可以追加到电子邮件正文开头的文本。 |
| StopByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt |指定包含电子邮件 Runbook 的自动化帐户的名称。  **请勿修改此变量。** |
| StopByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt |指定包含电子邮件 Runbook 的资源组的名称。  **请勿修改此变量。** |
| StopByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt |指定电子邮件的主题行。 |
| StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt |指定电子邮件的收件人。  使用分号 (;) 输入不同的名称。 |
| StopByResourceGroup-TargetResourceGroups-MS-Mgmt-VM |输入要从管理操作中排除的 VM 名称；使用分号 (;) 分隔名称。 值区分大小写并支持通配符（星号）。  默认值（星号）包含订阅中的所有资源组。 |
| StopByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM |指定包含此解决方案所要管理的 VM 的订阅。  这必须是此解决方案的自动化帐户所在的同一个订阅。 |

<br>

### <a name="schedules"></a>计划
| 计划 | 说明 |
| --- | --- |
| StartByResourceGroup-Schedule-MS-Mgmt |StartByResourceGroup Runbook 的计划，执行此解决方案管理的 VM 启动操作。 |
| StopByResourceGroup-Schedule-MS-Mgmt |StopByResourceGroup Runbook 的计划，执行此解决方案管理的 VM 关闭操作。 |

### <a name="credentials"></a>凭据
| 凭据 | 说明 |
| --- | --- |
| O365Credential |指定用于发送电子邮件的有效 Office 365 用户帐户。  仅当 SendMailO365-IsSendEmail-MS-Mgmt 变量设置为 **True** 时才需要。 |

## <a name="configuration"></a>配置
执行以下步骤可将非工作时间启动/停止 VM [预览版] 解决方案添加到自动化帐户，然后配置变量来自定义该解决方案。

1. 在 Azure 门户的主屏幕中选择“应用商店”磁贴。  如果该磁贴不再固定在主屏幕中，请从左侧导航窗格中选择“新建”。  
2. 在“应用商店”边栏选项卡上的搜索框中键入“启动 VM”，然后从搜索结果中选择“在非工作时间启动/停止 VM [预览版]”解决方案。  
3. 在所选解决方案的“在非工作时间启动/停止 VM [预览版]”边栏选项卡中查看摘要信息，然后单击“创建”。  
4. 此时将显示“添加解决方案”边栏选项卡，系统会提示先要配置解决方案，然后才可以将它导入自动化订阅。<br><br> ![VM 管理中的“添加解决方案”边栏选项卡](media/automation-solution-vm-management/vm-management-solution-add-solution-blade.png)<br><br>
5. 在“添加解决方案”边栏选项卡中，选择“工作区”并在此处选择链接到自动化帐户所在的同一个 Azure 订阅的 OMS 工作区，或创建新的 OMS 工作区。  如果没有 OMS 工作区，可以选择“新建工作区”，然后在“OMS 工作区”边栏选项卡中执行以下操作： 
   
   * 指定新 **OMS 工作区**的名称。
   * 如果选择的默认值不合适，请从下拉列表中选择要链接到的**订阅**。
   * 对于**资源组**，可以创建新资源组，或选择现有资源组。  
   * 选择“位置” 。  目前可供选择的位置只有“澳大利亚东南部”、“美国东部”、“东南亚”和“西欧”。
   * 选择“定价层” 。  该解决方案提供两种定价层：免费和 OMS 付费层。  免费层的每日可收集数据量、保留期和 Runbook 作业运行时分钟数有限制。  OMS 付费级别对每日可收集的数据量没有限制。  
     
     > [!NOTE]
     > 虽然独立付费层显示为一个选项，但它不适用。  如果选择它并继续在订阅中创建此解决方案，它将失败。  正式发布此解决方案时，此问题将得到解决。<br>如果使用此解决方案，它将仅使用自动化作业分钟数和日志引入。  此解决方案不会将其他 OMS 节点添加到你的环境中。  
     > 
     > 
6. 在“OMS 工作区”边栏选项卡中提供所需的信息后，单击“创建”。  在验证信息和创建工作区时，可以在菜单中的“通知”下面跟踪操作进度。  随后将返回到“添加解决方案”边栏选项卡。  
7. 在“添加解决方案”边栏选项卡中，选择“自动化帐户”。  如果要创建新的 OMS 工作区，则还需要创建将与前面指定的新 OMS 工作区相关联的新自动化帐户，包括 Azure 订阅、资源组和区域。  可以选择“创建自动化帐户”，然后在“添加自动化帐户”边栏选项卡中提供以下信息： 
   
   * 在“名称”字段中输入自动化帐户的名称。
     
     系统会根据所选的 OMS 工作区自动填充所有其他选项，无法修改这些选项。  “Azure 运行方式帐户”是此解决方案为 Runbook 包含的默认身份验证方法。  单击“确定”后，系统会验证配置选项并创建自动化帐户。  可以在菜单中的“通知”下面跟踪操作进度。 
     
     也可以选择现有的自动化运行方式帐户。  请注意，选择的帐户不能已链接到另一个 OMS 工作区，否则边栏选项卡中会显示一条通知消息。  如果该帐户已链接，则需要选择不同的自动化运行方式帐户或创建新帐户。<br><br> ![自动化帐户已链接到 OMS 工作区](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>
8. 最后，在“添加解决方案”边栏选项卡中选择“配置”，此时将显示“参数”边栏选项卡。  在“参数”边栏选项卡中，系统会提示：  
   
   * 指定“目标资源组名称”，此资源组名称包含此解决方案要管理的 VM。  可以输入多个名称，使用分号分隔即可（这些值区分大小写）。  如果想要针对订阅中的所有资源组内的 VM，可以使用通配符。
   * 选择“计划”，这是启动和停止目标资源组中 VM 的周期性日期和时间。  
9. 完成配置解决方案所需的初始设置后，选择“创建”。  系统将验证所有设置，然后尝试在订阅中部署该解决方案。  此过程需要几秒钟才能完成，可以在菜单中的“通知”下面跟踪进度。 

## <a name="collection-frequency"></a>收集频率
自动化作业日志和作业流数据每隔五分钟引入 OMS 存储库。  

## <a name="using-the-solution"></a>使用解决方案
在 OMS 工作区中添加 VM 管理解决方案时，“StartStopVM 视图”磁贴将添加到 OMS 仪表板。  此磁贴显示解决方案中已启动和成功完成的 Runbook 作业计数和图形表示。<br><br> ![VM 管理 StartStopVM 视图磁贴](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

在自动化帐户中，可以通过选择“解决方案”磁贴，然后从“解决方案”边栏选项卡上的列表中选择“Start-Stop-VM[Workspace]”解决方案，来访问和管理该解决方案。<br><br> ![自动化解决方案列表](media/automation-solution-vm-management/vm-management-solution-autoaccount-solution-list.png)  

选择该解决方案会显示“Start-Stop-VM[Workspace]”解决方案边栏选项卡，可以在其中查看重要详细信息，例如，就像在 OMS 工作区中一样，“StartStopVM”磁贴显示解决方案中已启动和成功完成的 Runbook 作业计数与图形表示。<br><br> ![自动化 VM 解决方案边栏选项卡](media/automation-solution-vm-management/vm-management-solution-solution-blade.png)  

在此处，还可以打开 OMS 工作区并对作业记录执行进一步的分析。  只需单击“所有设置”，在“设置”边栏选项卡中选择“快速启动”，然后在“快速启动”边栏选项卡选择“OMS 门户”。   此时将打开新选项卡或新浏览器会话，并显示与自动化帐户和订阅关联的 OMS 工作区。  

### <a name="configuring-email-notifications"></a>配置电子邮件通知
若要在完成启动和停止 VM Runbook 时启用电子邮件通知，需要修改 **O365Credential** 凭据，并且至少需要修改以下变量：

* SendMailO365-IsSendEmail-MS-Mgmt
* StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt
* StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt

若要配置 **O365Credential** 凭据，请执行以下步骤：

1. 在自动化帐户中，单击窗口顶部的“所有设置”。 
2. 在“设置”边栏选项卡中的“自动化资源”部分下面，选择“资产”。 
3. 在“资产”边栏选项卡中选择“凭据”磁贴，然后在“凭据”边栏选项中选择“O365Credential”。  
4. 输入有效的 Office 365 用户名和密码，然后单击“保存”保存更改。  

若要配置前面突出显示的变量，请执行以下步骤：

1. 在自动化帐户中，单击窗口顶部的“所有设置”。 
2. 在“设置”边栏选项卡中的“自动化资源”部分下面，选择“资产”。 
3. 在“资产”边栏选项卡中选择“变量”磁贴，在“变量”边栏选项卡中选择上面所列的变量，然后按照前面在[变量](##variables)部分中为其指定的说明来修改其值。  
4. 单击“保存”保存对变量所做的更改。   

### <a name="modifying-the-startup-and-shutdown-schedule"></a>修改启动和关闭计划
按照[在 Azure 自动化中计划 Runbook](automation-scheduling-a-runbook.md) 中所述的相同步骤管理此解决方案中的启动和关闭计划。  请记住，计划配置无法修改。  需要禁用现有计划并新建一个，然后将新计划链接到计划要应用到的 **StartByResourceGroup-MS-Mgmt-VM** 或 **StopByResourceGroup-MS-Mgmt-VM** Runbook。   

## <a name="log-analytics-records"></a>Log Analytics 记录
自动化在 OMS 存储库中创建两种类型的记录。

### <a name="job-logs"></a>作业日志
| 属性 | 说明 |
| --- | --- |
| 调用方 |谁启动了该操作。  可能的值为电子邮件地址或计划作业的系统。 |
| 类别 |数据类型的分类。  对于自动化，该值为 JobLogs。 |
| CorrelationId |用作 Runbook 作业相关性 ID 的 GUID。 |
| JobId |用作 Runbook 作业 ID 的 GUID。 |
| operationName |指定在 Azure 中执行的操作类型。  对于自动化，该值为 Job。 |
| resourceId |指定 Azure 中的资源类型。  对于自动化，该值是与 Runbook 关联的自动化帐户。 |
| resourceGroup |指定 Runbook 作业的资源组名称。 |
| ResourceProvider |指定  Azure 服务，它提供可部署和管理的资源。  对于自动化，该值为 Azure Automation。 |
| ResourceType |指定 Azure 中的资源类型。  对于自动化，该值是与 Runbook 关联的自动化帐户。 |
| resultType |Runbook 作业的状态。  可能的值包括：<br>- Started（已启动）<br>- Stopped（已停止）<br>- Suspended（已暂停）<br>- Failed（失败）<br>- Succeeded（成功） |
| resultDescription |描述 Runbook 作业结果状态。  可能的值包括：<br>- 作业已启动<br>- 作业失败<br>- 作业已完成 |
| RunbookName |指定 Runbook 的名称。 |
| SourceSystem |指定所提交数据的源系统。  对于自动化，该值为 :OpsManager |
| StreamType |指定事件的类型。 可能的值包括：<br>- Verbose（详细）<br>- Output（输出）<br>- Error（错误）<br>- Warning（警告） |
| SubscriptionId |指定作业的订阅 ID。 |
| 时间 |执行 Runbook 作业的日期和时间。 |

### <a name="job-streams"></a>作业流
| 属性 | 说明 |
| --- | --- |
| 调用方 |谁启动了该操作。  可能的值为电子邮件地址或计划作业的系统。 |
| 类别 |数据类型的分类。  对于自动化，该值为 JobStreams。 |
| JobId |用作 Runbook 作业 ID 的 GUID。 |
| operationName |指定在 Azure 中执行的操作类型。  对于自动化，该值为 Job。 |
| resourceGroup |指定 Runbook 作业的资源组名称。 |
| resourceId |指定 Azure 中的资源 ID。  对于自动化，该值是与 Runbook 关联的自动化帐户。 |
| ResourceProvider |指定  Azure 服务，它提供可部署和管理的资源。  对于自动化，该值为 Azure Automation。 |
| ResourceType |指定 Azure 中的资源类型。  对于自动化，该值是与 Runbook 关联的自动化帐户。 |
| resultType |生成事件时 Runbook 作业的结果。  可能的值包括：<br>- InProgress |
| resultDescription |包括来自 Runbook 的输出流。 |
| RunbookName |Runbook 的名称。 |
| SourceSystem |指定所提交数据的源系统。  对于自动化，该值为 OpsManager |
| StreamType |作业流的类型。 可能的值包括：<br>- Progress（进度）<br>- Output（输出）<br>- Warning（警告）<br>- Error（错误）<br>- Debug（调试）<br>- Verbose（详细） |
| 时间 |执行 Runbook 作业的日期和时间。 |

如果执行的日志搜索返回 **JobLogs** 或 **JobStreams** 类别的记录时，可以选择 **JobLogs** 或 **JobStreams** 视图，其中显示了一组汇总搜索所返回的更新的磁贴。

## <a name="sample-log-searches"></a>示例日志搜索
下表提供了此解决方案收集的作业记录的示例日志搜索。 

| 查询 | 说明 |
| --- | --- |
| 查找已成功完成的 Runbook StartVM 作业 |Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Succeeded &#124; measure count() by JobId_g |
| 查找已成功完成的 Runbook StopVM 作业 |Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Failed &#124; measure count() by JobId_g |
| 显示 StartVM 和 StopVM Runbook 在不同时间的作业状态 |Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" OR "StopByResourceGroup-MS-Mgmt-VM" NOT(ResultType="started") |

## <a name="next-steps"></a>后续步骤
* 若要详细了解如何使用 Log Analytics 构造不同的搜索查询和查看自动化作业日志，请参阅 [Log searches in Log Analytics](../log-analytics/log-analytics-log-searches.md)（Log Analytics 中的日志搜索）
* 若要详细了解 Runbook 执行方式、如何监视 Runbook 作业和其他技术详细信息，请参阅 [Track a runbook job](automation-runbook-execution.md)（跟踪 Runbook 作业）
* 若要了解有关 OMS Log Analytics 和数据收集源的详细信息，请参阅 [Collecting Azure storage data in Log Analytics overview](../log-analytics/log-analytics-azure-storage.md)（在 Log Analytics 中收集 Azure 存储数据概述）




<!--HONumber=Nov16_HO2-->


