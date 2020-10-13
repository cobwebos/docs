---
title: 教程 - 部署和配置 VMware HCX
description: 了解如何为 Azure VMware 解决方案私有云部署和配置 VMware HCX 解决方案。
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 69832d1537f0f1be95d3283f543ef6e54187b58d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578688"
---
# <a name="deploy-and-configure-vmware-hcx"></a>部署和配置 VMware HCX

本文将演练为 Azure VMWare 解决方案私有云部署和配置 VMware HCX 的过程。 使用 VMware HCX，可以通过各种迁移类型将 VMware 工作负荷迁移到 Azure VMware 解决方案和其他已连接站点。

VMware HCX Advanced（预部署在 Azure VMware 解决方案中）最多支持三个站点连接（本地到云，或云到云）。 如果 VMware HCX Enterprise 需要三个以上的站点连接，则可以选择通过提交[支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)来启用 [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) 加载项（目前为预览版）。 Azure VMware 解决方案以预览版功能/服务的形式提供了 VMware HCX Enterprise Edition (EE)。 虽然 Azure VMware 解决方案的 VMware HCX EE 为预览版，但它是一项免费的功能/服务，并且遵守预览版服务条款和条件。 在 VMware HCX EE 服务正式发布后，你会收到一个 30 天的通知，指出计费将会进行切换。 你还可以选择关闭/退出服务。

