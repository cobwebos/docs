---
title: 教程 - 使用基于 URL 路径的路由规则创建应用程序网关 - Azure 门户
description: 在本教程中，你将了解如何使用 Azure 门户为应用程序网关和虚拟机规模集创建基于 URL 路径的路由规则。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/18/2019
ms.author: victorh
ms.openlocfilehash: 5307f7674635fd33241e1faba9bb0b7c0432d10b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001025"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>教程：通过 Azure 门户使用基于路径的路由规则创建应用程序网关

创建[应用程序网关](overview.md)时，可以使用 Azure 门户配置[基于 URL 路径的路由规则](url-route-overview.md)。 本教程中使用虚拟机创建后端池。 然后创建路由规则，以确保 Web 流量到达池中的相应服务器。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建应用程序网关
> * 为后端服务器创建虚拟机
> * 使用后端服务器创建后端池
> * 创建后端侦听器
> * 创建基于路径的路由规则

![URL 路由示例](./media/create-url-route-portal/scenario.png)

如果你愿意，可以使用 [Azure CLI](tutorial-url-route-cli.md) 或 [Azure PowerShell](tutorial-url-route-powershell.md) 完成本教程中的步骤。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-an-application-gateway"></a>创建应用程序网关

若要在你创建的资源之间进行通信，需要设置虚拟网络。 在本示例中创建了两个子网：一个用于应用程序网关，另一个用于后端服务器。 可以在创建应用程序网关的同时创建虚拟网络。

1. 选择 Azure 门户左上角的“新建”。
2. 选择“网络”，然后在“特色”列表中选择“应用程序网关”。
3. 输入应用程序网关的以下值：

   - *myAppGateway* - 应用程序网关的名称。
   - *myResourceGroupAG* - 新资源组。

     ![新建应用程序网关](./media/create-url-route-portal/application-gateway-create.png)

4. 接受其他设置的默认值，然后选择“确定”。
5. 依次选择“选择虚拟网络”、“新建”，然后为虚拟网络输入以下值：

   - *myVNet* - 虚拟网络的名称。
   - *10.0.0.0/16* - 虚拟网络地址空间。
   - *myAGSubnet* - 子网名称。
   - *10.0.0.0/24* - 子网地址空间。

     ![创建虚拟网络](./media/create-url-route-portal/application-gateway-vnet.png)

6. 选择“确定”以创建虚拟网络和子网。
7. 依次选择“选择公共 IP 地址”、“新建”，然后输入公共 IP 地址的名称。 在本示例中，公共 IP 地址名为 *myAGPublicIPAddress*。 接受其他设置的默认值，然后选择“确定”。
8. 接受侦听器配置的默认值，让 Web 应用程序防火墙保留禁用状态，然后选择“确定”。
9. 检查摘要页上的设置，然后选择“确定”创建网络资源和应用程序网关。 创建应用程序网关可能需要几分钟时间，请等到部署成功完成，然后转到下一部分。

### <a name="add-a-subnet"></a>添加子网

1. 选择左侧菜单中的“所有资源”，然后从资源列表中选择“myVNet”。
2. 依次选择“子网”、“子网”。

    ![创建子网](./media/create-url-route-portal/application-gateway-subnet.png)

3. 输入 *myBackendSubnet* 作为子网的名称，然后选择“确定”。

## <a name="create-virtual-machines"></a>创建虚拟机

本示例将创建三个虚拟机，用作应用程序网关的后端服务器。 还可以在虚拟机上安装 IIS，以验证是否已成功创建应用程序网关。

1. 选择“新建”。
2. 选择“计算”，然后在“特色”列表中选择“Windows Server 2016 Datacenter”。
3. 输入虚拟机的以下值：

    - *myVM1* - 虚拟机的名称。
    - *azureuser* - 管理员用户名。
    - *Azure123456!* - 密码。
    - 选择“使用现有资源组”，然后选择“myResourceGroupAG”。

