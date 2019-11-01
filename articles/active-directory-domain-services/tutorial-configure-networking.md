---
title: 教程 - 为 Azure AD 域服务配置虚拟网络 | Microsoft Docs
description: 本教程介绍如何使用 Azure 门户为 Azure Active Directory 域服务实例创建和配置 Azure 虚拟网络子网或网络对等互连。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 26122278ad74fb1d383ca7a900810b6060ee78f5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172648"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-instance"></a>教程：为 Azure Active Directory 域服务实例配置虚拟网络

为了给用户和应用程序提供连接，已在 Azure 虚拟网络子网中部署了一个 Azure Active Directory 域服务 (Azure AD DS) 托管域。 此虚拟网络子网应该仅供 Azure 平台提供的托管域资源使用。 当你创建自己的 VM 和应用程序时，不应将它们部署到同一个虚拟网络子网中。 应创建应用程序并将其部署到不同的虚拟网络子网，或部署到与 Azure AD DS 虚拟网络对等互连的其他虚拟网络中。

本教程介绍如何创建和配置专用的虚拟网络子网，或者如何将不同的网络对等互连到 Azure AD DS 托管域的虚拟网络。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 了解已加入域的资源与 Azure AD DS 之间的虚拟网络连接选项
> * 在 Azure AD DS 虚拟网络中创建 IP 地址范围和额外的子网
> * 配置与独立于 Azure AD DS 的网络之间的虚拟网络对等互连

