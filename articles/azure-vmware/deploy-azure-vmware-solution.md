---
title: 部署和配置 Azure VMware 解决方案
description: 了解如何使用规划阶段中收集的信息部署 Azure VMware 解决方案私有云。
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 08d2d48820505dad9bba74fe3ac84f45525f4525
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578652"
---
# <a name="deploy-and-configure-azure-vmware-solution"></a>部署和配置 Azure VMware 解决方案

本文介绍如何使用[规划部分](production-ready-deployment-steps.md)的信息部署 Azure VMware 解决方案。 如果你尚未定义此信息，请先返回到[规划部分](production-ready-deployment-steps.md)定义此信息，然后再继续。

## <a name="register-the-resource-provider"></a>注册资源提供程序

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="deploy-azure-vmware-solution"></a>部署 Azure VMware 解决方案

使用[规划 Azure VMware 解决方案部署](production-ready-deployment-steps.md)一文中收集的信息：

>[!NOTE]
>若要部署 Azure VMware 解决方案，你在订阅中必须至少是参与者级别。

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]



## <a name="create-the-jump-box"></a>创建跳转盒

>[!IMPORTANT]
>如果你在“创建私有云”屏幕上的初始预配步骤中将“虚拟网络”选项留空，则请在继续执行此部分**之前**完成[为 VMware 私有云配置网络](tutorial-configure-networking.md)教程。   



部署 Azure VMware 解决方案后，你将创建虚拟网络的跳转盒，用于连接到 vCenter 和 NSX。 配置 ExpressRoute 线路和 ExpressRoute Global Reach 后，不需要跳转盒。  但在 Azure VMware 解决方案中访问 vCenter 和 NSX 非常方便。  


:::image type="content" source="media/pre-deployment/jump-box-diagram.png" alt-text="创建 Azure VMware 解决方案跳转盒":::

若要在你[在部署过程中标识或创建的](production-ready-deployment-steps.md#azure-virtual-network-to-attach-azure-vmware-solution)虚拟网络中创建虚拟机 (VM)，请按以下说明操作： 

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-a-virtual-network-with-expressroute"></a>通过 ExpressRoute 连接到虚拟网络

如果你未在部署步骤中定义虚拟网络，并且你的目的是将 Azure VMware 解决方案的 ExpressRoute 连接到现有 ExpressRoute 网关，请执行以下步骤。

如果已在 Azure 中的部署屏幕上定义了一个虚拟网络，请跳到下一部分。

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]

## <a name="verify-network-routes-advertised"></a>验证播发的网络路由

