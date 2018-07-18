---
title: 从模板创建工作流 - Azure 逻辑应用 | Microsoft Docs
description: 使用逻辑应用模板构建工作流更快
author: kevinlam1
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; klam
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72aca1420d76a34f8f43f05b25d146eb743b3233
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298682"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>从预构建的模板创建逻辑应用工作流

为更快掌握工作流创建方法，逻辑应用提供了模板，此模板是按照常用模式预先构建的逻辑应用。 使用提供的这些模板，或者对其进行编辑以适合你的方案。

此处有一些模板类别：

| 模板类型 | 说明 | 
| ------------- | ----------- | 
| 企业云模板 | 用于集成 Azure Blob、Dynamics CRM、Salesforce、Box 以及其他连接器来满足企业云需求。 例如，可以使用这些模板组织业务领导方式或备份公司文件数据。 | 
| 个人工作效率模板 | 通过设置日常提醒、将重要工作项目添加为待办事项，以及通过自动化将耗时较长的任务缩减为单个用户同意的步骤，有助于提高个人工作效率。 | 
| 消费者云模板 | 用于集成 Twitter、Slack 和电子邮件等社交媒体服务。 对于增强社交媒体营销计划十分有用。 这些模板还包括云复制等任务，可节省花费在传统重复性任务上的时间提高工作效率。 | 
| Enterprise Integration Pack 模板 | 可用于配置 VETER 管道（验证、提取、转换、扩展、路由），通过 AS2 接收 X12 EDI 文档并将其转换为 XML，还可用于处理 X12 和 AS2 消息。 | 
| 协议模式模板 | 用于实现 HTTP 请求-响应及 FTP 和 SFTP 之间的集成等协议模式。 使用这些提供的模板，或在其上构建模板以处理复杂协议模式。 | 
||| 

如果还没有 Azure 订阅，可以在开始前[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 有关构建逻辑应用的详细信息，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-logic-apps-from-templates"></a>从模板创建逻辑应用

1. 如果尚未登录 [Azure 门户](https://portal.azure.com "Azure portal")，请先登录。

2. 在 Azure 主菜单中，选择“创建资源” > “企业集成” > “逻辑应用”。

   ![Azure 门户 >“新建”>“Enterprise Integration”>“逻辑应用”](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. 使用此图片下面表格中的设置创建逻辑应用：

   ![提供逻辑应用的详细信息](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | 设置 | 值 | 说明 | 
   | ------- | ----- | ----------- | 
   | **名称** | your-logic-app-name | 提供一个唯一的逻辑应用名称。 | 
   | **订阅** | your-Azure-subscription-name | 选择要使用的 Azure 订阅。 | 
   | **资源组** | your-Azure-resource-group-name | 创建或选择一个用于此逻辑应用的 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)，以便组织与此应用相关联的所有资源。 | 
   | **位置** | your-Azure-datacenter-region | 选择用于部署逻辑应用的数据中心区域，例如“美国西部”。 | 
   | **Log Analytics** | 关闭（默认）或者打开 | 通过 [Azure 日志分析](../log-analytics/log-analytics-overview.md)打开逻辑应用的[诊断日志记录](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app)。 需要已具有 Log Analytics 工作区。 | 
   |||| 

4. 准备就绪后，请选择“固定到仪表板”。 这样一来，逻辑应用就会自动显示在 Azure 仪表板上，并在部署后打开。 选择“创建”。

   > [!NOTE]
   > 如果不希望固定逻辑应用，则必须在部署后手动查找并打开逻辑应用，然后才能继续。

   在 Azure 部署逻辑应用以后，逻辑应用设计器会打开并显示一个包含简介视频的页面。 
   在视频下可以找到常用逻辑应用模式的模板。 

5. 略过简介视频和常见触发器，滚动到模板。 选择预构建的模板。 例如：

   ![选择逻辑应用模板](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > 请选择“空白逻辑应用”，以便可以从头开始构建逻辑应用。

   选择预建构模板时，可以查看有关该模板的详细信息。 
   例如：

   ![选择预构建的模板](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. 若要使用所选模板继续，请选择“使用此模板”。 

7. 根据模板中的连接器，可能提示你执行这些步骤：

   * 使用凭据登录到模板所引用的系统或服务。

   * 创建模板引用的服务或系统连接。 若要创建连接，请提供连接名称，并按需选择要使用的资源。 

   * 如果已设置这些连接，请选择“继续”。

   例如：

   ![创建连接](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   完成后，逻辑应用将会在逻辑应用设计器中开启并显示。

   > [!TIP]
   > 若要返回到模板查看器，请选择设计器工具栏上的“模板”。 此操作会放弃所有未保存更改，因此，会出现警告消息，以确认请求。

8. 继续建构逻辑应用。

   > [!NOTE] 
   > 许多模板包括已预先安装了所需属性的连接器。 不过，某些模板仍然要求提供值，才能正确部署逻辑应用。 如果在完成缺失的属性字段前部署应用，会收到错误消息。 

## <a name="update-logic-apps-with-templates"></a>使用模板更新逻辑应用

1. 在 [Azure 门户](https://portal.azure.com "Azure portal") 中的逻辑应用设计器中查找并打开逻辑应用。

2. 在设计器工具栏上，选择“模板”。 此操作会放弃所有未保存更改，因此，会出现警告消息，以便确认是否继续。 若确认执行请求，请选择“确定”。 例如：

   ![选择“模板”](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. 略过简介视频和常见触发器，滚动到模板。 选择预构建的模板。 例如：

   ![选择逻辑应用模板](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   选择预建构模板时，可以查看有关该模板的详细信息。 
   例如：

   ![选择预构建的模板](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. 若要使用所选模板继续，请选择“使用此模板”。 

5. 根据模板中的连接器，可能提示你执行这些步骤：

   * 使用凭据登录到模板所引用的系统或服务。

   * 创建模板引用的服务或系统连接。 若要创建连接，请提供连接名称，并按需选择要使用的资源。 

   * 如果已设置这些连接，请选择“继续”。

   ![创建连接](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   此时，逻辑应用将会逻辑应用设计器中开启并显示。

8. 继续建构逻辑应用。 

   > [!TIP]
   > 如果还未保存更改，可放弃操作，返回到先前的逻辑应用。 在设计器工具栏上，选择“放弃”。

> [!NOTE] 
> 许多模板包括已预先安装了所需属性的连接器。 不过，某些模板仍然要求提供值，才能正确部署逻辑应用。 如果在完成缺失的属性字段前部署应用，会收到错误消息。

## <a name="deploy-logic-apps-built-from-templates"></a>部署模板构建的逻辑应用

对模板进行更改之后，可保存所做更改。 此操作还自动将发布应用逻辑。

在设计器工具栏上，选择“保存”。

![保存并发布逻辑应用](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

了解构建逻辑应用的全部示例、方案、客户案例和演练。

> [!div class="nextstepaction"]
> [评审逻辑应用示例、方案和演练](../logic-apps/logic-apps-examples-and-scenarios.md)