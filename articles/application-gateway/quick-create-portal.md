---
title: 快速入门 - 使用 Azure 应用程序网关定向 Web 流量 - Azure 门户 | Microsoft Docs
description: 了解如何使用 Azure 门户创建 Azure 应用程序网关，用以将 Web 流量重定向到后端池中的虚拟机。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/15/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7e11affece7e7eb133aa22e159ec07d4f15e96f7
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999602"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>快速入门：使用 Azure 应用程序网关定向 Web 流量 - Azure 门户

使用 Azure 应用程序网关，可以通过为端口分配侦听器、创建规则以及向后端池添加资源，来将应用程序 Web 流量定向到特定资源。

本快速入门展示了如何使用 Azure 门户快速创建后端池中有两台虚拟机的应用程序网关。 然后，对它进行测试以确保它正常工作。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户

## <a name="create-an-application-gateway"></a>创建应用程序网关

若要在创建的资源之间实现通信，需要设置虚拟网络。 在本示例中创建了两个子网：一个用于应用程序网关，另一个用于后端服务器。 可以在创建应用程序网关的同时创建虚拟网络。

1. 单击 Azure 门户左上角的“创建资源”。
2. 单击“网络”，然后在“特色”列表中单击“应用程序网关”。

### <a name="basics"></a>基础

1. 输入应用程序网关的以下值：

    - *myAppGateway* - 应用程序网关的名称。
    - *myResourceGroupAG* - 新资源组。

    ![新建应用程序网关](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. 接受其他设置的默认值，然后单击“确定”。

### <a name="settings"></a>设置

1. 依次单击“选择虚拟网络”、“新建”，然后输入虚拟网络的以下值：

    - *myVNet* - 虚拟网络的名称。
    - *10.0.0.0/16* - 虚拟网络地址空间。
    - *myAGSubnet* - 子网名称。
    - *10.0.0.0/24* - 子网地址范围。

    ![创建虚拟网络](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. 单击“确定”以返回到“设置”页面。
7. 在“前端 IP 配置”下，确保“IP 地址类型”设置为“公用”，在“公用 IP 地址”下，确保选中“新建”。 键入 *myAGPublicIPAddress* 作为公用 IP 地址名称。 接受其他设置的默认值，然后单击“确定”。

### <a name="summary"></a>摘要

复查摘要页上的设置，然后单击“确定”以创建虚拟网络、公共 IP 地址和应用程序网关。 创建应用程序网关可能需要几分钟时间。 请等待部署成功完成，然后再前进到下一部分。

## <a name="add-a-subnet"></a>添加子网

1. 单击左侧菜单中的“所有资源”，然后从资源列表中单击“myVNet”。
2. 单击“子网”，然后单击“+ 子网”。

    ![创建子网](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. 输入 *myBackendSubnet* 作为子网的名称，然后单击“确定”。

## <a name="create-backend-servers"></a>创建后端服务器

在此示例中，你创建两台虚拟机以用作应用程序网关的后端服务器。 还可以在虚拟机上安装 IIS，以验证是否已成功创建应用程序网关。

### <a name="create-a-virtual-machine"></a>创建虚拟机

1. 在 Azure 门户中，单击“创建资源”。
2. 单击“计算”，然后在“特色”列表中选择“Windows Server 2016 Datacenter”。
3. 输入虚拟机的以下值：

    - *myResourceGroupAG* - 资源组。
    - *myVM* - 作为虚拟机的名称。
    - *azureuser* - 作为管理员用户名。
    - *Azure123456!* - 密码。

   接受其他默认值，并单击“下一步:磁盘”。
4. 接受磁盘默认值，并单击“下一步:网络”。
5. 请确保选择 **myVNet** 作为虚拟网络，子网是 **myBackendSubnet**。
6. 接受其他默认值，并单击“下一步:管理”。
7. 单击“关闭”以禁用启动诊断。 接受其他默认值，并单击“复查 + 创建”。
8. 在“摘要”页上复查设置，然后单击“创建”。
9. 等待虚拟机创建完成，然后再继续操作。

### <a name="install-iis"></a>安装 IIS

1. 打开交互式 shell 并确保它设置为 **PowerShell**。

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

3. 使用刚刚完成的步骤创建第二个虚拟机并安装 IIS。 输入 *myVM2* 作为其名称，并将其用于 Set-AzureRmVMExtension 中的 VMName。

### <a name="add-backend-servers"></a>添加后端服务器

1. 单击“所有资源”，然后单击 **myAppGateway**。
4. 单击“后端池”。 默认池已随应用程序网关自动创建。 单击 **appGatewayBackendPool**。
5. 在“目标”下，单击“IP 地址或 FQDN”，选择“虚拟机”。
6. 在“虚拟机”下，添加 myVM 和 myVM2 虚拟机及其关联的网络接口。

    ![添加后端服务器](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. 单击“ **保存**”。

## <a name="test-the-application-gateway"></a>测试应用程序网关

1. 在“概述”屏幕上找到应用程序网关的公共 IP 地址。 单击“所有资源”，然后单击 **myAGPublicIPAddress**。

    ![记录应用程序网关的公共 IP 地址](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。

    ![测试应用程序网关](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、应用程序网关以及所有相关资源，可将其删除。 为此，请选择包含应用程序网关的资源组，并单击“删除”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过 Azure CLI 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-cli.md)
