---
title: "连接到 Azure 中使用 VPN 的 Azure 堆栈"
description: "如何将 Azure 堆栈中的虚拟网络连接到使用 VPN 的 Azure 中的虚拟网络。"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: 5d963fe8b1b576768156500af39254f45939f90d
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>连接到 Azure 中使用 VPN 的 Azure 堆栈

*适用范围： Azure 堆栈集成系统*

这篇文章演示了如何创建站点到站点 VPN 连接到 Azure 中的虚拟网络的 Azure 堆栈中的虚拟网络。

### <a name="connection-diagram"></a>连接关系图
下图显示什么连接配置应如下所示完成后：

![站点到站点 VPN 连接配置](media/azure-stack-connect-vpn/image2.png)

### <a name="before-you-begin"></a>开始之前
若要完成的连接配置，确保在开始之前，有以下各项：

* Azure 堆栈集成直接连接到 Internet 的系统 （多节点） 部署。 这意味着，外部的公共 IP 地址范围必须是可以从公共 Internet 直接访问。
* 有效的 Azure 订阅。  如果你没有 Azure 订阅，则可以创建[释放此处的 Azure 帐户](https://azure.microsoft.com/free/?b=17.06)。

## <a name="network-example-values-table"></a>网络示例值表
网络示例值表显示了这篇文章中的使用的示例值。 你可以使用这些值也可以引用它们以更好地了解这篇文章中的示例。

**网络示例值表**
|   |Azure Stack|Azure|
|---------|---------|---------|
|虚拟网络名称     |Azs-VNet|AzureVNet |
|虚拟网络地址空间 |10.1.0.0/16|10.100.0.0/16|
|子网名称     |FrontEnd|FrontEnd|
|子网地址范围|10.1.0.0/24 |10.100.0.0/24 |
|网关子网     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>在 Azure 中创建的网络资源

首先，你为 Azure 创建网络资源。 下面的说明演示如何通过使用创建资源[Azure 门户](http://portal.azure.com/)。

### <a name="create-the-virtual-network-and-vm-subnet"></a>创建虚拟网络和 VM 子网

1. 登录到[Azure 门户](http://portal.azure.com/)使用你的 Azure 帐户。
2. 在用户门户中，选择**新建**。
3. 转到**Marketplace**，然后选择**网络**。
4. 选择“虚拟网络”。
5. 使用网络配置表中的信息以确定的值为 Azure**名称**，**地址空间**，**子网名称**，和**子网地址范围**。
6. 有关**资源组**，创建新的资源组或如果你已经有一个，则选择**使用现有**。
7. 选择**位置**的你的 VNet。  如果你使用的示例值，选择**美国东部**或如果你愿意使用另一个位置。
8. 选择“固定到仪表板”。
9. 选择“创建”。

### <a name="create-the-gateway-subnet"></a>创建网关子网
1. 打开刚才创建的虚拟网络资源 (**AzureVNet**) 从仪表板。
2. 上**设置**部分中，选择**子网**。
3. 选择**网关子网**将网关子网添加到虚拟网络。
4. 默认情况下，子网的名称设置为 **GatewaySubnet**。
   网关子网很特殊，必须使用该特定名称才能正常运行。
5. 在**地址范围**字段中，验证地址是否**10.100.1.0/24**。
6. 选择**确定**创建网关子网。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关
1. 在 Azure 门户中，选择**新建**。  
2. 转到**Marketplace**，然后选择**网络**。
3. 从网络资源的列表中选择**虚拟网络网关**。
4. 在**名称**，类型**Azure GW**。
5. 若要选择一个虚拟网络，选择**虚拟网络**。 然后选择**AzureVnet**从列表中。
6. 选择**公共 IP 地址**。 当**选择公共 IP 地址**部分将打开，选择**新建**。
7. 在**名称**，类型**Azure GW PiP**，然后选择**确定**。
8. 默认情况下，为**VPN 类型**，**基于路由的**选择。
    保留**基于路由的**VPN 类型。
9. 验证“订阅”和“位置”是否正确。 你可以固定到仪表板的资源。 选择“创建”。

### <a name="create-the-local-network-gateway-resource"></a>创建本地网络网关资源

1. 在 Azure 门户中，选择**新建**。 
4. 转到**Marketplace**，然后选择**网络**。
5. 从资源的列表中选择**本地网络网关**。
6. 在**名称**，类型**Azs GW**。
7. 在**IP 地址**，输入你 Azure 堆栈虚拟网络网关之前在网络配置表中列出的公共 IP 地址。
8. 在**地址空间**，从 Azure 堆栈键入**10.1.0.0/24**和**10.1.1.0/24**地址空间为**AzureVNet**。
9. 验证你**订阅**，**资源组**，和**位置**正确无误，，然后选择**创建**。

## <a name="create-the-connection"></a>创建连接
1. 在用户门户中，选择**新建**。 
2. 转到**Marketplace**，然后选择**网络**。
3. 从资源的列表中选择**连接**。
4. 上**基本**设置部分中，为**连接类型**，选择**站点到站点 (IPSec)**。
5. 选择**订阅**，**资源组**，和**位置**，然后选择**确定**。
6. 上**设置**部分中，选择**虚拟网络网关**，然后选择**Azure GW**。
7. 选择**本地网络网关**，然后选择**Azs GW**。
8. 在**连接名称**，类型**Azure Azs**。
9. 在**共享密钥 (PSK)**，类型**12345**。 如果你选择不同的值，请记住它*必须*匹配你在连接的另一端创建的共享密钥的值。 选择“确定”。
10. 查看**摘要**部分，，然后选择**确定**。

## <a name="create-a-virtual-machine"></a>创建虚拟机
现在，在 Azure 中创建虚拟机并将其放在虚拟网络中你的 VM 子网。

1. 在 Azure 门户中，选择**新建**。
2. 转到**Marketplace**，然后选择**计算**。
3. 在虚拟机映像的列表中，选择**Windows Server 2016 数据中心 Eval**映像。
4. 上**基础知识**部分中，为**名称**，类型**AzureVM**。
5. 键入有效的用户名和密码。 此帐户用于登录到虚拟机将在创建后。
6. 提供**订阅**，**资源组**，和**位置**，然后选择**确定**。
7. 上**大小**部分，选择此情况下，虚拟机大小，然后选择**选择**。
8. 上**设置**部分中，你可以接受默认值。 请确保**AzureVnet**虚拟网络已选中，然后验证子网是否设置为**10.100.0.0/24**。 选择“确定”。
9. 上查看设置**摘要**部分，，然后选择**确定**。

## <a name="create-the-network-resources-in-azure-stack"></a>在 Azure 堆栈中创建的网络资源
接下来，你在 Azure 堆栈中创建的网络资源。

### <a name="sign-in-as-a-user"></a>以用户身份登录
服务管理员可以登录的用户以测试计划、 服务和其用户可能使用的订阅。 如果你还没有一个，[创建用户帐户](azure-stack-add-new-user-aad.md)在登录之前。

### <a name="create-the-virtual-network-and-vm-subnet"></a>创建虚拟网络和 VM 子网
1. 使用用户帐户登录到用户门户。
2. 在用户门户中，选择**新建**。

    ![创建新的虚拟网络](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. 转到**Marketplace**，然后选择**网络**。
4. 选择“虚拟网络”。
5. 有关**名称**，**地址空间**，**子网名称**，和**子网地址范围**，使用网络配置表中的值。
6. 在**订阅**，先前创建的订阅显示。
7. 有关**资源组**，你可以创建资源组，也可以已经拥有一个帐户，如果选择**使用现有**。
8. 验证默认位置。
9. 选择“固定到仪表板”。
10. 选择“创建”。

### <a name="create-the-gateway-subnet"></a>创建网关子网
1. 在仪表板中，打开您创建的 Azs VNet 虚拟网络资源。
2. 上**设置**部分中，选择**子网**。
3. 若要将网关子网添加到虚拟网络中，选择**网关子网**。
   
    ![添加网关子网](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. 默认情况下，子网名称设置为**GatewaySubnet**。
   网关的子网是特殊的。 若要正常运行，它们必须使用*GatewaySubnet*名称。
5. 在**地址范围**，验证地址是否**10.1.1.0/24**。
6. 选择**确定**创建网关子网。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关
1. 在 Azure 堆栈门户中，选择**新建**。 
2. 转到**Marketplace**，然后选择**网络**。
3. 从网络资源的列表中选择**虚拟网络网关**。
4. 在**名称**，类型**Azs GW**。
5. 选择**虚拟网络**项选择虚拟网络。
   选择**Azs VNet**从列表中。
6. 选择**公共 IP 地址**菜单项。 当**选择公共 IP 地址**部分将打开，选择**新建**。
7. 在**名称**，类型**Azs GW PiP**，然后选择**确定**。
8.  默认情况下，为**VPN 类型**，**基于路由的**选择。
    保留**基于路由的**VPN 类型。
9. 验证“订阅”和“位置”是否正确。 你可以固定到仪表板的资源。 选择“创建”。

### <a name="create-the-local-network-gateway"></a>创建本地网关
这一概念*本地网络网关*Azure 堆栈中是稍有不同于 Azure 部署中。

在 Azure 部署中，本地网络网关表示的本地 （在用户位置中） 物理设备，用于连接到 Azure 虚拟网络网关。 Azure 堆栈中连接两个 ends 是虚拟网络网关 ！

更广义考虑这一方法是，本地网络网关资源始终指示远程网关连接另一端。 

### <a name="create-the-local-network-gateway-resource"></a>创建本地网络网关资源
1. 登录到 Azure 堆栈门户。
2. 在用户门户中，选择**新建**。
3. 转到**Marketplace**，然后选择**网络**。
4. 从资源的列表中选择**本地网络网关**。
5. 在**名称**，类型**Azure GW**。
6. 在**IP 地址**，键入在 Azure 中的虚拟网络网关的公共 IP 地址**Azure GW PiP**。 此地址将显示在网络配置表的前面部分中。
7. 在**地址空间**，为你创建的 Azure VNET 的地址空间中，键入**10.100.0.0/24**和**10.100.1.0/24**。
8. 验证你**订阅**，**资源组**，和**位置**正确无误，，然后选择**创建**。

### <a name="create-the-connection"></a>创建连接
1. 在用户门户中，选择**新建**。
2. 转到**Marketplace**，然后选择**网络**。
3. 从资源的列表中选择**连接**。
4. 上**基础知识**设置部分中，为**连接类型**，选择**站点到站点 (IPSec)**。
5. 选择**订阅**，**资源组**，和**位置**，然后选择**确定**。
6. 上**设置**部分中，选择**虚拟网络网关**，然后选择**Azs GW**。
7. 选择**本地网络网关**，然后选择**Azure GW**。
8. 在**连接名称**，类型**Azs Azure**。
9. 在**共享密钥 (PSK)**，类型**12345**，然后选择**确定**。
10. 上**摘要**部分中，选择**确定**。

### <a name="create-a-vm"></a>创建 VM
若要验证的数据，通过 VPN 连接，你需要在每个端来发送和接收数据通过 VPN 隧道上创建虚拟机。 

1. 在 Azure 门户中，选择**新建**。
2. 转到**Marketplace**，然后选择**计算**。
3. 在虚拟机映像的列表中，选择**Windows Server 2016 数据中心 Eval**映像。
4. 上**基础知识**部分中，在**名称**，类型**Azs VM**。
5. 键入有效的用户名和密码。 此帐户用于登录到 VM，将在创建后。
6. 提供**订阅**，**资源组**，和**位置**，然后选择**确定**。
7. 上**大小**部分，此实例中，选择虚拟机大小，然后选择**选择**。
8. 上**设置**部分中，接受默认设置。 请确保**Azs VNet**选择虚拟网络。 验证子网是否设置为**10.1.0.0/24**。 然后选择“确定”。
9. 上**摘要**部分，查看设置，，然后选择**确定**。


## <a name="test-the-connection"></a>测试连接
现在，建立站点到站点连接时，你应验证就可以流量流向通过它。 若要验证，登录到你在 Azure 堆栈中创建的虚拟机之一。 然后，执行 ping 操作在 Azure 中创建的虚拟机。 

若要确保发送的流量通过站点到站点连接，ping 远程子网，不 VIP 上的虚拟机的直接 IP (DIP) 地址。 若要执行此操作，找到连接的另一端上的 DIP 地址。 保存以供将来使用的地址。

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>登录到用户 Azure 堆栈中的 VM
1. 登录到 Azure 堆栈门户。
2. 在左侧的导航栏中，选择**虚拟机**。
3. 在虚拟机列表中，查找**Azs VM** ，以前，创建，然后选择它。
4. 在为虚拟机部分中，单击**连接**，然后打开 Azs VM.rdp 文件。
   
     ![连接按钮](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. 使用在创建虚拟机时配置的帐户登录。
6. 打开提升**Windows PowerShell**窗口。
7. 键入 **ipconfig /all**。
8. 在输出中，查找**IPv4 地址**，然后将保存以供将来使用的地址。 这是你将执行 ping 操作从 Azure 的地址。 在示例环境中，该地址是**10.1.0.4**，但你的环境中可能会不同。 它不应超过**10.1.0.0/24**你之前创建的子网。
9. 若要创建允许要在响应 ping 的虚拟机的防火墙规则，运行以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>登录到租户在 Azure 中 VM
1. 登录到 Azure 门户。
2. 在左侧的导航栏中，单击**虚拟机**。
3. 从虚拟机列表中，找到**Azure VM** ，以前，创建，然后选择它。
4. 在为虚拟机部分中，单击**连接**。
5. 使用在创建虚拟机时配置的帐户登录。
6. 打开提升**Windows PowerShell**窗口。
7. 键入 **ipconfig /all**。
8. 你应看到范围内的 IPv4 地址**10.100.0.0/24**。 在示例环境中，该地址是**10.100.0.4**，但你的地址可能会不同。
9. 若要创建允许要在响应 ping 的虚拟机的防火墙规则，运行以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. 从 Azure 中的虚拟机，进行 ping 操作中 Azure 堆栈通过隧道的虚拟机。 若要执行此操作，你 ping 记录从 Azs VM 的 DIP。
   在示例环境中，这是**10.1.0.4**，但请确保你的实验室中记下的地址执行 ping 操作。 你应看到类似于以下屏幕截图的结果：
   
    ![成功 ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. 从远程虚拟机的回复表明成功的测试 ！ 你可以关闭虚拟机窗口。 若要测试你的连接，你可以尝试其他类型的类似文件复制的数据传输。

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>通过网关连接查看数据传输统计信息
如果你想要知道多少数据通过站点到站点连接，此信息位于**连接**部分。 此测试也是另一种方法验证刚刚发送 ping 实际经历 VPN 连接。

1. 而你要登录到 Azure 堆栈中的用户虚拟机，使用你的用户帐户登录到用户门户。
2. 转到**的所有资源**，然后选择**Azs Azure**连接。 **连接**显示。
4. 上**连接**部分的统计信息**中的数据**和**输出数据量**显示。 在以下屏幕截图中，较大的数字归因于另外的文件传输。 你应看到那里一些非零值。
   
    ![数据流入和流出](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>后续步骤

[将应用部署到 Azure 和 Azure 堆栈](azure-stack-solution-pipeline.md)
