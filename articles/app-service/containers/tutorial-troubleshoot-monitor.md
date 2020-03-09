---
title: 教程：使用 Azure Monitor 进行故障排除
description: 了解 Azure Monitor 和 Log Analytics 如何帮助你监视应用服务 Web 应用。 Azure Monitor 提供用于监视环境的综合解决方案，可最大限度地提高可用性。
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 2/28/2020
ms.openlocfilehash: c4aee7c7e78c6799874194697fb3bc9c4aa33b38
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227986"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>教程：使用 Azure Monitor 排查应用服务应用的问题

> [!NOTE]
> Azure Monitor 与应用服务的集成目前以[预览版](https://aka.ms/appsvcblog-azmon)提供。
>

[Linux 应用服务](app-service-linux-intro.md)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 提供用于收集、分析和处理来自云与本地环境的遥测数据的综合解决方案，可将应用程序和服务的可用性和性能最大化。

本教程介绍如何使用 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 排查应用问题。 示例应用中的代码故意耗尽内存并导致 HTTP 500 错误，使你可以使用 Azure Monitor 来诊断和解决问题。

完成后，某个示例应用将在与 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 集成的“Linux 上的应用服务”中运行。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用 Azure Monitor 配置 Web 应用
> * 将控制台日志发送到 Log Analytics
> * 使用日志查询来识别和排查 Web 应用错误

可以在 macOS、Linux、Windows 中执行本教程中的步骤。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要：

- [Azure 订阅](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>创建 Azure 资源

首先在本地运行多个命令，以设置要在本教程中使用的示例应用。 这些命令将克隆示例应用，创建 Azure 资源，创建部署用户，然后将应用部署到 Azure。 系统会提示输入在创建部署用户期间提供的密码。 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure master
```

## <a name="configure-azure-monitor-preview"></a>配置 Azure Monitor（预览版）

### <a name="create-a-log-analytics-workspace"></a>创建 Log Analytics 工作区

将示例应用部署到 Azure 应用服务后，接下来配置监视功能，以便在出现问题时对应用进行故障排除。 Azure Monitor 将日志数据存储在 Log Analytics 工作区中。 工作区是包含数据和配置信息的容器。

此步骤将创建一个用来为应用配置 Azure Monitor 的 Log Analytics 工作区。

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [对于 Azure Monitor Log Analytics，需要支付数据引入和数据保留费。](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>创建诊断设置

可以使用诊断设置将特定 Azure 服务的指标收集到 Azure Monitor 日志中，以使用日志查询将这些指标连同其他监视数据一起进行分析。 在本教程中，请启用 Web 服务器和标准输出/错误日志。 有关日志类型和说明的完整列表，请参阅[支持的日志类型](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types)。

运行以下命令创建 AppServiceConsoleLogs（标准输出/错误）和 AppServiceHTTPLogs（Web 服务器日志）的诊断设置。 请将 _\<app-name>_ 和 _\<workspace-name>_ 替换为自己的值。 

> [!NOTE]
> 前两个命令 `resourceID` 和 `workspaceID` 是要在 `az monitor diagnostic-settings create` 命令中使用的变量。 有关此命令的详细信息，请参阅[使用 Azure CLI 创建诊断设置](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-using-azure-cli)。
>

```bash

resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'

```

## <a name="troubleshoot-the-app"></a>排查应用问题

浏览到 `http://<app-name>.azurewebsites.net`。

示例应用 ImageConverter 将包含的图像从 `JPG` 转换为 `PNG`。 我们有意在本教程的代码中安排了一个 bug。 如果选择了足够多的图像，该应用会在转换图像期间生成 HTTP 500 错误。 假设在开发阶段并未考虑到这种情况。 你将使用 Azure Monitor 来排查该错误。

### <a name="verify-the-app-is-works"></a>验证应用是否正常工作

若要转换图像，请单击 `Tools` 并选择 `Convert to PNG`。

![单击“工具”并选择“转换为 PNG”](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

选择前两个图像，然后单击 `convert`。 随后会成功转换图像。

![选择前两个图像](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>中断应用

通过成功转换前两个图像来验证应用后，我们将尝试转换前五个图像。

![转换前五个图像](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

此操作失败，并生成一个在开发期间未测试过的 `HTTP 500` 错误。

![转换将导致 HTTP 500 错误](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>使用日志查询查看 Azure Monitor 日志

让我们看看 Log Analytics 工作区中提供了哪些日志。 

单击此 [Log Analytics 工作区链接](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces)在 Azure 门户中访问工作区。

在 Azure 门户中，选择 Log Analytics 工作区。

### <a name="log-queries"></a>日志查询

日志查询可帮助你充分利用在 Azure Monitor 日志中收集的数据的价值。 使用日志查询可以识别 AppServiceHTTPLogs 和 AppServiceConsoleLogs 中的日志。 有关日志查询的详细信息，请参阅[日志查询概述](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

### <a name="view-appservicehttplogs-with-log-query"></a>使用日志查询查看 AppServiceHTTPLogs

访问应用后，让我们查看 `AppServiceHTTPLogs` 中的与 HTTP 请求关联的数据。

1. 在左侧导航栏中单击 `Logs`。

![Log Analytics 工作区日志](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. 搜索 `appservice` 并双击 `AppServiceHTTPLogs`。

![Log Analytics 工作区表](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. 单击 `Run`。

![Log Analytics 工作区应用服务 HTTP 日志](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

`AppServiceHTTPLogs` 查询返回过去 24 小时发出的所有请求。 列 `ScStatus` 包含 HTTP 状态。 若要诊断 `HTTP 500` 错误，请将 `ScStatus` 限制为 500 并运行查询，如下所示：

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>使用日志查询查看 AppServiceConsoleLogs

确认 HTTP 500 错误后，让我们查看应用发出的标准输出/错误。 可在“AppServiceConsoleLogs”中找到这些日志。

(1) 单击 `+` 创建新查询。 

(2) 双击 `AppServiceConsoleLogs` 表并单击 `Run`。 

由于转换五个图像会导致服务器错误，可以根据 `ResultDescription` 筛选错误以确定应用是否也写入了错误，如下所示：

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

在 `ResultDescription` 列中，将看到以下错误：

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>联接 AppServiceHTTPLogs 和 AppServiceConsoleLogs

识别 HTTP 500 和标准错误后，需要确认这些消息之间是否存在关联。 接下来，根据时间戳 `TimeGenerated` 联接这些表。

> [!NOTE]
> 事先已准备好一个可执行以下操作的查询：
>
> - 在 HTTPLogs 中筛选 500 错误
> - 查询控制台日志
> - 根据 `TimeGenerated` 联接表
>

运行以下查询：

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

在 `ResultDescription` 列中，将会看到发生时间与 Web 服务器错误相同的以下错误：

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

消息在 `process.php` 的第 20 行指出内存已耗尽。 现已确认应用程序在出现 HTTP 500 错误期间生成了错误。 让我们查看代码以识别问题。

## <a name="identify-the-error"></a>识别错误

在本地目录中，打开 `process.php` 并查看第 20 行。 

```php
imagepng($imgArray[$x], $filename);
```

第一个参数 `$imgArray[$x]` 是变量，包含所有需要转换的 JPG（内存中图像）。 但是，`imagepng` 只需要正在转换的图像，而不需要所有图像。 预先加载图像是没有必要的，可能会导致内存耗尽，从而导致 HTTP 500 错误。 让我们更新代码以按需加载图像，然后查看是否解决了问题。 接下来，改善代码以解决内存问题。

## <a name="fix-the-app"></a>修复应用

### <a name="update-locally-and-redeploy-the-code"></a>在本地更新并重新部署代码

对 `process.php` 进行以下更改，以处理内存耗尽问题：

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

提交在 Git 中所做的更改，然后将代码更改推送到 Azure。

```bash
git commit -am "Load images on-demand in process.php"
git push azure master
```

### <a name="browse-to-the-azure-app"></a>转到 Azure 应用

浏览到 `http://<app-name>.azurewebsites.net`。 

转换图像应该不再会生成 HTTP 500 错误。

![在 Azure 应用服务中运行的 PHP 应用](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

使用以下命令删除诊断设置：

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
你已了解：

> [!div class="checklist"]
> * 使用 Azure Monitor 配置 Web 应用
> * 将日志发送到 Log Analytics
> * 使用日志查询来识别和排查 Web 应用错误

## <a name="nextsteps"></a> 后续步骤
* [使用 Azure Monitor 查询日志](../../azure-monitor/log-query/log-query-overview.md)
* [在 Visual Studio 中对 Azure 应用服务进行故障排除](../troubleshoot-dotnet-visual-studio.md)
* [在 HDInsight 中分析应用日志](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
