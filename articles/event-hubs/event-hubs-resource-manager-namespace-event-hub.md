---
title: 快速入门：创建包含使用者组的事件中心 - Azure 事件中心
description: 快速入门：使用 Azure 资源管理器模板创建包含事件中心和使用者组的事件中心命名空间
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 6ddf17030da8b0ff50f10938221b9c1dba08084d
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964288"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>快速入门：使用 Azure 资源管理器模板创建事件中心

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。 在本快速入门中，你将使用 [Azure 资源管理器模板](../azure-resource-manager/management/overview.md)创建一个事件中心。 然后部署 Azure 资源管理器模板，以创建包含一个事件中心的[事件中心](event-hubs-what-is-event-hubs.md)类型的命名空间。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

无。

## <a name="create-an-event-hub"></a>创建事件中心

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-eventhubs-create-namespace-and-eventhub/)。

:::code language="json" source="~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json" range="1-61" highlight="32-59":::

该模板中定义的资源包括：

- [**Microsoft.EventHub/namespaces**](/azure/templates/microsoft.eventhub/namespaces)
- [**Microsoft.EventHub/namespaces/eventhubs**](/azure/templates/microsoft.eventhub/namespaces/eventhubs)

若要查找更多模板示例，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular)。

### <a name="deploy-the-template"></a>部署模板

若要部署模板：

1. 从后续代码块中选择“试用”，然后按说明登录到 Azure Cloud Shell。

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   创建事件中心需要花费片刻时间。

1. 选择“复制”以复制 PowerShell 脚本。
1. 右键单击 shell 控制台并选择“粘贴”。

## <a name="verify-the-deployment"></a>验证部署

若要验证部署，可以从 [Azure 门户](https://portal.azure.com)打开资源组，或使用以下 Azure PowerShell 脚本。  如果 Cloud Shell 仍处于打开状态，则无需复制/运行第一行 (Read-Host)。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。 如果 Cloud Shell 仍处于打开状态，则无需复制/运行第一行 (Read-Host)。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>后续步骤

在本文中，你已创建一个事件中心命名空间，并在该命名空间中创建了一个事件中心。 有关如何将事件发送到事件中心（或）从事件中心接收事件的分步说明，请参阅“发送和接收事件”教程：

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C（仅发送）](event-hubs-c-getstarted-send.md)
- [Apache Storm（仅接收）](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