在开始之前，请仔细查看[准备工作](#before-you-begin)、[软件版本要求](#software-version-requirements)和[先决条件](#prerequisites)。 

然后，我们会演练所有必需过程，以便：

> [!div class="checklist"]
> * 部署本地 VMware HCX OVA（连接器）
> * 激活 VMware HCX
> * 将本地环境与 Azure VMware 解决方案环境配对。
> * 配置互连（计算配置文件、网络配置文件和服务网格）
> * 通过检查设备状态来完成设置。

完成后，可以按照本文末尾建议的后续步骤进行操作。  

## <a name="before-you-begin"></a>准备工作
   
* 查看基本的 Azure VMware 解决方案软件定义数据中心 (SDDC) [教程系列](tutorial-network-checklist.md)。
* 查看并参阅 [VMware HCX 文档](https://docs.vmware.com/en/VMware-HCX/index.html)，包括 HCX 用户指南。
* 查看 VMware 文档：[使用 VMware HCX 迁移虚拟机](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)。
* （可选）查看 [VMware HCX 部署注意事项](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)。
* （可选）查看 HCX 的相关 VMware 材料，如 HCX 的 VMware vSphere [博客系列](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)。 
* （可选）通过 Azure VMware 解决方案支持渠道请求 Azure VMware 解决方案 HCX 企业版激活。
* 为了部署 WAN 互连设备，已经从客户提供的 `\22` 分配了特定的 CIDR 范围，用于进行私有云创建操作。

根据计算和存储资源调整工作负荷的大小是一个重要的规划步骤。 此调整大小步骤问题应在初始私有云环境规划过程中解决。 

还可以通过在 Azure Migrate 门户中完成 [Azure VMware 解决方案评估](../migrate/how-to-create-azure-vmware-solution-assessment.md)来调整工作负荷的大小。

## <a name="software-version-requirements"></a>软件版本要求

基础结构组件必须运行所需的最低版本。 
                                                         
| 组件类型    | 源环境要求    | 目标环境要求   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>如果使用 5.5 U1 或更低版本，请为 HCX 操作使用独立 HCX 用户界面。  | 6.0 U2 及更高版本   |
| ESXi   | 5.0    | ESXi 6.0 及更高版本   |
| NSX    | 对于源上的逻辑交换机的 HCX 网络扩展：NSXv 6.2+ 或 NSX-T 2.4+   | NSXv 6.2+ 或 NSX-T 2.4+<br/><br/>对于 HCX 邻近路由：NSXv 6.4+（NSX-T 不支持邻近路由） |
| vCloud Director   | 不需要 - 源站点上不与 vCloud Director 进行互操作 | 将目标环境与 vCloud Director 集成时，最低版本为 9.1.0.2。  |

## <a name="prerequisites"></a>先决条件

### <a name="network-and-ports"></a>网络和端口

* 在本地与 Azure VMware 解决方案 SDDC ExpressRoute 线路之间配置 [ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md)。

* 若要在本地组件之间以及在本地与 Azure VMware 解决方案 SDDC 之间进行通信，应打开所有必需的端口（请参阅 [VMware HCX 文档](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)）。

* 本地 HCX IX 和 NE 设备应该能够访问 vCenter 和 ESXi 基础结构。

### <a name="ip-addresses"></a>IP 地址

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>在本地部署 VMware HCX OVA

>[!NOTE]
>将虚拟设备部署到本地 vCenter 之前，需要下载 VMware HCX OVA。 

1. 使用 **cloudadmin** 用户凭据通过 `https://x.x.x.9` 端口 443 登录到 Azure VMware 解决方案 HCX 管理器，然后转到“支持”。

   >[!TIP]
   >若要确定 HCX 管理器的 IP 地址，请在“Azure VMware 解决方案”边栏选项卡中转到“管理” > “连接性”，然后选择“HCX”选项卡。 
   >
   >vCenter 密码是在设置私有云时定义的。

1. 选择“下载”链接以下载 VMware HCX OVA 文件。

1. 转到本地 vCenter，选择要部署到本地 vCenter 的 OVF 模板（你下载的 OVA 文件）。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. 选择名称和位置，以及要在其中部署 HCX 的资源/群集，然后查看详细信息和所需的资源。  

   :::image type="content" source="media/tutorial-vmware-hcx/configure-template.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/configure-template.png":::

1. 查看许可条款，如果同意，则选择所需的存储和网络，然后选择“下一步”。

1. 在“自定义模板”中，输入所有必需的信息。 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. 选择“下一步”，验证配置，然后选择“完成”以部署 HCX OVA。
     
   >[!NOTE]
   >通常，你现在部署的 VMware HCX 管理器将部署到群集的管理网络上。  
   
   > [!IMPORTANT]
   > 部署完成后，你可能需要手动启动虚拟设备。
   > 打开 HCX 设备后，请等待 10-15 分钟，然后再继续执行下一步。

有关此步骤的端到端概述，请观看 [Azure VMware 解决方案 - VMware HCX 设备部署](https://www.youtube.com/embed/BwSnQeefnso)视频。 


## <a name="activate-vmware-hcx"></a>激活 VMware HCX

在本地部署 VMware HCX OVA 后，便可以激活 VMware HCX。 你需要先检索许可证，然后才能激活 VMware HCX。

1. 在 Azure VMware 解决方案中，转到“管理” > “连接性”，选择“HCX”选项卡，然后选择“添加”。

1. 使用 **admin** 用户凭据登录到 `https://HCXManagerIP:9443` 处的本地 VMware HCX 管理器。 

   > [!IMPORTANT]
   > 确保在 VMware HCX 管理器 IP 地址中包括 `9443` 端口号。

1. 在“许可”中，输入“HCX Advanced 密钥”。  
   
    > [!NOTE]
    > VMware HCX 管理器必须开放 Internet 访问权限或配置一个代理。

1. 在“数据中心位置”中，提供在本地安装 VMware HCX 管理器的最近位置。

1. 修改“系统名称”或接受默认值。
   
1. 你可以稍后完成，也可以继续。选择选项“是，继续”即可继续。
    
1. 在“连接 vCenter”中，提供你的 vCenter 服务器的 FQDN 或 IP 地址和相应的凭据，然后选择“继续”。
   
1. 在“配置 SSO/PSC”中，提供你的 PSC 的 FQDN 或 IP 地址，然后选择“继续”。
   
   >[!NOTE]
   >通常与你的 vCenter FQDN/IP 相同。

1. 验证所有输入是否正确，并选择“重启”。
    
   > [!NOTE]
   > 重启后，会有一段延迟，然后系统才会提示你执行下一步。
   >
   >在服务重启后，你会在出现的屏幕上看到 vCenter 显示为绿色，这一点非常重要。 VCenter 和 SSO 都具有相应的配置参数，它们应该与上一个屏幕相同。

   :::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

有关此步骤的端到端概述，请观看 [Azure VMware 解决方案 - VMware HCX 激活](https://www.youtube.com/embed/BkAV_TNYxdE)视频。


## <a name="configure-vmware-hcx"></a>配置 VMware HCX

现在，你已准备好添加站点配对、创建网络和计算配置文件，以及启用服务，例如迁移、网络扩展或灾难恢复。 

### <a name="add-a-site-pairing"></a>添加站点配对

你可以将 Azure VMware 解决方案中的 VMware HCX 管理器与数据中心内的 VMware HCX 管理器进行连接（配对）。 

1. 登录到你的本地 vCenter，然后在“主页”下选择“HCX”。

   :::image type="content" source="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/vcenter-vmware-hcx.png":::

1. 在“基础结构”下，选择“站点配对”，然后选择“连接到远程站点”选项（在屏幕中间）。 

   :::image type="content" source="media/tutorial-vmware-hcx/connect-remote-site.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/connect-remote-site.png":::

1. 输入**远程 HCX URL 或 IP 地址**、Azure VMware 解决方案的 cloudadmin@vsphere.local 用户名以及**密码**，然后选择“连接”。

   > [!NOTE]
   > **远程 HCX URL** 是你的 Azure VMware 解决方案私有云 HCX 管理器，通常是管理网络的“.9”地址。  例如，如果你的 vCenter 是 192.168.4.2，则你的 HCX URL 将是 192.168.4.9。
   >
   > **密码**与用于登录到 vCenter 的密码相同。 你已在初始部署屏幕上定义了此密码。

   你会看到一个屏幕，其中显示了 Azure VMware 解决方案中的 HCX 管理器，以及在本地连接（配对）的 HCX 管理器。

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。":::

有关此步骤的端到端概述，请观看 [Azure VMware 解决方案 - VMware HCX 站点配对](https://www.youtube.com/embed/sKizDCRHOko)视频。



### <a name="create-network-profiles"></a>创建网络配置文件

VMware HCX 部署了几个虚拟设备（自动），但需要多个 IP 段。  当你创建网络配置文件时，你将定义在 [VMware HCX 网络段预部署准备和规划阶段](production-ready-deployment-steps.md#vmware-hcx-network-segments)确定的 IP 段。

你将创建四个网络配置文件：

   - 管理
   - vMotion
   - 复制
   - 上行

1. 在“基础结构”下，选择“互连” > “多站点服务网格” > “网络配置文件” > “创建网络配置文件”。    

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. 对于每个网络配置文件，请选择网络、端口组，提供名称，为该特定段创建 IP 池，然后选择“创建”。 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。":::

有关此步骤的端到端概述，请观看 [Azure VMware 解决方案 - VMware HCX 创建网络配置文件](https://www.youtube.com/embed/NhyEcLco4JY)视频。


### <a name="create-compute-profile"></a>创建计算配置文件

1. 选择“计算配置文件” > “创建计算配置文件”。

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. 输入配置文件的名称，然后选择“继续”。  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. 选择要启用的服务，例如迁移、网络扩展或灾难恢复，然后选择“继续”。
  
   > [!NOTE]
   > 通常，此处将不会更改任何内容。

1. 在“选择服务资源”中，选择要为其启用选定的 VMware HCX 服务的一个或多个服务资源（群集）。  

1. 看到本地数据中心的群集后，选择“继续”。

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. 从“选择数据存储”中，选择用于部署 VMware HCX 互连设备的数据存储资源，然后选择“继续”。

   选择了多个资源时，VMware HCX 会使用所选的第一个资源，直到其容量用尽。   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. 选择“管理网络配置文件”、你在前面的步骤中创建的管理网络配置文件，然后选择“继续”。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

   > [!NOTE]
   > 管理网络配置文件允许 VMware HCX 设备与 vCenter 通信，因此可以访问 ESXi 主机。

1. 选择“上行网络配置文件”、你在前面的步骤中创建的上行网络配置文件，然后选择“继续”。

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. 选择“vMotion 网络配置文件”、你在前面的步骤中创建的 vMotion 网络配置文件，然后选择“继续”。

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. 选择“vSphere 复制网络配置文件”、你在前面的步骤中创建的复制网络配置文件，然后选择“继续”。

   在大多数情况下，复制网络配置文件与管理网络配置文件相同。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. 选择“用于网络扩展的分布式交换机”、要迁移到第 2 层扩展网络上的 Azure VMware 解决方案的虚拟机所在的分布式虚拟网络，然后选择“继续”。

   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. 查看连接规则，然后选择“继续”。  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. 选择“完成”以创建完整配置文件。

   你会看到与下面显示的屏幕类似的屏幕。

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

有关此步骤的端到端概述，请观看 [Azure VMware 解决方案 - VMware HCX 创建计算配置文件](https://www.youtube.com/embed/qASXi5xrFzM)视频。




### <a name="create-service-mesh"></a>创建服务网格

现在，可以在本地与 Azure VMware 解决方案 SDDC 之间配置服务网格了。

1. 在“基础结构”下，选择“互连” > “服务网格” > “创建服务网格”。      

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. 查看预填充的站点，然后选择“继续”。 

   >[!NOTE]
   >如果这是你的第一个服务网格配置，则无需修改此屏幕。  

1. 选择源计算配置文件和远程计算配置文件的下拉框，然后选择“继续”。  

   这些选择定义 VM 可以在其中消耗 VMware HCX 服务的资源。  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. 查看将启用的服务，然后选择“继续”。  

1. 在“高级配置 - 替代上行网络配置文件”中，选择“继续”。  上行网络配置文件连接到网络，可以通过该网络访问远程站点的互连设备。  
  
1. 在“高级配置 - 网络扩展设备横向扩展”中进行检查，然后选择“继续”。 

1. 在“高级配置 - 流量工程”中进行检查并做出必要的修改，然后选择“继续”。

1. 查看拓扑预览，然后选择“继续”。

1. 为此服务网格输入一个用户易记名称，然后选择“完成”以完成此操作。  

1. 选择“查看任务”以监视部署。 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。":::

   当服务网格部署成功完成时，你会看到服务为绿色。

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. 通过检查设备状态验证服务网格的运行状况，选择“互联” > “设备”。 

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

有关此步骤的端到端概述，请观看 [Azure VMware 解决方案 - VMware HCX 创建服务网格](https://www.youtube.com/embed/FyZ0d3P_T24)视频。



### <a name="optional-create-a-network-extension"></a>（可选）创建网络扩展

如果你希望将任何网络从本地环境扩展到 Azure VMware 解决方案，请执行以下步骤来扩展这些网络。

1. 在“服务”下，选择“网络扩展”，然后选择“创建网络扩展”。

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. 选择你要将其扩展到 Azure VMware 解决方案的每个网络，然后选择“下一步”。

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。":::

1. 输入要扩展的每个网络的本地网关 IP，然后选择“提交”。 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。":::

   只需几分钟时间即可完成网络扩展。 然后，你会看到状态更改为“扩展完成”。

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="转到本地 vCenter 并选择要部署到本地 vCenter 的 OVF 模板。" lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

有关此步骤的端到端概述，请观看 [Azure VMware 解决方案 - VMware HCX 网络扩展](https://www.youtube.com/embed/cNlp0f_tTr0)视频。


## <a name="next-steps"></a>后续步骤

如果你已完成到这里，并且设备互连隧道状态为“正常”且其颜色为绿色，则可以使用 VMware HCX 来迁移和保护 Azure VMware 解决方案 VM。  Azure VMware 解决方案支持工作负荷迁移（带有或不带网络扩展）。  你仍然可以在 vSphere 环境中执行工作负荷迁移，在本地创建网络并将 VM 部署到这些网络上。  有关详细信息，请参阅 VMware 技术文档中的 [VMware HCX 文档](https://docs.vmware.com/en/VMware-HCX/index.html)和[使用 VMware HCX 迁移虚拟机](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)，其中详细介绍了如何使用 HCX。