如果你没有 Azure 订阅，可以在开始之前[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 需要在 Azure AD 目录中拥有“全局管理员”特权才能启用 Azure AD DS。 
* 需要在 Azure 订阅中拥有“参与者”特权才能创建所需的 Azure AD DS 资源。 
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请参考第一篇教程[创建并配置 Azure Active Directory 域服务实例][create-azure-ad-ds-instance]。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在本教程中，你将使用 Azure 门户来创建和配置 Azure AD DS 实例。 若要开始操作，请登录到 [Azure 门户](https://portal.azure.com)。

## <a name="application-workload-connectivity-options"></a>应用程序工作负荷连接选项

在上一篇教程中，我们已创建一个 Azure AD DS 托管域，该托管域对虚拟网络使用了一些默认的配置选项。 这些默认选项创建了一个 Azure 虚拟网络和虚拟网络子网。 提供托管域服务的 Azure AD DS 域控制器已连接到此虚拟网络子网。

创建并运行需要使用 Azure AD DS 托管域的 VM 时，需要提供网络连接。 可通过以下方式之一提供此网络连接：

* 在默认 Azure AD DS 托管域的虚拟网络中创建额外的虚拟网络子网。 将在此额外子网中创建和连接 VM。
    * 由于这些 VM 位于同一虚拟网络中，它们可以自动执行名称解析并与 Azure AD DS 域控制器通信。
* 配置从 Azure AD DS 托管域的虚拟网络到一个或多个独立虚拟网络的 Azure 虚拟网络对等互连。 将在这些独立的虚拟网络中创建和连接 VM。
    * 配置虚拟网络对等互连时，还必须配置 DNS 设置，以使用定向回到 Azure AD DS 域控制器的名称解析。

通常我们只使用其中的一个网络连接选项。 做出的选择往往取决于如何单独管理 Azure 资源。 若要将 Azure AD DS 和连接的 VM 作为一组资源进行管理，可为 VM 额外创建一个虚拟网络子网。 若要将 Azure AD DS 和任何连接的 VM 分开管理，可以使用虚拟网络对等互连。 还可以选择使用虚拟网络对等互连来与 Azure 环境中已连接到现有虚拟网络的现有 VM 建立连接。

在本教程中，你只需配置其中的一个虚拟网络连接选项。

有关如何规划和配置虚拟网络的详细信息，请参阅 [Azure Active Directory 域服务的网络注意事项][network-considerations]。

## <a name="create-a-virtual-network-subnet"></a>创建虚拟网络子网

默认情况下，使用 Azure AD DS 托管域创建的 Azure 虚拟网络包含单个虚拟网络子网。 此虚拟网络子网应该仅供 Azure 平台用来提供托管域服务。 若要在此 Azure 虚拟网络中创建并使用自己的 VM，请创建额外的子网。

若要为 VM 和应用程序工作负荷创建虚拟网络子网，请完成以下步骤：

1. 在 Azure 门户中，选择 Azure AD DS 托管域的资源组，例如 *myResourceGroup*。 在资源列表中选择默认的虚拟网络，例如 *aadds-vnet*。
1. 在虚拟网络窗口的左侧菜单中，选择“地址空间”。  随即会使用单个地址空间 *10.0.1.0/24*（由默认子网使用）创建虚拟网络。

    将额外的 IP 地址范围添加到该虚拟网络。 此地址范围的大小以及要使用的实际 IP 地址范围取决于已部署的其他网络资源。 该 IP 地址范围不应与 Azure 或本地环境中的任何现有地址范围重叠。 请确保该 IP 地址范围足够大，能够与要部署到子网中的 VM 数量相适应。

    在以下示例中，添加了额外的 IP 地址范围 *10.0.2.0/24*。 准备就绪后，选择“保存”。 

    ![在 Azure 门户中添加额外的虚拟网络 IP 地址范围](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. 接下来，在虚拟网络窗口的左侧菜单中选择“子网”，然后选择“+ 子网”以添加子网。  
1. 输入子网的名称，例如 *workloads*。 如果需要使用在前面步骤中为虚拟网络配置的 IP 地址范围子集，请更新“地址范围”。  暂时对网络安全组、路由表、服务终结点等选项保留默认值。

    在以下示例中，创建了名为 *workloads*、使用 *10.0.2.0/24* IP 地址范围的子网：

    ![在 Azure 门户中添加额外的虚拟网络子网](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. 准备就绪后，选择“确定”。  需要花费片刻时间来创建虚拟网络子网。

创建需要使用 Azure AD DS 托管域的 VM 时，请务必选择此虚拟网络子网。 请不要在默认的 *aadds-subnet* 中创建 VM。 如果选择其他虚拟网络，则除非配置虚拟网络对等互连，否则将不提供网络连接和 DNS 解析，因此无法连接到 Azure AD DS 托管域。

## <a name="configure-virtual-network-peering"></a>配置虚拟网络对等互连

你可能已经为 VM 创建了 Azure 虚拟网络，或者希望保持 Azure AD DS 托管域虚拟网络的独立性。 若要使用托管域，其他虚拟网络中的 VM 需要通过某种方式来与 Azure AD DS 域控制器通信。 可以使用 Azure 虚拟网络对等互连来提供此连接。

使用 Azure 虚拟网络对等互连时，两个虚拟网络将连接到一起，而无需使用虚拟专用网络 (VPN) 设备。 使用网络对等互连可以快速连接虚拟网络并定义整个 Azure 环境中的流量流。 有关对等互连的详细信息，请参阅 [Azure 虚拟网络对等互连概述][peering-overview]。

若要将某个虚拟网络对等互连到 Azure AD DS 托管域虚拟网络，请完成以下步骤：

1. 选择为 Azure AD DS 实例创建的名为 *aadds-vnet* 的默认虚拟网络。
1. 在虚拟网络窗口的左侧菜单中，选择“对等互连”。 
1. 若要创建对等互连，请选择“+ 添加”。  在以下示例中，默认的 *aadds-vnet* 已对等互连到名为 *myVnet* 的虚拟网络。 使用自己的值配置以下设置：

    * **从 aadds-vnet 到远程虚拟网络的对等互连的名称**：两个网络的描述性标识符，例如 *aadds-vnet-myvnet*
    * **虚拟网络部署类型**：*Resource Manager*
    * **订阅**：要对等互连到的虚拟网络的订阅，例如 *Azure*
    * **虚拟网络**：要对等互连到的虚拟网络，例如 *myVnet*
    * **从 myVnet 到 aadds-vnet 的对等互连的名称**：两个网络的描述性标识符，例如 *myvnet-to-aadds-vnet*

    ![在 Azure 门户中配置虚拟网络对等互连](./media/tutorial-configure-networking/create-peering.png)

    除非对环境有特定的要求，否则请对虚拟网络访问或转发流量等任何其他设置保留默认值；然后选择“确定”。 

1. 对 Azure AD DS 虚拟网络和所选虚拟网络创建对等互连需要花费片刻时间。 准备就绪后，“对等互连状态”中会报告“已连接”，如以下示例所示：  

    ![已成功在 Azure 门户中连接对等互连的网络](./media/tutorial-configure-networking/connected-peering.png)

在对等互连的虚拟网络中的 VM 可以使用 Azure AD DS 托管域之前，请配置 DNS 服务器，以便能够正确进行名称解析。

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>在对等互连的虚拟网络中配置 DNS 服务器

要使对等互连虚拟网络中的 VM 和应用程序能够成功地与 Azure AD DS 托管域通信，必须更新 DNS 设置。 必须将 Azure AD DS 域控制器的 IP 地址配置为对等互连虚拟网络上的 DNS 服务器。 可通过两种方式将域控制器配置为对等互连虚拟网络的 DNS 服务器：

* 将 Azure 虚拟网络 DNS 服务器配置为使用 Azure AD DS 域控制器。
* 将对等互连虚拟网络中使用的现有 DNS 服务器配置为使用条件 DNS 转发向 Azure AD DS 托管域定向查询。 这些步骤根据所用的现有 DNS 服务器而异。

在本教程中，让我们将 Azure 虚拟网络 DNS 服务器配置为向 Azure AD DS 域控制器定向所有查询。

1. 在 Azure 门户中，选择对等互连虚拟网络的资源组，例如 *myResourceGroup*。 在资源列表中选择对等互连的虚拟网络，例如 *myVnet*。
1. 在虚拟网络窗口的左侧菜单中，选择“DNS 服务器”。 
1. 默认情况下，虚拟网络使用 Azure 提供的内置 DNS 服务器。 选择使用“自定义”DNS 服务器。  输入 Azure AD DS 域控制器的 IP 地址，通常是 *10.0.1.4* 和 *10.0.1.5*。 在门户上 Azure AD DS 托管域的“概述”窗口中确认这些 IP 地址。 

    ![将虚拟网络 DNS 服务器配置为使用 Azure AD DS 域控制器](./media/tutorial-configure-networking/custom-dns.png)

1. 准备就绪后，选择“保存”。  需要花费片刻时间来更新虚拟网络的 DNS 服务器。
1. 若要将更新的 DNS 设置应用到 VM，请重启已连接到对等互连虚拟网络的 VM。

创建需要使用 Azure AD DS 托管域的 VM 时，请务必选择此对等互连的虚拟网络。 如果选择其他虚拟网络，将不提供网络连接和 DNS 解析，因此无法连接到 Azure AD DS 托管域。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 了解已加入域的资源与 Azure AD DS 之间的虚拟网络连接选项
> * 在 Azure AD DS 虚拟网络中创建 IP 地址范围和额外的子网
> * 配置与独立于 Azure AD DS 的网络之间的虚拟网络对等互连

若要了解此托管域的运作情况，请创建一个虚拟机并将其加入域。

> [!div class="nextstepaction"]
> [将 Windows Server 虚拟机加入托管域](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
