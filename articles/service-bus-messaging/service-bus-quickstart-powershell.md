---
title: 使用 Azure PowerShell 创建服务总线队列
description: 在本快速入门中，你将了解如何使用 Azure PowerShell 创建服务总线命名空间并在其中创建队列。
author: spelluru
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 178f990e46801cd51e9feb88bbd20181842e4400
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "89077681"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>使用 Azure PowerShell 创建服务总线命名空间和队列
本快速入门介绍如何使用 Azure PowerShell 创建服务总线命名空间和队列。 本快速入门还介绍了如何获取客户端应用程序向队列发送消息或从队列接收消息所使用的授权凭据。 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保你有一个 Azure 订阅。 如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户][]。 

在本快速入门中，你将使用 Azure Cloud Shell，在登录 Azure 门户后即可启动该服务。 有关 Azure Cloud Shell 的详细信息，请参阅 [Azure Cloud Shell 概述](../cloud-shell/overview.md)。 你还可以在计算机上[安装](/powershell/azure/install-Az-ps)和使用 Azure PowerShell。 


## <a name="provision-resources"></a>预配资源
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 通过选择下图中显示的图标启动 Azure Cloud Shell： 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="启动 Cloud Shell":::
3. 在底部的 Cloud Shell 窗口中，从“Bash”切换到“PowerShell” 。 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="启动 Cloud Shell":::    
4. 运行以下命令来创建 Azure 资源组。 如有需要，请更新资源组名称和位置。 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. 运行以下命令以创建服务总线消息命名空间。 在此示例中，`ContosoRG` 是在上一步中创建的资源组。 `ContosoSBusNS` 是在该资源组中创建的服务总线命名空间的名称。 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. 运行以下命令，在上一步中创建的命名空间中创建一个队列。 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. 获取命名空间的主连接字符串。 使用此连接字符串连接到队列并发送和接收消息。 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
    ```

    请记下该连接字符串和队列名称。 使用它们发送和接收消息。 


## <a name="next-steps"></a>后续步骤
在本文中，你创建了服务总线命名空间并在其中创建了队列。 若要了解如何向队列发送消息或从队列接收消息，请参阅“发送和接收消息”部分中的以下某个快速入门。 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues-new-package.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

