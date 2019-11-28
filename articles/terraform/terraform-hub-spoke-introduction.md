---
title: 教程 - 使用 Terraform 在 Azure 中创建中心辐射型混合网络拓扑
description: 本教程演示如何使用 Terraform 在 Azure 中创建完整的混合网络参考体系结构
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 90a60fc4fe98397c903e3e8a460806d6a2edb908
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159032"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>教程：使用 Terraform 在 Azure 中创建中心辐射型混合网络拓扑

本教程系列介绍如何使用 Terraform 在 Azure 中实现[中心辐射型网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)。 

使用中心辐射型拓扑可以隔离工作负荷，同时共享公用的服务。 这些服务包括标识和安全性。 中心是一个虚拟网络 (VNet)，充当本地网络的中心连接点。 辐射是与中心对等互连的 VNet。 共享服务部署在中心内，而各个工作负荷部署在辐射网络内。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 使用 HCL（HashiCorp 语言）排布中心辐射型混合网络参考体系结构资源
> * 使用 Terraform 创建中心网络设备资源
> * 使用 Terraform 在 Azure 创建中心网络，用于充当所有资源的公用连接点
> * 使用 Terraform 创建单个工作负荷，用作 Azure 中的辐射 VNet
> * 使用 Terraform 在本地与 Azure 网络之间建立网关和连接
> * 使用 Terraform 来与辐射网络建立 VNet 对等互连

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**：如果没有 Azure 订阅，请在开始之前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- **安装并配置 Terraform**：若要在 Azure 中预配 VM 和其他基础结构，请[安装并配置 Terraform](/azure/virtual-machines/linux/terraform-install-configure)

## <a name="hub-and-spoke-topology-architecture"></a>中心辐射型拓扑体系结构

在中心辐射型拓扑中，中心是一个 VNet。 该 VNet 充当本地网络的中心连接点。 分支是与中心对等互连的 VNet，可用于隔离工作负荷。 流量通过 ExpressRoute 或 VPN 网关连接在本地数据中心与中心之间流动。 下图演示了中心辐射型拓扑中的组件：

![Azure 中的中心辐射型拓扑体系结构](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>中心辐射型拓扑的优势

使用中心辐射型网络拓扑可以隔离工作负荷，同时共享公用的服务。 这些服务包括标识和安全性。 中心是一个 VNet，充当本地网络的中心连接点。 辐射是与中心对等互连的 VNet。 共享服务部署在中心内，而各个工作负荷部署在辐射网络内。 下面是中心辐射型网络拓扑的某些优势：

- **成本节省** - 将服务集中到可由多个工作负荷共享的单个位置。 这些工作负荷包括网络虚拟设备和 DNS 服务器。
- **克服订阅限制** - 通过将不同订阅中的 Vnet 对等互连到中心。
- **关注点隔离**（在中心 IT（SecOps、InfraOps）与工作负荷 (DevOps) 之间）。

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>中心辐射型体系结构的典型用法

中心辐射型体系结构的某些典型用法包括：

- 许多客户在不同的环境中部署了工作负荷。 这些环境包括开发、测试和生产。 这些工作负荷经常需要共享 DNS、IDS、NTP 或 AD DS 等服务。 可将这些共享的服务放在中心 VNet 中。 这样，每个环境将部署到辐射网络以保持隔离性。
- 工作负荷不需要彼此连接，但需要访问共享的服务。
- 企业需要在安全方面进行集中控制。
- 企业需要对每个辐射网络中的工作负荷进行隔离式的管理。

## <a name="preview-the-demo-components"></a>预览演示组件

在学习本教程系列中的每篇教程过程中，各个组件将在不同的 Terraform 脚本中定义。 创建并部署的演示体系结构包括以下组件：

- **本地网络**。 组织中运行的专用局域网。 对于中心辐射型参考体系结构，Azure 中的 VNet 将用于模拟本地网络。

- **VPN 设备**。 用于与本地网络建立外部连接的 VPN 设备或服务。 VPN 设备可以是硬件设备，也可以是软件解决方案。 

- **中心 VNet**。 中心是本地网络的中心连接点，也是服务的托管位置。 这些服务可供辐射 VNet 中托管的不同工作负荷使用。

- **网关子网**。 VNet 网关保留在同一子网中。

- **辐射 VNet**。 辐射可以用来隔离其自己的 VNet 中的工作负荷，独立于其他辐射进行管理。 每个工作负荷可以包括多个层，并具有通过 Azure 负载均衡器连接的多个子网。 

- **VNet 对等互连**。 可以使用对等互连来连接两个 VNet。 对等互连连接是 VNet 之间的不可传递低延迟连接。 对等互连后，VNet 将使用 Azure 主干交换流量，而无需路由器。 在中心辐射型网络拓扑中，使用 VNet 对等互连将中心连接到每个辐射。 可以对等互连相同区域或不同区域中的 VNet。

## <a name="create-the-directory-structure"></a>创建目录结构

创建包含 Terraform 配置文件的目录用于演示。

1. 浏览到 [Azure 门户](https://portal.azure.com)。

1. 打开 [Azure Cloud Shell](/azure/cloud-shell/overview)。 如果事先未选择环境，请选择“Bash”作为环境。 

    ![Cloud Shell 提示符](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. 切换到 `clouddrive` 目录。

    ```bash
    cd clouddrive
    ```

1. 创建名为 `hub-spoke` 的目录。

    ```bash
    mkdir hub-spoke
    ```

1. 将目录切换到新目录：

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>声明 Azure 提供程序

创建声明 Azure 提供程序的 Terraform 配置文件。

1. 在 Cloud Shell 中，打开名为 `main.tf` 的新文件。

    ```bash
    code main.tf
    ```

1. 在编辑器中粘贴以下代码：

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. 保存该文件并退出编辑器。

## <a name="create-the-variables-file"></a>创建 variables 文件

为在不同脚本中使用的通用变量创建 Terraform 配置文件。

1. 在 Cloud Shell 中，打开名为 `variables.tf` 的新文件。

    ```bash
    code variables.tf
    ```

1. 在编辑器中粘贴以下代码：

    ```hcl
    variable "location" {
      description = "Location of the network"
      default     = "centralus"
    }
    
    variable "username" {
      description = "Username for Virtual Machines"
      default     = "testadmin"
    }
    
    variable "password" {
      description = "Password for Virtual Machines"
      default     = "Password1234!"
    }
    
    variable "vmsize" {
      description = "Size of the VMs"
      default     = "Standard_DS1_v2"
    }
    ```

1. 保存文件并退出编辑器。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [在 Azure 中使用 Terraform 创建本地虚拟网络](./terraform-hub-spoke-on-prem.md)