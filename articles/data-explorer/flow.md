---
title: 微软 Azure 数据资源管理器流连接器（预览版）
description: 了解如何使用 Microsoft 流连接器创建自动计划或触发任务的流。
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 198b78d5bab15057fdb6c7f6d4e8fff9f77d496e
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397095"
---
# <a name="microsoft-flow-connector-preview"></a>微软流连接器（预览版）

Azure 数据资源管理器流连接器允许 Azure 数据资源管理器使用[Microsoft Power 自动的流功能](https://flow.microsoft.com/)作为计划或触发任务的一部分自动运行 Kusto 查询和命令。

常见使用方案包括：

* 发送包含表格和图表的每日报告
* 根据查询结果设置通知
* 在群集上调度控制命令
* 在 Azure 数据资源管理器和其他数据库之间导出和导入数据 

有关详细信息，请参阅[Microsoft Flow 连接器使用示例](flow-usage.md)。

##  <a name="log-in"></a>登录 

1. 登录到[微软电源自动。](https://flow.microsoft.com/)

1. 首次连接时，系统将提示您登录。

1. 选择“登录”**** 并输入凭据。

![“登录”对话框](./media/flow/flow-signin.png)

## <a name="authentication"></a>身份验证

您可以使用用户凭据或 AAD 应用程序进行身份验证。

> [!Note]
> 确保您的应用程序是[AAD 应用程序](https://docs.microsoft.com/azure/kusto/management/access-control/how-to-provision-aad-app)，并有权在群集上执行查询。

1. 选择 Microsoft 流连接器右上角的三个点：![添加连接](./media/flow/flow-addconnection.png)

1. 选择 **"添加新连接**"，然后选择"**与服务主体连接**"。
![登录对话框](./media/flow/flow-signin.png)

1. 输入所需信息：
    * 连接名称：新连接的描述性和有意义的名称
    * 客户端 ID：您的应用程序 ID
    * 客户端密钥：您的应用程序密钥
    * 租户：在其中创建应用程序的 AAD 目录的 ID。 例如，Microsoft 租户 ID 是：72f988bf-86f1-41af-91ab-2d7cd011db47

![应用程序身份验证](./media/flow/flow-appauth.png)

身份验证完成后，您将看到流使用新添加的连接。

![已完成应用程序身份验证](./media/flow/flow-appauthcomplete.png)

从现在起，此流将使用这些应用程序凭据运行。

## <a name="find-the-azure-kusto-connector"></a>查找 Azure 库斯托连接器

要使用 Microsoft Flow 连接器，您需要首先添加触发器。 可以基于定期时间段或对前一个流操作的响应定义触发器。

1. [创建新流](https://flow.microsoft.com/manage/flows/new)，或者从主页选择 **"我的流"** 操作，然后选择 **"新建**"。

    ![创建新流](./media/flow/flow-newflow.png)

1. 从空白添加计划。

    ![新的计划流](./media/flow/flow-scheduled-from-blank.png)

1. 在"生成计划流"页上输入所需信息。
    ![生成计划流](./media/flow/flow-build-scheduled-flow.png)
1. 选择“创建”  。
1. 选择 **= 新步骤**。
1. 在搜索框中，输入"库斯托"。

    ![选择流操作](./media/flow/flow-actions.png)

1. 选择**Azure 数据资源管理器**。

## <a name="flow-actions"></a>流操作

打开 Azure 数据资源管理器连接器时，可以向流添加三种可能的操作。

本节介绍每个 Microsoft Flow 操作的功能和参数。

![流 Azure 数据资源管理器操作](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>运行控制命令并可视化结果

使用"运行控制"命令并可视化结果操作来运行[控制命令](https://docs.microsoft.com/azure/kusto/management/index)。

1. 指定群集 URL。 例如： https://clusterName.eastus.kusto.windows.net
1. 输入数据库的名称。
1. 指定控制命令：
    * 从流中使用的应用和连接器中选择动态内容
    * 添加表达式以访问、转换和比较值
1. 要通过电子邮件发送此操作的结果作为表格或图表，请指定图表类型，该类型可以是：
    * HTML 表
    * 饼图
    * 时间图表
    * 条形图

![运行流控制命令](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> 在 *"群集名称"* 字段中，输入群集 URL。

### <a name="run-query-and-list-results"></a>运行查询和列表结果

> [!Note]
> 如果查询以点开头（表示它是[控件命令](https://docs.microsoft.com/azure/kusto/management/index)），请使用["运行"控件命令并可视化结果](#run-control-command-and-visualize-results)。

此操作向 Kusto 群集发送查询。 之后添加的操作会迭代查询结果的每一行。

以下示例每分钟触发查询，并根据查询结果发送电子邮件。 查询检查数据库中的行数，然后仅在行数大于 0 时发送电子邮件。 

![运行查询列表结果](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> 如果列有多个行，则连接器将针对列中的每一行运行。

### <a name="run-query-and-visualize-results"></a>运行查询并可视化结果
        
> [!Note]
> 如果查询以点开头（表示它是[控件命令](https://docs.microsoft.com/azure/kusto/management/index)），请使用["运行"控件命令并可视化结果](#run-control-command-and-visualize-results)。
        
使用"运行"查询并可视化结果操作，将 Kusto 查询结果可视化为表或图表。 例如，使用此流通过电子邮件接收每日 ICM 报告。 
    
在此示例中，查询的结果将作为 HTML 表返回。
            
![运行查询并可视化结果](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> 在 *"群集名称"* 字段中，输入群集 URL。

## <a name="email-kusto-query-results"></a>通过电子邮件发送库斯托查询结果

您可以在任何流中包括一个步骤，以便通过电子邮件将报告发送到任何电子邮件地址。 

1. 选择 **" 新步骤**"可向流添加新步骤。
1. 在搜索字段中，输入 Office 365 并选择**Office 365 Outlook**。
1. 选择**发送电子邮件 （V2）。**
1. 输入要发送电子邮件报告的电子邮件地址。
1. 输入电子邮件的主题。
1. 选择 **"代码"视图**。
1. 将光标放在 *"正文"* 字段中，然后选择 **"添加动态内容**"。
1. 选择**正文Html**。
    ![发送电子邮件](./media/flow/flow-send-email.png)
1. 选择 **"显示高级选项**"。
1. 在*附件名称 -1*字段中，选择**附件名称**。
1. 在 *"附件内容"* 字段中，选择**附件内容**。
1. 如有必要，添加更多附件。 
1. 如有必要，设置重要性级别。
1. 选择“保存”。 

![发送电子邮件](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>检查您的流是否成功

要检查流是否成功，请参阅流的运行历史记录：
1. 转到[微软流主页](https://flow.microsoft.com/)。
1. 从主菜单中，选择["我的流](https://flow.microsoft.com/manage/flows)"。
    ![我的流页](./media/flow/flow-myflows.png)
1. 在要调查的流行上，选择更多命令图标，然后**运行历史记录**。

    ![运行历史记录菜单](./media/flow//flow-runhistory.png)

    所有流运行都列出启动时间、持续时间和状态。
    ![运行历史记录结果页](./media/flow/flow-runhistoryresults.png)

    有关流的完整详细信息，请在"[我的流"](https://flow.microsoft.com/manage/flows)页上选择要调查的流。

    ![运行历史记录完整结果页](./media/flow/flow-fulldetails.png) 

要查看运行失败的原因，请选择运行开始时间。 此时将显示流，失败流的步骤由红色感叹号指示。 展开失败步骤以查看其详细信息。 右侧窗格包含有关故障的信息，以便您可以排除故障。

![流错误](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>超时异常

如果流运行超过 7 分钟，则可能会失败并返回"请求超时"异常。

了解有关[微软流量限制](#limitations)的更多。
    
同一查询可能在 Azure 数据资源管理器中成功运行，因为时间不受限制，可以更改。
            
下图显示了"请求超时"异常：
    
![流请求超时异常错误](./media/flow/flow-requesttimeout.png)
    
要修复超时问题，请尝试提高查询的效率，使其运行得更快，或将其划分为区块。 每个区块都可以在查询的不同部分上运行。

有关详细信息，请阅读[查询最佳实践](https://docs.microsoft.com/azure/kusto/query/best-practices)。

## <a name="limitations"></a>限制

* 返回给客户端的结果仅限于 500，000 条记录。 这些记录的总内存不能超过 64 MB 和 7 分钟执行时间。
* 连接器不支持[分叉](https://docs.microsoft.com/azure/kusto/query/forkoperator)和[分面](https://docs.microsoft.com/azure/kusto/query/facetoperator)运算符。
* 流量在微软边缘和Chrome上效果最好。

## <a name="next-steps"></a>后续步骤

了解[Microsoft Azure 资源管理器逻辑应用连接器](https://docs.microsoft.com/azure/kusto/tools/logicapps)，这是作为计划或触发任务的一部分自动运行 Kusto 查询和命令的另一种方法。
