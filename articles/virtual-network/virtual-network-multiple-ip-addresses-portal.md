---
title: "虚拟机的多个 IP 地址 - 门户 | Microsoft 文档"
description: "了解如何使用 Azure 门户将多个 IP 地址分配到虚拟机。"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 716046d5e23de015da42f25f8a1f674de228efac
ms.openlocfilehash: a04192ffde57ae38d901a78a74724a89f43caedb


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>将多个 IP 地址分配到虚拟机
> [!div class="op_single_selector"]
> * [Azure 门户](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)


在一个 Azure 虚拟机 (VM) 上可以附加一个或多个网络接口 (NIC)。 可为任何 NIC 分配一个或多个公共或专用 IP 地址。 如果你不熟悉 Azure 中的 IP 地址，请参阅 [Azure 中的 IP 地址](virtual-network-ip-addresses-overview-arm.md)一文了解详细信息。 本文说明如何使用 Azure 门户在 Azure Resource Manager 部署模型中为 VM 分配多个 IP 地址。

为 VM 分配多个 IP 地址可实现以下功能：

* 在单个服务器上使用不同的 IP 地址和 SSL 证书托管多个网站或服务。
* 用作网络虚拟设备，例如防火墙或负载均衡器。
* 可将任何 NIC 的任何专用 IP 地址添加到 Azure Load Balancer 后端池。 以往，只能将主要 NIC 的主要 IP 地址添加到后端池。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

若要注册预览版，请向[多个 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) 发送一封电子邮件，其中包含你的订阅 ID 和目标用途。

## <a name="scenario"></a>方案
本文将三种 IP 配置关联到一个网络接口。
将创建以下示例配置并将其分配到具有三个专用 IP 地址和一个公共 IP 地址的 NIC。