跳转盒位于 Azure VMware 解决方案通过其 ExpressRoute 线路进行连接的虚拟网络中。  在 Azure 中，转到跳转盒的网络接口，[查看有效路由](../virtual-network/manage-route-table.md#view-effective-routes)。

在有效路由列表中，你应当会看到在 Azure VMware 解决方案部署过程中创建的网络。 你会看到从你在本文前面的[部署步骤](#deploy-azure-vmware-solution)中定义的 [`/22` 网络](production-ready-deployment-steps.md#ip-address-segment)派生的多个网络。

:::image type="content" source="media/pre-deployment/azure-vmware-solution-effective-routes.png" alt-text="创建 Azure VMware 解决方案跳转盒":::

在此示例中，在部署过程中输入的 10.74.72.0/22 网络派生了 /24 网络。  如果你看到类似的内容，则可以在 Azure VMware 解决方案中连接到 vCenter。

## <a name="connect-and-sign-in-to-vcenter-and-nsx-t"></a>连接并登录到 vCenter 和 NSX-T

登录到你在前面的步骤中创建的跳转盒。 登录后，打开 Web 浏览器，导航到 vCenter 和 NSX-T 管理控制台并登录到其中。  

你可以在 Azure 门户中标识 vCenter 以及 NSX-T 管理控制台的 IP 地址和凭据。  选择你的私有云，然后在“概览”视图中选择“标识”>“默认” 。 

## <a name="create-a-network-segment-on-azure-vmware-solution"></a>在 Azure VMware 解决方案中创建一个网段

你使用 NSX-T 在 Azure VMware 解决方案环境中创建新的网段。  你已在[规划部分](production-ready-deployment-steps.md)定义了要创建的网络。  如果你尚未定义它们，请先返回到[规划部分](production-ready-deployment-steps.md)定义它们，然后再继续。

>[!IMPORTANT]
>请确保你定义的 CIDR 网络地址块不与 Azure 或本地环境中的任何地址块重叠。  

按照[在 Azure VMware 解决方案中创建 NSX-T 网段](tutorial-nsx-t-network-segment.md)教程，在 Azure VMware 解决方案中创建 NSX-T 网段。

## <a name="verify-advertised-nsx-t-segment"></a>验证播发的 NSX-T 网段

返回到[验证播发的网络路由](#verify-network-routes-advertised)步骤。 你会在列表中看到其他路由，它们代表你在上一步中创建的网段。  

对于虚拟机，需分配在[验证播发的 NSX-T 网段](#verify-advertised-nsx-t-segment)步骤中创建的网段。  

由于 DNS 是必需的，因此请标识要使用的 DNS 服务器。  

- 如果已配置了 ExpressRoute Global Reach，请使用要在本地使用的任何 DNS 服务器。  
- 如果你在 Azure 中有 DNS 服务器，请使用该服务器。  
- 如果没有上述任一服务器，则使用你的跳转盒所使用的任何 DNS 服务器。

>[!NOTE]
>此步骤用于标识 DNS 服务器，不执行任何配置。

## <a name="optional-provide-dhcp-services-to-nsx-t-network-segment"></a>（可选）为 NSX-T 网段提供 DHCP 服务

如果你计划在 NSX-T 网段上使用 DHCP，请继续阅读本部分。 否则，请跳到[在 NSX-T 网段上添加 VM](#add-a-vm-on-the-nsx-t-network-segment) 部分。  

现在，你已创建了你的 NSX-T 网段，接下来可以执行以下任一操作：

* 使用 NSX-T 作为创建的段的 DHCP 服务器。 对于此选项，你需要[在 NSX-T 中创建 DHCP 服务器](manage-dhcp.md#create-dhcp-server)并[中继到该服务器](manage-dhcp.md#create-dhcp-relay-service)。
* 将来自 NSX-T 段的 DHCP 请求中继到环境中其他位置的 DHCP 服务器。 对于此选项，[仅执行中继配置](manage-dhcp.md#create-dhcp-relay-service)。


## <a name="add-a-vm-on-the-nsx-t-network-segment"></a>在 NSX-T 网段上添加 VM

在你的 Azure VMware 解决方案 vCenter 中，部署一个 VM，并使用它来验证从 Azure VMware 解决方案网络到以下目标的连接性：

- Internet
- Azure 虚拟网络
- 本地。  

像在任何 vSphere 环境中一样部署 VM。  将 VM 附加到你之前在 NSX-T 中创建的网段之一。  

>[!NOTE]
>如果设置了 DHCP 服务器，则可以从该服务器获取 VM 的网络配置（不要忘记设置作用域）。  如果要以静态方式进行配置，请如往常那样进行配置。

## <a name="test-the-nsx-t-segment-connectivity"></a>测试 NSX-T 网段连接性

登录到在上一步创建的 VM 并验证连接性；

1. 对 Internet 上的某个 IP 执行 ping 命令。
2. 通过 Web 浏览器访问某个 Internet 站点。
3. 对位于 Azure 虚拟网络上的跳转盒执行 ping 命令。

>[!IMPORTANT]
>此时，Azure VMware 解决方案已启动并运行，并且你已成功建立与 Azure 虚拟网络和 Internet 之间的连接。

## <a name="next-steps"></a>后续步骤

在下一部分，需通过 ExpressRoute 将 Azure VMware 解决方案连接到本地网络。
> [!div class="nextstepaction"]
> [将 Azure VMware 解决方案连接到本地环境](azure-vmware-solution-on-premises.md)
