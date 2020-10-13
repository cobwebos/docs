---
title: 使用 Azure 开发测试实验室和 Azure 资源管理器模板创建实验室
description: 本快速入门使用 Azure 资源管理器模板（ARM 模板）在 Azure 开发测试实验室中创建实验室。 实验室管理员会设置实验室、创建实验室中的 VM 并配置策略。
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/26/2020
ms.openlocfilehash: 2b825b4d4485f401199556b6faaef0017f583cc1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91461183"
---
# <a name="quickstart-set-up-a-lab-by-using-azure-devtest-labs-arm-template"></a>快速入门：使用 Azure 开发测试实验室 ARM 模板设置实验室
在本快速入门中，你将使用 Azure 资源管理器模板（ARM 模板）创建具有 Windows Server 2019 Datacenter VM 的实验室。 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

在本快速入门中，你将执行以下操作：

> [!div class="checklist"]
> * 查看模板 
> * 部署模板
> * 验证模板
> * 清理资源

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/)。

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json":::

该模板中定义的资源包括：

- [Microsoft.DevTestLab/labs](/azure/templates/microsoft.devtestlab/labs)
- [Microsoft.DevTestLab labs/virtualnetworks](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [Microsoft.DevTestLab labs/virtualmachines](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

若要查找更多模板示例，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab)。

## <a name="deploy-the-template"></a>部署模板
若要自动运行部署，请单击以下按钮。 

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. 创建新的资源组，以便之后可以轻松地进行清理。
1. 选择资源组的位置。 
1. 输入实验室名称。 
1. 输入 VM 名称。 
1. 输入可以访问 VM 的用户名。 
1. 输入对应于该用户的**密码**。 
1. 选择“我同意上述条款和条件”。 
1. 然后，选择“购买”。

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="部署模板页":::

## <a name="validate-the-deployment"></a>验证部署
1. 选择顶部的“通知”，查看部署状态，并单击“正在进行部署”链接 。

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="部署模板页":::
2. 在“部署 - 概述”页上，等待部署完成。 此操作（特别是创建 VM）需要一段时间才能完成。 然后，选择“转到资源组”或“资源组名称”，如下图所示 ： 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="部署模板页":::
3. 在“资源组”页上，可以看到资源组中的资源列表。 确认在资源中可以看到类型为 `DevTest Lab` 的实验室。 还可以在资源组中看到相关资源，例如虚拟网络和虚拟机。 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="部署模板页":::
4. 从资源列表中选择你的实验室，查看实验室的主页。 确认在“我的虚拟机”列表中可以看到 Windows Server 2019 Datacenter VM。 在下图中，“Essentials”部分已最小化。 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="部署模板页":::

    > [!IMPORTANT] 
    > 保持此页打开并按照下一部分中的说明清理资源，以避免在 Azure 上运行实验室和 VM 产生成本。 如果想要学习下一个教程来测试在实验室中对 VM 的访问，请在完成该教程后清理资源。 

## <a name="clean-up-resources"></a>清理资源

1. 首先，删除实验室以便可以删除资源组。 无法删除包含实验室的资源组。 若要删除实验室，请选择工具栏上的“删除”。 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="部署模板页":::
 2. 在确认页上，键入“实验室名称”，然后选择“删除” 。 
 3. 等待实验室删除完成。 选择“钟形”图标以查看删除操作的通知。 此过程需要一些时间。 确认实验室已删除，然后在痕迹导航菜单上选择“资源组”。 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="部署模板页":::
 1. 在“资源组”页上，从工具栏中选择“删除资源组” 。 在确认页上，键入“资源组名称”，然后选择“删除” 。 检查通知，确认资源组已删除。
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="部署模板页":::

## <a name="next-steps"></a>后续步骤
在本快速入门中，你创建了一个包含 VM 的实验室。 若要了解如何访问实验室，请继续学习下一教程：

> [!div class="nextstepaction"]
> [教程：访问实验室](tutorial-use-custom-lab.md)
