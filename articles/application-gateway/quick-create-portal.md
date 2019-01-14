---
title: 快速入门 - 使用 Azure 应用程序网关定向 Web 流量 - Azure 门户 | Microsoft Docs
description: 了解如何使用 Azure 门户创建 Azure 应用程序网关，用以将 Web 流量重定向到后端池中的虚拟机。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 16e23f77509d2402f765981b39a30e08a2309f68
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156521"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>快速入门：使用 Azure 应用程序网关定向 Web 流量 - Azure 门户

本快速入门展示了如何使用 Azure 门户快速创建后端池中有两台虚拟机的应用程序网关。 然后，对它进行测试以确保它正常工作。 使用 Azure 应用程序网关，可以为端口分配侦听器、创建规则以及向后端池添加资源，以便将应用程序 Web 流量定向到特定资源。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-an-application-gateway"></a>创建应用程序网关

Azure 需要一个虚拟网络才能在创建的资源之间通信。 在本示例中创建两个子网：一个用于应用程序网关，另一个用于后端服务器。 可以在创建应用程序网关的同时创建虚拟网络。

1. 选择 Azure 门户左侧菜单上的“创建资源”。 此时会显示“新建”窗口。

2. 选择“网络”，然后在“特色”列表中选择“应用程序网关”。

### <a name="basics-page"></a>“基本信息”页面

1. 在“基本信息”页上，输入这些值作为以下应用程序网关设置：

    - **名称**：输入 *myAppGateway* 作为应用程序网关的名称。
    - **资源组**：选择 **myResourceGroupAG** 作为资源组。 如果该资源组不存在，请选择“新建”，创建一个新的。

    ![新建应用程序网关](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. 接受其他设置的默认值，然后选择“确定”。

### <a name="settings-page"></a>“设置”页

1. 在“设置”页的“子网配置”下，选择“选择虚拟网络”。

2. 在“选择虚拟网络”页上，选择“新建”，然后为以下虚拟网络设置输入相应值：

    - **名称**：输入 *myVNet* 作为虚拟网络的名称。

    - **地址空间**：输入 *10.0.0.0/16* 作为虚拟网络地址空间。

    - **子网名称**：输入 *myAGSubnet* 作为子网名称。<br>应用程序网关子网只能包含应用程序网关。 不允许其他资源。

    - **子网地址范围**：输入 *10.0.0.0/24* 作为子网地址范围。

    ![创建虚拟网络](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

3. 选择“确定”，返回到“设置”页。

4. 在“前端 IP 配置”下，验证“IP 地址类型”是否设置为“公共”。 在“公共 IP 地址”下，验证是否已选择“新建”。 

5. 输入 *myAGPublicIPAddress* 作为公共 IP 地址名称。 

6. 接受其他设置的默认值，然后选择“确定”。

### <a name="summary-page"></a>“摘要”页

复查摘要页上的设置，然后选择“确定”以创建虚拟网络、公共 IP 地址和应用程序网关。 Azure 可能需要数分钟时间来创建应用程序网关。 请等待部署成功完成，然后再前进到下一部分。

## <a name="add-a-subnet"></a>添加子网

按以下步骤将子网添加到已创建的虚拟网络：

1. 在 Azure 门户的左侧菜单上选择“所有资源”，在搜索框中输入 *myVNet*，然后从搜索结果中选择 **myVNet**。

2. 从左侧菜单选择“子网”，然后选择“+ 子网”。 

    ![创建子网](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. 在“添加子网”页中输入 *myBackendSubnet* 作为子网的**名称**，然后选择“确定”。

## <a name="create-backend-servers"></a>创建后端服务器

在此示例中，你创建两台虚拟机，供 Azure 用作应用程序网关的后端服务器。 还可以在虚拟机上安装 IIS，验证 Azure 是否已成功创建应用程序网关。

### <a name="create-a-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户中，选择“创建资源”。 此时会显示“新建”窗口。

2. 选择“计算”，然后在“特色”列表中选择“Windows Server 2016 Datacenter”。 此时会显示“创建虚拟机”页。

3. 对于以下虚拟机设置，请在“基本信息”选项卡中输入相应值：

    - **资源组**：选择 **myResourceGroupAG** 作为资源组名称。
    - **虚拟机名称**：输入 *myVM* 作为虚拟机的名称。
    - **用户名**：输入 *azureuser* 作为管理员用户名。
    - **密码**：输入 *Azure123456!* 作为管理员密码。

4. 接受其他默认值，然后选择“下一步:磁盘”。  

5. 接受“磁盘”选项卡的默认值，然后选择“下一步:网络”。

6. 在“网络”选项卡上，验证是否已选择 **myVNet** 作为**虚拟网络**，以及是否已将“子网”设置为 **myBackendSubnet**。 接受其他默认值，然后选择“下一步:**管理”。

8. 在“管理”选项卡上，将“启动诊断”设置为“关闭”。 接受其他默认值，然后选择“复查 + 创建”。

9. 在“复查 + 创建”选项卡上复查设置，更正任何验证错误，然后选择“创建”。

10. 等待虚拟机创建完成，然后再继续操作。

### <a name="install-iis"></a>安装 IIS

1. 打开 [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell)。 为此，请在 Azure 门户的顶部导航栏中选择“Cloud Shell”，然后从下拉列表中选择“PowerShell”。 

    ![安装自定义扩展](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. 运行以下命令以在虚拟机上安装 IIS： 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. 使用以前完成的步骤创建第二个虚拟机并安装 IIS。 使用 *myVM2* 作为虚拟机名称，以及作为 **Set-AzureRmVMExtension** cmdlet 的 **VMName** 设置。

### <a name="add-backend-servers"></a>添加后端服务器

1. 选择“所有资源”，然后选择“myAppGateway”。

2. 从左侧菜单中选择“后端池”。 当你创建应用程序网关时，Azure 自动创建了默认池 **appGatewayBackendPool**。 

3. 选择“appGatewayBackendPool”。

4. 在“目标”下，从下拉列表中选择“虚拟机”。

5. 在“虚拟机”和“网络接口”下，从下拉列表中选择 **myVM** 和 **myVM2** 虚拟机及其关联的网络接口。

    ![添加后端服务器](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. 选择“保存”。

## <a name="test-the-application-gateway"></a>测试应用程序网关

1. 在“概览”页上找到应用程序网关的公共 IP 地址。

    ![记下应用程序网关的公共 IP 地址](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

    也可选择“所有资源”，在搜索框中输入 *myAGPublicIPAddress*，然后在搜索结果中将其选中。 Azure 会在“概览”页上显示公共 IP 地址。

2. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。

    ![测试应用程序网关](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要通过应用程序网关创建的资源，请删除资源组。 删除资源组时，也会删除应用程序网关和及其所有的相关资源。 

若要删除资源组，请执行以下操作：
1. 在 Azure 门户的左侧菜单上选择“资源组”。
2. 在“资源组”页的列表中搜索“myResourceGroupAG”，然后将其选中。
3. 在“资源组”页上，选择“删除资源组”。
4. 在“键入资源组名称”字段中输入“myResourceGroupAG”，然后选择“删除”

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过 Azure CLI 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-cli.md)
