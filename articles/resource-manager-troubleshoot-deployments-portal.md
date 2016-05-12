<properties
   pageTitle="使用门户对部署进行故障排除 | Azure"
   description="介绍如何使用 Azure 门户来检测和解决资源管理器部署的问题。"
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.date="03/21/2016"
   wacn.date=""/>

# 使用 Azure 门户对资源组部署进行故障排除

> [AZURE.SELECTOR]
- [门户](/documentation/articles/resource-manager-troubleshoot-deployments-portal)
- [PowerShell](/documentation/articles/resource-manager-troubleshoot-deployments-powershell)
- [Azure CLI](/documentation/articles/resource-manager-troubleshoot-deployments-cli)
- [REST API](/documentation/articles/resource-manager-troubleshoot-deployments-rest)

如果在将资源部署到 Azure 时发生错误，你需要进行故障排除。Azure 门户提供了一个界面让你轻松找到错误并确定可能的解决方法。

可以通过查看审核日志或部署操作来对部署进行故障排除。本主题将演示这两种方法。

如果部署之前先验证模板和基础结构，则可以避免一些错误。有关详细信息，请参阅 [Deploy a resource group with Azure Resource Manager template（使用 Azure Resource Manager 模板部署资源组）](/documentation/articles/resource-group-template-deploy)。

## 使用审核日志进行故障排除

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

若要查看部署相关的错误，请使用以下步骤：

1. 通过在门户中选择“浏览”和“审核日志”来查看审核日志。

    ![选择审核日志](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. 在“审核日志”边栏选项卡中，你将看到订阅中所有资源组的最新操作摘要。其中包括表示操作的时间与状态的图形，以及操作的列表。

    ![显示操作](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. 你可以在列表中选择任意操作。选择包含你要调查的错误的操作。

    ![选择操作](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
4. 你将看到该操作的所有事件。请注意摘要中的**相关性 ID**。此 ID 用于跟踪相关的事件。与技术支持人员合作排查问题时，此 ID 非常有用。你可以选择任一事件，以查看有关该事件的详细信息。

    ![选择事件](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

5. 你将看到有关事件的详细信息。请特别注意“属性”，其中提供了有关错误的信息。

    ![显示审核日志详细信息](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

你可以筛选审核日志的视图，以便重点关注特定的条件。若要自定义审核日志的视图，请执行以下操作：

1. 选择“审核日志”边栏选项卡顶部的“筛选”。

    ![筛选日志](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

2. 从“筛选”边栏选项卡中选择条件，以将审核日志的视图限制为你要查看的操作。例如，你可以筛选操作，以便只显示特定资源组的错误。

    ![设置筛选选项](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

3. 你可以设置时间跨度以进一步筛选操作。下图将视图筛选为包含特定的 20 分钟时间跨度的信息。

    ![设置时间](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

更新审核日志的视图之后，你只会看到符合指定条件的操作。下次你查看审核日志时，这些设置仍会保留，因此可能需要更改这些值才能扩展操作视图。

你很有可能会找出部署失败的原因。你也可以查看部署操作以获取状态信息，如下部分中所示。

## 使用部署操作进行故障排除

若要查看部署操作，请使用以下步骤 ：

1. 对于部署中涉及的资源组，请注意最后一个部署的状态。你可以选择此状态以获取更多详细信息。

    ![部署状态](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. 你将看到最近的部署历史记录。选择失败的部署。

    ![部署状态](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. 查看有关部署的信息，然后选择失败的操作以查看有关错误的详细信息。

    ![查看失败的部署](./media/resource-manager-troubleshoot-deployments-portal/view-failed-deployment.png)

4. 在“操作详细信息”边栏选项卡中，你将看到有关操作失败的信息。请特别注意状态消息。

    ![查看状态消息](./media/resource-manager-troubleshoot-deployments-portal/operations-status.png)



## 后续步骤

- 若要了解如何使用审核日志来监视其他类型的操作，请参阅 [Audit operations with Resource Manager（使用资源管理器执行审核操作）](/documentation/articles/resource-group-audit)。
- 若要在执行部署之前验证部署，请参阅 [Deploy a resource group with Azure Resource Manager template（使用 Azure Resource Manager 模板部署资源组）](/documentation/articles/resource-group-template-deploy)。

<!---HONumber=Mooncake_0503_2016-->