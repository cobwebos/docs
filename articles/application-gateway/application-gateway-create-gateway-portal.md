---
title: 创建应用程序网关 - Azure 门户 | Microsoft Docs
description: 了解如何使用 Azure 门户创建应用程序网关。
services: application-gateway
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: davidmu
ms.openlocfilehash: df9235bc7ff61943de52a0bcc4064bf9fab6636a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>使用 Azure 门户创建应用程序网关

可以使用 Azure 门户创建或管理应用程序网关。 本快速入门演示如何创建网络资源、后端服务器和应用程序网关。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure"></a>登录 Azure

通过 [http://portal.azure.com](http://portal.azure.com) 登录到 Azure 门户

## <a name="create-an-application-gateway"></a>创建应用程序网关

要使创建的资源之间实现通信需要虚拟网络。 在此示例中创建了两个子网：一个用于应用程序网关，另一个用于后端服务器。 可以在创建应用程序网关的同时创建虚拟网络。

1. 单击 Azure 门户左上角的“新建”。
2. 选择“网络”，然后在“特别推荐”列表中选择“应用程序网关”。
3. 对于应用程序网关输入以下值：

    - *myAppGateway* - 作为应用程序网关的名称。
    - *myResourceGroupAG* - 作为新资源组。

    ![新建应用程序网关](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

4. 接受其他设置的默认值，然后单击“确定”。
5. 单击“选择虚拟网络”，单击“新建”，然后为虚拟网络输入以下值：

    - *myVNet* - 作为虚拟网络的名称。
    - *10.0.0.0/16* - 作为虚拟网络地址空间。
    - *myAGSubnet* - 作为子网名称。
    - *10.0.0.0/24* - 作为子网地址空间。

    ![创建虚拟网络](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. 单击“确定”以创建虚拟网络和子网。
6. 单击“选择公共 IP 地址”，单击“新建”，然后输入公共 IP 地址的名称。 在此示例中，公共 IP 地址名为 *myAGPublicIPAddress*。 接受其他设置的默认值，然后单击“确定”。
8. 接受侦听器配置的默认值，让 Web 应用程序防火墙保留禁用状态，然后单击“确定”。
9. 复查摘要页上的设置，然后单击“确定”以创建虚拟网络、公共 IP 地址和应用程序网关。 创建应用程序网关可能需要几分钟时间，请等到部署成功完成，然后再转到下一部分。

### <a name="add-a-subnet"></a>添加子网

1. 单击左侧菜单中的“所有资源”，然后从资源列表中单击 **myVNet**。
2. 单击“子网”，然后单击“子网”。

    ![创建子网](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. 输入 *myBackendSubnet* 作为子网的名称，然后单击“确定”。

## <a name="create-backend-servers"></a>创建后端服务器

在此示例中，将创建两个虚拟机以用作应用程序网关的后端服务器。 还可以在虚拟机上安装 IIS，以验证是否已成功创建应用程序网关。

### <a name="create-a-virtual-machine"></a>创建虚拟机

1. 单击“新建” 。
2. 单击“计算”，然后在“特别推荐”列表中选择“Windows Server 2016 Datacenter”。
3. 为虚拟机输入以下值：

    - *myVM* - 作为虚拟机的名称。
    - *azureuser* - 作为管理员用户名。
    - *Azure123456!* 作为密码。
    - 选择“使用现有资源组”，然后选择“myResourceGroupAG”。

4. 单击“确定”。
5. 选择 **DS1_V2** 作为虚拟机的大小，然后单击“选择”。
6. 请确保选择 **myVNet** 作为虚拟网络，子网是 **myBackendSubnet**。 
7. 单击“禁用”以禁用启动诊断。
8. 创建“确定”，复查“摘要”页上的设置，然后单击“创建”。

### <a name="install-iis"></a>安装 IIS

1. 打开交互式 shell 并确保它已设置为 **PowerShell**。

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

3. 单击“所有资源”，然后单击 **myAppGateway**。
4. 单击“后端池”。 默认池已随应用程序网关自动创建。 单击 **appGatewayBackendPool**。
5. 单击“添加目标”将所创建的每个虚拟机添加到后端池。

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

在本快速入门中，创建了资源组、网络资源和后端服务器。 然后可以使用这些资源来创建应用程序网关。 若要了解有关应用程序网关及其关联资源的详细信息，请继续阅读操作指南文章。
