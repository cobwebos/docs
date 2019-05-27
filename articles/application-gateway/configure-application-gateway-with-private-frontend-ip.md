---
title: 使用专用前端 IP 地址配置 Azure 应用程序网关
description: 本文提供有关如何使用专用前端 IP 地址配置应用程序网关的信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/26/2019
ms.author: absha
ms.openlocfilehash: cfc63349e20aa6dbef4e0d31e81842d325bd3ec6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "66134643"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>使用内部负载均衡器 (ILB) 终结点配置应用程序网关

可向 Azure 应用程序网关配置面向 Internet 的 VIP 或不向 Internet 公开的内部终结点（也称为内部负载均衡器 (ILB) 终结点（使用前端 IP 地址的专用 IP）。 对于不向 Internet 公开的内部业务线应用程序，使用前端专用 IP 地址配置网关的做法非常有效。 对于位于不向 Internet 公开的安全边界内的多层应用程序中的服务和层也很有用，但仍需要执行循环负载分散、会话粘性或安全套接字层 (SSL) 终止。

本文逐步讲解如何在 Azure 门户中使用前端专用 IP 地址配置应用程序网关。

本文介绍如何执行以下操作：

- 为应用程序网关创建专用前端 IP 配置
- 使用专用前端 IP 配置创建应用程序网关


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>登录 Azure

在 <https://portal.azure.com> 登录 Azure 门户

## <a name="create-an-application-gateway"></a>创建应用程序网关

Azure 需要一个虚拟网络才能在创建的资源之间通信。 可以创建新的虚拟网络，也可以使用现有的虚拟网络。 本示例将创建新的虚拟网络。 可以在创建应用程序网关的同时创建虚拟网络。 在不同的子网中创建应用程序网关实例。 在本示例中创建两个子网：一个用于应用程序网关，另一个用于后端服务器。

1. 单击 Azure 门户左上角的“新建”。
2. 选择“网络”，然后在“特别推荐”列表中选择“应用程序网关”。
3. 输入 *myAppGateway* 作为应用程序网关的名称，输入 *myResourceGroupAG* 作为新资源组的名称。
4. 接受其他设置的默认值，然后单击“确定”。
5. 依次单击“选择虚拟网络”、“新建”，然后输入虚拟网络的以下值：
   - myVNet* - 虚拟网络的名称。
   - 10.0.0.0/16* - 虚拟网络地址空间。
   - *myAGSubnet* - 子网名称。
   - *10.0.0.0/24* - 子网地址空间。  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. 单击“确定”创建虚拟网络和子网。
7. 选择“专用”作为“前端 IP 配置”（默认为动态 IP 地址分配）。 所选子网的第一个可用地址将分配为前端 IP 地址。
8. 若要从子网地址范围中选择专用 IP（静态分配），请单击“选择特定的专用 IP 地址”框并指定 IP 地址。
   > [!NOTE]
   > IP 地址类型（静态或动态）一经分配，以后便不可更改。
9. 选择协议和端口的侦听器配置以及 WAF 配置（如果需要），然后单击“确定”。
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. 检查摘要页上的设置，然后单击“确定”创建网络资源和应用程序网关。 创建应用程序网关可能需要几分钟时间，请等到部署成功完成，然后转到下一部分。

## <a name="add-backend-pool"></a>添加后端池

后端池用于将请求路由到将为请求提供服务的后端服务器。 后端可以包含 NIC、虚拟机规模集、公共 IP、内部 IP、完全限定的域名 (FQDN) 和多租户后端（例如 Azure 应用服务）。 本示例使用虚拟机作为目标后端。 可以使用现有的虚拟机，或创建新的虚拟机。 本示例创建两台虚拟机，供 Azure 用作应用程序网关的后端服务器。 为此，我们将会：

1. 创建两个新的 VM *myVM* 和 *myVM2*，用作后端服务器。
2. 可以在虚拟机上安装 IIS，以验证是否已成功创建了应用程序网关。
3. 将后端服务器添加到后端池。

### <a name="create-a-virtual-machine"></a>创建虚拟机

1. 单击“新建” 。
2. 单击“计算”，然后在“特色”列表中选择“Windows Server 2016 Datacenter”。
3. 为虚拟机输入以下值：
   - *myVM* - 作为虚拟机的名称。
   - *azureuser* - 作为管理员用户名。
   - *Azure123456!* - 密码。
   - 选择“使用现有资源组”，然后选择“myResourceGroupAG”。
4. 单击“确定”。
5. 选择“DS1_V2”作为虚拟机的大小，然后单击“选择”。
6. 请确保选择 **myVNet** 作为虚拟网络，子网是 **myBackendSubnet**。
7. 单击“禁用”以禁用启动诊断。
8. 创建“确定”，检查“摘要”页上的设置，然后单击“创建”。

### <a name="install-iis"></a>安装 IIS

1. 打开交互式 shell 并确保它已设置为 **PowerShell**。
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. 运行以下命令以在虚拟机上安装 IIS：

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
