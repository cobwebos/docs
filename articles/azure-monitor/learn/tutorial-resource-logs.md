---
title: 从 Azure 资源收集资源日志并使用 Azure Monitor 进行分析
description: 本教程介绍如何将诊断设置配置为将 Azure 资源中的资源日志收集到 Log Analytics 工作区中，以通过日志查询对这些这些日志进行分析。
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: d356042d65c419163de4951e64a635a22ea90e6d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "78269191"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>教程：从 Azure 资源收集和分析资源日志

资源日志提供有关 Azure 资源详细操作的见解，有助于监控其运行状况和可用性。 Azure 资源会自动生成资源日志，但必须配置这些日志的收集位置。 本教程将指导你完成创建诊断设置以收集 Azure 订阅中某个资源的资源日志，并使用日志查询对其进行分析。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure Monitor 中创建 Log Analytics 工作区
> * 创建诊断设置以收集资源日志 
> * 创建简单的日志查询以分析日志


## <a name="prerequisites"></a>先决条件

要完成本教程，需要一个要监控的 Azure 资源。 你可以使用 Azure 订阅中支持诊断设置的任何资源。 要确定资源是否支持诊断设置，请在 Azure 门户中前往其菜单，并验证菜单的“监视”部分是否有“诊断设置”选项   。


## <a name="log-in-to-azure"></a>登录 Azure
通过 [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户。


## <a name="create-a-workspace"></a>创建工作区
Azure Monitor 中的 Log Analytics 工作区可从各种源收集和索引日志数据，并允许使用功能强大的查询语言进行高级分析。 在创建诊断设置之前，需要存在 Log Analytics 工作区，以确保能将数据发送到工作区。 你可以使用 Azure 订阅中的现有工作区，也可以按照以下过程创建一个工作区。 

> [!NOTE]
> 在“Azure Monitor”菜单中处理 Log Analytics 工作区中的数据时，可在“Log Analytics 工作区”菜单中创建和管理工作区   。

1. 在“所有服务”中，选择“Log Analytics 工作区”   。
2. 单击屏幕顶部的“添加”，并为工作区提供以下详细信息  ：
   - **Log Analytics 工作区**：新工作区的名称。 此名称在所有 Azure Monitor 订阅中必须是全局唯一的。
   - **订阅**：选择用于存储工作区的订阅。 此订阅无需与要监控资源的订阅相同。
   - **资源组**：选择现有资源组，或单击“新建”，创建一个新资源组  。 此资源组无需与要监控资源的资源组相同。
   - **位置**：选择一个 Azure 区域或创建一个新区域。 此区域无需与要监控资源的位置相同。
   - **定价层**：选择“即用即付”  作为定价层。 之后可以更改定价层。 单击“Log Analytics 定价”链接以了解有关不同定价层的详细信息  。

    ![新建工作区](media/tutorial-resource-logs/new-workspace.png)

3. 单击“确定”以创建工作区  。

## <a name="create-a-diagnostic-setting"></a>创建诊断设置
[诊断设置](../platform/diagnostic-settings.md)定义特定资源的日志所发送到的位置。 单个诊断设置可具有多个[目标](../platform/diagnostic-settings.md#destinations)，但在本教程中，我们将只使用 Log Analytics 工作区。

1. 在资源设置菜单的“监控”部分，选择“诊断设置”   。
2. 此时应该会显示“未定义任何诊断设置”消息。 单击“添加诊断设置”  。

    ![诊断设置](media/tutorial-resource-logs/diagnostic-settings.png)

3. 每个诊断设置都有三个基本组成部分：
 
   - **Name**：此部分影响不大，只提供描述信息。
   - **目标**：要将日志发送到的一个或多个目标。 所有 Azure 服务共享同一组三个可能的目标。 每个诊断设置都可以定义一个或多个目标，但不能定义特定类型的多个目标。 
   - **类别**：要发送到每个目标的日志类别。 每个 Azure 服务的类别集将有所不同。

4. 选择“发送到 Log Analytics 工作区”，然后选择所创建的工作区  。
5. 选择要收集的类别。 请参阅每个服务的文档以了解其可用类别的定义。

    ![诊断设置](media/tutorial-resource-logs/diagnostic-setting.png)

6. 单击“保存”以保存这些设置  。

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>使用日志查询来检索日志
使用以 Kusto 查询语言 (KQL) 编写的日志查询从 Log Analytics 工作区检索数据。 Azure Monitor 中的见解和解决方案会提供日志查询来检索特定服务的数据，但你可以使用 Log Analytics 直接在 Azure 门户中处理日志查询及其结果。 

1. 在资源设置菜单的“监控”部分，选择“日志”   。
2. Log Analytics 打开时会显示一个空查询窗口，其中范围设置为资源。 所有查询都将只包括来自该资源的记录。

    > [!NOTE]
    > 如果从 Azure Monitor 菜单中打开“日志”，则会将范围设置为“Log Analytics 工作区”。 在这种情况下，所有查询都将包括工作区中的所有记录。
   
    ![日志](media/tutorial-resource-logs/logs.png)

4. 示例中显示的服务将资源日志写入 AzureDiagnostics 表，但其他服务可能会写入其他表  。 请参阅 [Azure 资源日志支持的服务、架构和类别](../platform/diagnostic-logs-schema.md)，以查看不同 Azure 服务使用的表。

    > [!NOTE]
    > 多个服务将资源日志写入 AzureDiagnostics 表。 如果从 Azure Monitor 菜单中启动 Log Analytics，则需要添加包含 `ResourceProvider` 列的 `where` 语句来指定特定服务。 从资源菜单启动 Log Analytics 时，范围设置为仅来自此资源的记录，因此不需要此列。 请参阅服务文档了解示例查询。


5. 键入查询，然后单击“运行”以检查结果  。 
6. 有关编写日志查询的教程，请参阅 [Azure Monitor 中的日志查询入门](../log-query/get-started-queries.md)。

    ![日志查询](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>后续步骤
你已了解如何将资源日志收集到 Log Analytics 工作区中，现在请完成有关编写日志查询的教程以分析此数据。

> [!div class="nextstepaction"]
> [Azure Monitor 中的日志查询入门](../log-query/get-started-queries.md)
