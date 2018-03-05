---
title: "监视和诊断 Azure Service Fabric 中的 Windows 容器 | Microsoft 文档"
description: "在本教程中，请设置对 Azure Service Fabric 上安排的 Windows 容器的监视和诊断。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: de77d10e4875173c7a067e945e473887d3cc7422
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-oms"></a>教程：使用 OMS 监视 Service Fabric 上的 Windows 容器

这是教程的第三部分，将详细介绍设置 OMS，以监视 Service Fabric 上安排的 Windows 容器的过程。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 为 Service Fabric 群集配置 OMS
> * 使用 OMS 工作区查看和查询容器和节点中的日志
> * 配置 OMS 代理，以收集容器和节点指标

## <a name="prerequisites"></a>先决条件
开始学习本教程之前，应做好以下准备：
- 在 Azure 上拥有一个群集，或者[使用此教程创建一个群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
- [将容器化的应用程序部署到该群集](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-oms-with-your-cluster-in-the-resource-manager-template"></a>在资源管理器模板中为群集设置 OMS

如果使用了本教程第一部分中[提供的模板](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Tutorial)，则它应包含常规 Service Fabric Azure 资源管理器模板的下列附加件。 如果拥有自己的群集，并且想要对它进行设置，以便使用 OMS 监视容器：
* 在资源管理器模板中进行以下更改。
* 使用 PowerShell 部署它，以通过[部署模板](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)升级群集。 Azure 资源管理器会确认此资源的存在，以将它作为升级内容推出。

### <a name="adding-oms-to-your-cluster-template"></a>将 OMS 添加到群集模板

在 template.json 中进行以下更改：

1. 将 OMS 工作区的位置和名称添加到参数部分：
    
    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your OMS Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your OMS workspace"
      }
    }
    ```

    若要更改其中任意一个所使用的值，可将同样的参数添加到 template.parameters.json，并更改该位置中使用的值。

2. 将解决方案名称和解决方案添加到变量中： 
    
    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. 将 OMS Microsoft Monitoring Agent 添加为虚拟机扩展。 找到虚拟机规模集资源：resources > *"apiVersion": "[variables('vmssApiVersion')]"*。 在 properties > virtualMachineProfile > extensionProfile > extensions 中的 ServiceFabricNode 扩展下，添加以下扩展说明： 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. 将 OMS 工作区添加为个人资源。 在“资源”中的虚拟机规模集资源后面，添加以下内容：
    
    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

可在[此处](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json)找到示例模板（本教程第一部分中使用过），其中包含上述所有更改，你可以根据需要参考这些更改。 通过这些更改，可将 OMS Log Analytics 工作区添加到资源组。 该工作区将被配置为收集已配置 [Windows Azure 诊断](service-fabric-diagnostics-event-aggregation-wad.md)代理的存储表中的 Service Fabric 平台事件。 此外，还将 OMS 代理 (Microsoft Monitoring Agent) 作为虚拟机扩展已添加到群集中的每个节点 - 这意味着，当你缩放群集时，会自动在每个虚拟机上配置此代理，并将它关联到同一个工作区。

部署包含新更改的模板，以升级当前的群集。 部署完成后，应在资源组中看到 OMS 资源。 群集就绪后，将容器化应用程序部署到该群集。 在下一步中，我们将设置对容器的监视。

## <a name="add-the-container-monitoring-solution-to-your-oms-workspace"></a>将容器监视解决方案添加到 OMS 工作区

若要在工作区中设置容器解决方案，请搜索容器监视解决方案，并创建容器资源（在“监视和管理”类别下）。

![添加容器解决方案](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

出现针对 OMS 工作区的提示后，选择已在资源组创建的工作区，然后单击“创建”。 此操作会将容器监视解决方案添加到工作区，并会自动让模板部署的 OMS 代理开始收集 docker 日志和统计信息。 

重新导航到资源组，现在你应该在该位置看到新添加的监视解决方案。 单击它后，登陆页面应显示正在运行的容器映像数量。 

请注意，此处运行了本教程[第二部分](service-fabric-host-app-in-a-container.md)中的 fabrikam 容器的 5 个实例

![容器解决方案登陆页面](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

单击“容器监视解决方案”后，会将你转到更加详细的仪表板，在其中可以滚动浏览多个面板并可在 Log Analytics 中运行查询。 

请注意，自 2017 年 9 月起，该解决方案一直在进行更新 - 请忽略你可能会收到的关于 Kubernetes 事件的任何错误消息，因为我们正在致力于将多个业务流程协调程序集成到同一个解决方案中。

由于该代理正在收集 docker 日志，因此，默认情况下它会显示 stdout 和 stderr。 如果滚动到右侧，将看到容器映像清单、状态、指标和示例查询，你可以运行这些查询以获取更多有用的数据。 

![容器解决方案仪表板](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

单击这些面板中的任意一个，均会将你转到 Log Analytics 查询，该查询将生成显示值。 将此查询更改为 \*，可看到正在收集的所有不同的日志类型。 在此处，你可以查询或筛选容器性能、日志，或查看 Service Fabric 平台事件。 此外，代理也会不断从各个节点发出检测信号，你可以查看这些信号，以确保群集配置更改后是否仍在从所有计算机收集数据。   

![容器查询](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-oms-agent-to-pick-up-performance-counters"></a>配置 OMS 代理以收集性能计数器

使用 OMS 代理的另一个好处是，可以通过 OMS 用户界面体验更改想要收集的性能计数器，而不必配置 Azure 诊断代理并每次都基于资源管理器模板进行升级。 若要执行此操作，请单击容器监视（或 Service Fabric）解决方案登陆页面上的“OMS 门户”。

![OMS 门户](./media/service-fabric-tutorial-monitoring-wincontainers/oms-portal.png)

此操作会将你转到 OMS 门户中的工作区，可以从中查看你的解决方案，创建自定义仪表板，以及配置 OMS 代理。 
* 单击屏幕右上角的齿轮，打开“设置”菜单。
* 单击“连接的源” > “Windows Server”，验证是否已连接了 5 个 Windows 计算机。
* 单击“数据” > “Windows 性能计数器”，搜索并添加新性能计数器。 此处，你将看到 OMS 提供的关于可收集的性能计数器的建议列表，以及用于搜索其他计数器的选项。 单击“添加选定的性能计数器”，以开始收集建议的指标。

    ![性能计数器](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters.png)

返回 Azure 门户，几分钟后刷新容器监视解决方案，应开始看到计算机性能数据出现。 此数据将有助于你了解的资源的使用情况。 此外，还可以使用这些指标做出适当的群集缩放决策，或者使用它们确认群集是否正在按照预期方式平衡负载。

注意：请确保已正确设置时间筛选器，以便于使用这些指标。 

![性能计数器 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 为 Service Fabric 群集配置 OMS
> * 使用 OMS 工作区查看和查询容器和节点中的日志
> * 配置 OMS 代理，以收集容器和节点指标

至此，你已设置对容器化应用程序的监视，接下来请尝试以下操作：

* 按照上述类似步骤操作，为 Linux 群集设置 OMS。 请参考[此模板](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux)，对资源管理器模板进行更改。
* 配置 OMS，设置[自动警报](../log-analytics/log-analytics-alerts.md)，以辅助检测和诊断。
* 浏览 Service Fabric 的[性能计数器建议](service-fabric-diagnostics-event-generation-perf.md)列表，以为群集配置它们。
* 掌握 Log Analytics 中提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能。