* IPConfig-1：一个动态专用 IP 地址（默认值）以及一个来自名为 PIP1 的公共 IP 地址资源的公共 IP 地址。
* IPConfig-2：一个静态专用 IP 地址，没有公共 IP 地址。
* IPConfig-3：一个动态专用 IP 地址，没有公共 IP 地址。

    ![Alt 图像文本](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

本方案假设有一个名为 *RG1* 的资源组，其中有一个名为 *VNet1* 的 VNet 和一个名为 *Subnet1* 的子网。 此外，假设有一个名为 *VM1* 的 VM、与该 VM 关联的名为 *VM1-NIC1* 的网络接口，以及一个名为 *PIP1* 的公共 IP 地址。

[本文](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)逐步讲解如何创建上述资源（如果以前尚未创建这些资源）。

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>创建具有多个 IP 地址的 VM
若要通过 Azure 预览门户基于上述方案创建多个 IP 配置，请执行以下步骤。

1. 从浏览器导航到 http://portal.azure.com，并在必要时用 Azure 帐户登录。
2. 选择要将 IP 配置添加到的网络接口：“虚拟机” > “VM1” > “网络接口” > “VM1-NIC1”
3. 在“网络接口”边栏选项卡中，选择“IP 配置”。 此时将显示现有 IP 配置的列表。
4. 若要将 **PIP1** 与 **ipconfig1** 相关联，请在“IP 配置”边栏选项卡中选择“ipconfig1”。 在“ipconfig1”边栏选项卡中的“公共 IP 地址”下面，选择“已启用”。
5. 在“IP 地址”选项卡中，选择“配置所需设置”，选择“PIP1”或者要与此主 IP 配置关联的公共 IP 地址，然后单击“保存”。 也可以在此处创建要关联的新公共 IP 地址。 然后，将会显示“正在保存网络接口”通知，完成保存后，将显示与 **ipconfig1** 关联的 **PIP1**。

    ![Alt 图像文本](media\\virtual-network-multiple-ip-addresses-portal\\01-portal.PNG)

    >[!NOTE] 
    > 公共 IP 地址会产生少许费用。 有关 IP 地址定价的详细信息，请阅读 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。

1. 接下来，若要添加 IP 配置，请在网络接口的“IP 配置”部分下面单击“+添加”。

   > [!NOTE]
   > 最多可为一个 NIC 分配 250 个专用 IP 地址。 可在一个订阅中使用的公共 IP 地址数有限制。 有关详细信息，请阅读 [Azure limits](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)（Azure 限制）一文。
   >
   >
2. 在“添加 IP 配置”边栏选项卡中为 IP 配置命名。 在本例中，该名称为 **IPConfig-2**。 在“分配”中选择“静态”，并输入所需的 IP 地址。 在本方案中，该 IP 地址为 10.0.0.5。 。 保存配置后，将在列表中看到该 IP 配置。

    ![Alt 图像文本](media\\virtual-network-multiple-ip-addresses-portal\\02-portal.PNG)
3. 接下来，请添加第三个 IP 配置：在“IP 配置”边栏选项卡中选择“+添加”，并填写所需的详细信息，如下所示。 然后选择“确定”。

    ![Alt 图像文本](media\\virtual-network-multiple-ip-addresses-portal\\03-portal.PNG)

    请注意，也可以通过在“添加 IP 配置”边栏选项卡的“公共 IP 地址”部分中选择“已启用”，将 IPConfig-2 和 IPConfig-3 与公共 IP 相关联。 可以创建新的公共 IP，或者将已创建的公共 IP 与 IP 配置相关联。
4. 按如下所示，将所有专用 IP 地址（包括主 IP 地址）手动添加到操作系统中的 TCP/IP 配置。 若要手动添加 IP 地址，必须连接到 VM，然后遵循下面所述的步骤。

**Windows**

1. 在命令提示符下，键入 *ipconfig /all*。  只能看到*主要*专用 IP 地址（通过 DHCP）。
2. 接下来，在命令提示窗口中键入 *ncpa.cpl*。 此时将打开一个新窗口。
3. 打开“连接连接”的属性。
4. 双击“Internet 协议版本 4 (IPv4)”
5. 单击“使用下面的 IP 地址”并输入以下值：

   * **IP 地址**：输入*主要*专用 IP 地址
   * **子网掩码**：根据子网设置此值。 例如，如果子网为 /24 子网，则子网掩码为 255.255.255.0。
   * **默认网关**：子网中的第一个 IP 地址。 如果子网为 10.0.0.0/24，则网关 IP 地址为 10.0.0.1。
   * 单击“使用下面的 DNS 服务器地址”并输入以下值：
     * **首选 DNS 服务器**：如果不使用自己的 DNS 服务器，请输入 168.63.129.16。  否则请输入 DNS 服务器的 IP 地址。
   * 单击“高级”按钮，然后添加其他 IP 地址。 使用为主 IP 地址指定的相同子网，为 NIC 添加步骤 8 中列出的每个辅助专用 IP 地址。
   * 单击“确定”关闭“TCP/IP 设置”，然后再次单击“确定”关闭适配器设置。 随后将重新建立 RDP 连接。
6. 在命令提示符下，键入 *ipconfig /all*。 此时将显示添加的所有 IP 地址，DHCP 已关闭。

**Linux (Ubuntu)**

1. 打开终端窗口。
2. 请确保以 root 用户身份操作。 否则，可以使用以下命令执行此操作：

            sudo -i
3. 更新网络接口（假设为“eth0”）的配置文件。

   * 保留 dhcp 的现有行项。 这会像前面一样配置主 IP 地址。
   * 使用以下命令添加其他静态 IP 地址的配置：

               cd /etc/network/interfaces.d/
               ls

       应会看到一个 .cfg 文件。
4. 打开该文件：vi *文件名*。

    该文件的末尾应会显示以下命令行：

            auto eth0
            iface eth0 inet dhcp
5. 在此文件包含的命令行后面添加以下命令行：

            iface eth0 inet static
            address <your private IP address here>
6. 使用以下命令保存该文件：

            :wq
7. 使用以下命令重置网络接口：

           sudo ifdown eth0 && sudo ifup eth0

   > [!IMPORTANT]
   > 如果使用远程连接，请在同一行中同时运行 ifdown 和 ifup。
   >
   >
8. 使用以下命令验证 IP 地址是否已添加到网络接口：

            ip addr list eth0

    应会在列表中看到添加的 IP 地址。

**Linux（Redhat、CentOS 和其他操作系统）**

1. 打开终端窗口。
2. 请确保以 root 用户身份操作。 否则，可以使用以下命令执行此操作：

            sudo -i
3. 输入密码，根据提示的说明操作。 切换为 root 用户后，使用以下命令导航到网络脚本文件夹：

            cd /etc/sysconfig/network-scripts
4. 使用以下命令列出相关的 ifcfg 文件：

            ls ifcfg-*

    应会看到其中一个文件是 *ifcfg-eth0*。
5. 使用以下命令复制 *ifcfg-eth0* 文件并将它命名为 *ifcfg-eth0:0*：

            cp ifcfg-eth0 ifcfg-eth0:0
6. 使用以下命令编辑 *ifcfg-eth0:0* 文件：

            vi ifcfg-eth1
7. 使用以下命令在文件中将设备更改为适当的名称，在本例中为 *eth0:0*：

            DEVICE=eth0:0
8. 更改 *IPADDR = YourPrivateIPAddress* 行以反映 IP 地址。
9. 使用以下命令保存该文件：

            :wq
10. 运行以下命令重新启动网络服务，确保更改成功：

            /etc/init.d/network restart
            Ipconfig

    应会在返回的列表中看到添加的 IP 地址 *eth0:0*。

## <a name="a-nameaddaadd-ip-addresses-to-an-existing-vm"></a><a name="add"></a>将 IP 地址添加到现有 VM
完成以下步骤，将更多 IP 地址添加到现有 NIC：

1. 从浏览器导航到 http://portal.azure.com，并在必要时用 Azure 帐户登录。
2. 在“网络接口”部分中选择要将 IP 配置添加到的网络接口。
3. 在“网络接口”边栏选项卡中，选择“IP 配置”。 此时将显示现有 IP 配置的列表。
4. 接下来，若要添加 IP 配置，请在网络接口的“IP 配置”部分下面单击“+添加”。
5. 在“添加 IP 配置”边栏选项卡中为 IP 配置命名。 在“分配”下面选择所需的 IP 地址类型：“静态”或“动态”。 如果想要将 IP 配置与公共 IP 相关联，请在“公共 IP 地址”下面选择“已启用”。 否则，请单击“已禁用”。 如果选择“已启用”，则还可以将现有公共 IP 关联到配置。然后单击“确定”。 保存配置后，将在列表中看到该 IP 配置。



<!--HONumber=Nov16_HO3-->


