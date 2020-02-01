---
title: 配置内部负载均衡器（ILB）终结点
titleSuffix: Azure Application Gateway
description: 本文提供了有关如何使用专用前端 IP 地址配置应用程序网关的信息
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: c49c37ced4a5d5cc7cdde0737b889aad3b538f7f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898995"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>使用内部负载均衡器（ILB）终结点配置应用程序网关

Azure 应用程序网关可使用面向 Internet 的 VIP 或不向 Internet 公开的内部终结点进行配置。 内部终结点使用前端专用 IP 地址（也称为*内部负载均衡器（ILB）终结点）* 。

使用前端专用 IP 地址配置网关对于不向 Internet 公开的内部业务线应用程序非常有用。 对于位于不向 Internet 公开的安全边界内的多层应用程序中的服务和层也很有用，但仍需要轮循负载分发、会话粘性或安全套接字层（SSL）终止。

本文将指导你完成使用 Azure 门户的前端专用 IP 地址配置应用程序网关的步骤。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 <https://portal.azure.com>Kv 的 Azure 门户

## <a name="create-an-application-gateway"></a>创建应用程序网关

Azure 需要一个虚拟网络才能在创建的资源之间通信。 可以创建新的虚拟网络，也可以使用现有的虚拟网络。 本示例将创建新的虚拟网络。 可以在创建应用程序网关的同时创建虚拟网络。 在不同的子网中创建应用程序网关实例。 在本示例中创建两个子网：一个用于应用程序网关，另一个用于后端服务器。

1. 展开 "门户" 菜单并选择 "**创建资源**"。
2. 选择“网络”，然后在“特色”列表中选择“应用程序网关”。
3. 输入 *myAppGateway* 作为应用程序网关的名称，输入 *myResourceGroupAG* 作为新资源组的名称。
4. 对于 "**区域**"，请选择 "美国**中部**"。
5. 对于 "**层**"，请选择 "**标准**"。
6. 在 "**配置虚拟网络**" 下选择 "**新建**"，然后输入虚拟网络的以下值：
   - *myVNet* - 虚拟网络的名称。
   - *10.0.0.0/16* - 虚拟网络地址空间。
   - *myAGSubnet* - 子网名称。
   - *10.0.0.0/24* - 子网地址空间。
   - *myBackendSubnet* -后端子网名称。
   - *10.0.1.0/24* -用于后端子网地址空间。

    ![创建虚拟网络](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. 选择“确定”以创建虚拟网络和子网。
7. 选择**下一步：前端**。
8. 对于 "**前端 IP 地址类型**"，选择 "**专用**"。

   默认情况下，它是动态 IP 地址分配。 将配置的子网的第一个可用地址指定为前端 IP 地址。
   > [!NOTE]
   > 分配后，以后不能更改 IP 地址类型（静态或动态）。
9. 选择**下一步：后端**。
10. 选择 "**添加后端池**"。
11. 对于 "**名称**"，请键入*appGatewayBackendPool*。
12. 对于 "**添加无目标的后端池**"，请选择 **"是"** 。 稍后将添加目标。
13. 选择 **添加** 。
14. 选择**下一步：配置**。
15. 在 "**路由规则**" 下，选择 "**添加规则**"。
16. 对于 "**规则名称**"，请键入*Rrule-01*。
17. 对于 "**侦听器名称**"，键入 "*侦听器-01*"。
18. 对于**前端 IP**，请选择 "**专用**"。
19. 接受剩余的默认值，然后选择 "**后端目标**" 选项卡。
20. 对于 "**目标类型**"，选择 "**后端池**"，然后选择 " **appGatewayBackendPool**"。
21. 对于 " **HTTP 设置**"，选择 "**新建**"。
22. 对于 " **http 设置名称**"，请键入 " *http-设置-01*"。
23. 对于**后端协议**，选择 " **HTTP**"。
24. 对于**后端端口**，请键入*80*。
25. 接受剩余的默认值，然后选择 "**添加**"。
26. 在 "**添加路由规则**" 页上，选择 "**添加**"。
27. 选择 "**下一步：标记**"。
28. 选择**下一步：查看 + 创建**。
29. 查看 "摘要" 页上的设置，然后选择 "**创建**" 以创建网络资源和应用程序网关。 创建应用程序网关可能需要几分钟时间。 请等待部署成功完成，然后再前进到下一部分。

## <a name="add-backend-pool"></a>添加后端池

后端池用于将请求路由到为请求提供服务的后端服务器。 后端可以由 Nic、虚拟机规模集、公共 IP 地址、内部 IP 地址、完全限定的域名（FQDN）和多租户后端（如 Azure App Service）组成。 本示例将使用虚拟机作为目标后端。 可以使用现有的虚拟机，或创建新的虚拟机。 在此示例中，你创建两台虚拟机，供 Azure 用作应用程序网关的后端服务器。

为此，请执行以下操作：

1. 创建两个用作后端服务器的新虚拟机（ *myVM*和*myVM2*）。
2. 可以在虚拟机上安装 IIS，以验证是否已成功创建了应用程序网关。
3. 将后端服务器添加到后端池。

### <a name="create-a-virtual-machine"></a>创建虚拟机

1. 选择“创建资源”。
2. 选择 "**计算**"，然后选择 "**虚拟机**"。
4. 输入虚拟机的以下值：
   - 为 "**资源组**" 选择*myResourceGroupAG* 。
   - *myVM* -**虚拟机名称**。
   - 选择用于**映像**的**Windows Server 2019 Datacenter** 。
   - *azureadmin* -**用户名**。
   - *Azure123456!* - 用于**密码**。
5. 接受剩余的默认值并选择 "**下一步：磁盘**"。
6. 接受默认值并选择 "**下一步：网络**"。
7. 请确保选择 **myVNet** 作为虚拟网络，子网是 **myBackendSubnet**。
8. 接受剩余的默认值，然后选择 "**下一步：管理**"。
9. 选择“关闭”以禁用启动诊断。
10. 接受剩余的默认值，然后选择 "**下一步：高级**"。
11. 选择 "**下一步：标记**"。
12. 选择**下一步：查看 + 创建**。
13. 检查摘要页上的设置，然后选择“创建”。 创建 VM 可能需要几分钟时间。 请等待部署成功完成，然后再前进到下一部分。

### <a name="install-iis"></a>安装 IIS

1. 打开 Cloud Shell 并确保将其设置为**PowerShell**。
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

     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `

     -Location CentralUS `

   ```



3. 使用刚刚完成的步骤创建第二个虚拟机并安装 IIS。 在 myVM2 中输入其名称，为 VMName 输入 AzVMExtension。

### <a name="add-backend-servers-to-backend-pool"></a>将后端服务器添加到后端池

1. 选择“所有资源”，然后选择“myAppGateway”。
2. 选择“后端池”。 选择“appGatewayBackendPool”。
3. 在 "**目标类型**" 下，选择 "**虚拟机**"，然后在 "**目标**" 下选择与 myVM 关联的 vNIC。
4. 重复此步骤以添加 MyVM2。
   ![frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. 选择 "**保存"。**

## <a name="test-the-application-gateway"></a>测试应用程序网关

1. 通过单击门户中的 "**前端 Ip 配置**" 页检查分配的前端 ip。
    ![frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. 复制专用 IP 地址，然后将其粘贴到位于同一 VNet 中的 VM 或本地连接到此 VNet 的 VM 的浏览器地址栏中，并尝试访问应用程序网关。

## <a name="next-steps"></a>后续步骤

如果要监视后端的运行状况，请参阅[应用程序网关的后端运行状况和诊断日志](application-gateway-diagnostics.md)。
