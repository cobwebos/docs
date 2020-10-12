---
title: Azure Monitor 中的监视解决方案 | Microsoft Docs
description: Azure Monitor 中的监视解决方案是逻辑、可视化效果和数据采集规则的集合，提供围绕特定问题领域制定的指标。  本文提供了有关安装和使用监视解决方案的信息。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/07/2020
ms.openlocfilehash: d509862fe4dafff174ee03c3b5cc887fa9d9ff22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90085988"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Azure Monitor 中的监视解决方案

Azure Monitor 中的监视解决方案提供对特定 Azure 应用程序或服务的操作分析。 本文简要概述了 Azure 中的监视解决方案，并详细介绍如何了使用和安装这些解决方案。 可以在 Azure Monitor 中针对你使用的任何应用程序和服务添加监视解决方案。 这些解决方案通常是免费的，但收集的数据可能会产生使用费。

## <a name="use-monitoring-solutions"></a>使用监视解决方案

Azure Monitor 中的 "解决方案 **概述** " 页显示 Log Analytics 工作区中安装的每个解决方案的磁贴。 若要打开此页，请在 [Azure 门户](https://ms.portal.azure.com)中转到“Azure Monitor”。 在“见解”菜单下，选择“更多”以打开“见解中心”，然后单击“Log Analytics 工作区”。

[![见解中心](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


使用屏幕顶部的下拉框更改工作区或用于磁贴的时间范围。 单击解决方案的磁贴，以打开它的视图，其中包含它收集的数据的更详细分析。

[![屏幕截图显示具有所选解决方案的 "Azure 门户" 菜单和 "解决方案" 窗格中显示的解决方案。](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

监视解决方案可以包含多种类型的 Azure 资源。可以像查看其他任何资源一样查看解决方案包含的任何资源。 例如，解决方案中包含的任何日志查询都列在[查询资源管理器](../log-query/get-started-portal.md#load-queries)中的“解决方案查询”下，可在使用[日志查询](../log-query/log-query-overview.md)执行临时分析时使用这些查询。

## <a name="list-installed-monitoring-solutions"></a>列出已安装的监视解决方案

### <a name="portal"></a>[Portal](#tab/portal)

使用以下过程列出订阅中安装的监视解决方案。

1. 转到 [Azure 门户](https://ms.portal.azure.com)。 搜索并选择“解决方案”。
1. 将列出所有工作区中安装的解决方案。 解决方案名称的后面是在其中安装该解决方案的工作区的名称。
1. 使用屏幕顶部的下拉框可按订阅或资源组进行筛选。

![列出所有解决方案](media/solutions/list-solutions-all.png)

单击某个解决方案的名称打开其摘要页。 此页显示解决方案中包含的所有视图，并提供解决方案本身及其工作区的不同选项。 使用上述过程之一查看解决方案的摘要页以列出解决方案，然后单击解决方案的名称。

![解决方案属性](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az monitor log-analytics solution list](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-list) 命令列出订阅中安装的监视解决方案。   在运行 `list` 命令之前，请遵循[安装监视解决方案](#install-a-monitoring-solution)中找到的先决条件。

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>安装监视解决方案

### <a name="portal"></a>[Portal](#tab/portal)

[Azure 市场](https://azuremarketplace.microsoft.com)中提供了 Microsoft 和合作伙伴提供的监视解决方案。 可以搜索可用的解决方案，并使用以下过程进行安装。 安装解决方案时，必须选择要在其中安装该解决方案的 [Log Analytics 工作区](../platform/manage-access.md)，以及要将解决方案数据收集到的位置。

1. 在[订阅的解决方案列表](#list-installed-monitoring-solutions)中，单击“添加”。
1. 浏览或搜索解决方案。 也可以通过[此搜索链接](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)来浏览解决方案。
1. 找到所需的监视解决方案并阅读其说明。
1. 单击“创建”以启动安装进程。
1. 当安装过程开始时，系统会提示你指定 Log Analytics 工作区，并为解决方案提供任何所需的配置。

![安装解决方案](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>从社区安装解决方案

社区成员可以将管理解决方案提交到 Azure 快速入门模板。 可以直接安装这些解决方案，或者下载模板，以便今后安装。

1. 请遵循 [Log Analytics 工作区和自动化帐户](#log-analytics-workspace-and-automation-account)中所述的过程来链接工作区和帐户。
2. 转到 [Azure 快速入门模板](https://azure.microsoft.com/documentation/templates/)。
3. 搜索感兴趣的解决方案。
4. 从结果中选择解决方案以查看其详细信息。
5. 单击“**部署到 Azure**”按钮。
6. 除了解决方案中任何参数的值，系统还会提示提供资源组和位置等信息。
7. 单击“**购买**”可安装解决方案。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>准备环境

1. 安装 Azure CLI

   需要[安装 Azure CLI](/cli/azure/install-azure-cli)，然后才能运行 CLI 引用命令。  如果愿意，还可以使用 Azure Cloud Shell 来完成本文中的步骤。  Azure Cloud Shell 是一种通过浏览器使用的交互式 Shell 环境。  使用以下方法之一开始 Cloud Shell：

   - 通过转到打开 Cloud Shell [https://shell.azure.com](https://shell.azure.com)

   - 选择菜单栏上[Azure 门户](https://portal.azure.com)中右上角的 " **Cloud Shell** " 按钮

1. 登录。

   如果使用的是 CLI 的本地安装，请使用 [az login](/cli/azure/reference-index#az-login) 命令登录。  遵循终端中显示的步骤完成身份验证过程。

    ```azurecli
    az login
    ```

1. 安装 `log-analytics-solution` 扩展

   `log-analytics-solution` 命令是核心 Azure CLI 的实验性扩展。 在[将扩展与 Azure CLI 结合使用](/cli/azure/azure-cli-extensions-overview?)中详细了解扩展参考。

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   预期会出现以下警告。

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>通过 Azure CLI 安装解决方案

安装解决方案时，必须选择要在其中安装该解决方案的 [Log Analytics 工作区](../platform/manage-access.md)，以及要将解决方案数据收集到的位置。  使用 Azure CLI，你可以通过 [az monitor log-analytics workspace](/cli/azure/monitor/log-analytics/workspace) 引用命令来管理工作区。  请遵循 [Log Analytics 工作区和自动化帐户](#log-analytics-workspace-and-automation-account)中所述的过程来链接工作区和帐户。

使用 [az monitor log-analytics solution create](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution) 安装监视解决方案。  方括号中的参数是可选的。

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

下面是一个代码示例，用于为 OMSGallery/容器的计划产品创建日志分析解决方案。

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics 工作区和自动化帐户

所有监视解决方案都需要使用一个 [Log Analytics 工作区](../platform/manage-access.md)来存储解决方案收集的数据，以及托管其日志搜索和视图。 某些解决方案还需要使用一个[自动化帐户](../../automation/automation-security-overview.md)来包含 Runbook 和相关资源。 工作区和帐户必须满足以下要求。

* 解决方案的每项安装只能使用一个 Log Analytics 工作区和一个自动化帐户。 可将解决方案单独安装到多个工作区。
* 如果解决方案需要自动化帐户，则必须将 Log Analytics 工作区和自动化帐户相互链接。 一个 Log Analytics 工作区只能链接到一个自动化帐户，而一个自动化帐户也只能链接到一个 Log Analytics 工作区。

通过 Azure Marketplace 安装解决方案时，系统将提示你输入工作区和自动化帐户。 如果工作区与自动化帐户之间尚未建立链接，则系统会创建这种链接。

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>验证 Log Analytics 工作区和自动化帐户之间的链接

可以使用以下过程验证 Log Analytics 工作区和自动化帐户之间的链接。

1. 在 Azure 门户中选择自动化帐户。
1. 滚动到菜单的“相关资源”部分，然后选择“链接的工作区”。
1. 如果工作区已链接到自动化帐户，此页面会列出它链接到的工作区。 如果你选择所列出工作区的名称，则会被重定向到此工作区的概述页面。

## <a name="remove-a-monitoring-solution"></a>删除监视解决方案

### <a name="portal"></a>[Portal](#tab/portal)

若要通过门户删除已安装的解决方案，请在[已安装解决方案的列表](#list-installed-monitoring-solutions)中找到它。 单击解决方案的名称打开其摘要页，然后单击“删除”。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要通过 Azure CLI 删除已安装的解决方案，请使用 [az monitor log-analytics solution delete](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-delete) 命令。

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

* * *

## <a name="next-steps"></a>后续步骤

* 获取 [Microsoft 提供的监视解决方案的列表](../monitor-reference.md)。
* 了解如何[创建查询](../log-query/log-query-overview.md)来分析监视解决方案收集的数据。
* 查看所有[适用于 Azure Monitor 的 Azure CLI 命令](/cli/azure/azure-cli-reference-for-monitor)。