4. 选择“确定”。
5. 选择“DS1_V2”作为虚拟机的大小，然后选择“选择”。
6. 请确保选择 **myVNet** 作为虚拟网络，子网是 **myBackendSubnet**。 
7. 选择“禁用”以禁用启动诊断。
8. 选择“确定”，检查摘要页上的设置，然后选择“创建”。

### <a name="install-iis"></a>安装 IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 打开交互式 shell 并确保将它设置为 **PowerShell**。

    ![安装自定义扩展](./media/create-url-route-portal/application-gateway-extension.png)

2. 运行以下命令以在虚拟机上安装 IIS： 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. 使用刚刚完成的步骤创建另外两个虚拟机并安装 IIS。 在 Set-AzVMExtension 中输入 *myVM2* 和 *myVM3* 作为名称，并输入 VMName 值。

## <a name="create-backend-pools-with-the-virtual-machines"></a>使用虚拟机创建后端池

1. 选择“所有资源”，然后选择“myAppGateway”。
2. 选择“后端池”。 默认池已随应用程序网关自动创建。 选择“appGatewayBackendPool”。
3. 选择“添加目标”，将 *myVM1* 添加到 appGatewayBackendPool。

    ![添加后端服务器](./media/create-url-route-portal/application-gateway-backend.png)

4. 选择“保存”。
5. 依次选择“后端池”、“添加”。
6. 输入名称 *imagesBackendPool*，并使用“添加目标”添加 *myVM2*。
7. 选择“确定”。
8. 再次选择“添加”以添加名为 *videoBackendPool* 的另一个后端池，并在其中添加 *myVM3*。

## <a name="create-a-backend-listener"></a>创建后端侦听器

1. 选择“侦听器”，然后选择“基本”。
2. 输入 *myBackendListener* 作为名称，输入 *myFrontendPort* 作为前端端口的名称，输入 *8080* 作为侦听器的端口。
3. 选择“确定”。

## <a name="create-a-path-based-routing-rule"></a>创建基于路径的路由规则

1. 选择“规则”，然后选择“基于路径”。
2. 输入 *rule2* 作为名称。
3. 输入 *Images* 作为第一个路径的名称。 输入 */images/*\* 作为路径。 选择后端池的 **imagesBackendPool**。
4. 输入 *Video* 作为第二个路径的名称。 输入 */video/*\* 作为路径。 选择后端池的 **videoBackendPool**。

    ![创建基于路径的规则](./media/create-url-route-portal/application-gateway-route-rule.png)

5. 选择“确定”。

## <a name="test-the-application-gateway"></a>测试应用程序网关

1. 依次选择“所有资源”、“myAGPublicIPAddress”。

    ![记下应用程序网关的公共 IP 地址](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 例如 http://40.121.222.19。

    ![在应用程序网关中测试基 URL](./media/create-url-route-portal/application-gateway-iistest.png)

3. 将 URL 更改为 http://&lt;ip-address&gt;:8080/images/test.htm（请将 &lt;ip-address&gt; 替换为你的 IP 地址），然后，应会看到如以下示例所示的内容：

    ![在应用程序网关中测试映像 URL](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. 将 URL 更改为 http://&lt;ip-address&gt;:8080/video/test.htm（请将 &lt;ip-address&gt; 替换为你的 IP 地址），然后，应会看到如以下示例所示的内容：

    ![在应用程序网关中测试视频 URL](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要通过应用程序网关创建的资源，请删除资源组。 删除资源组时，也会删除应用程序网关和及其所有的相关资源。 

若要删除资源组，请执行以下操作：

1. 在 Azure 门户的左侧菜单上选择“资源组”。
2. 在“资源组”页的列表中搜索“myResourceGroupAG”，然后将其选中。
3. 在“资源组”页上，选择“删除资源组”。
4. 在“键入资源组名称”字段中输入“myResourceGroupAG”，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解有关如何使用 Azure 应用程序网关的详细信息](application-gateway-introduction.md)
