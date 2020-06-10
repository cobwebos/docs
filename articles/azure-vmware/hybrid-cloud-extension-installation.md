---
title: 安装混合云扩展 (HCX)
description: 设置适用于 Azure VMware 解决方案 (AVS) 私有云的 VMware 混合云扩展 (HCX) 解决方案
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: dc5f7f82b83c82538b2d5a7b4c87131afb3fcc20
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873654"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>安装适用于 Azure VMware 解决方案的 HCX

在本文中，我们会演示设置适用于 Azure VMware 解决方案 (AVS) 私有云的 VMware 混合云扩展 (HCX) 解决方案的过程。 HCX Advanced（默认安装）最多支持三个外部站点，其中每个外部站点都需要安装并激活一个 HCX Enterprise 管理器或连接器。
使用 HCX 可以通过 HCX 支持的各种内置迁移类型将 VMware 工作负载迁移到云和/或其他连接的站点。 如果需要三个以上的站点，则客户可以选择通过支持启用 HCX Enterprise 附加产品。 正式发布 (GA) 之后，HCX Enterprise 会向客户收取额外费用，但是会提供[其他功能](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)。

首先，请仔细查看[开始之前](#before-you-begin)、[软件版本要求](#software-version-requirements)和[先决条件](#prerequisites)。 

随后，我们会演练所有必需过程，以便：

> [!div class="checklist"]
> * 部署本地 HCX OVA
> * 激活并配置 HCX
> * 配置网络上行和服务网格
> * 通过检查设备状态来完成设置

完成设置之后，会提供建议的后续步骤。

## <a name="before-you-begin"></a>开始之前
    
* 查看基本的 AVS 软件定义数据中心 (SDDC) [教程系列](tutorial-network-checklist.md)
* 查看并参阅 [VMware HCX 文档](https://docs.vmware.com/en/VMware-HCX/index.html)，包括 HCX 用户指南
* 查看 VMware 文档[使用 VMware HCX 迁移虚拟机](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* （可选）查看 [VMware HCX 部署注意事项](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* （可选）查看 HCX 的相关 VMware 材料，如 HCX 的 VMware vSphere [博客系列](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)。 
* 通过 AVS 支持渠道订购 AVS HCX Enterprise 激活。

在准备使用 AVS 私有云 HCX 解决方案时，按照计算和存储资源调整工作负载大小是一个重要的规划步骤。 此调整大小步骤应在初始私有云环境规划过程中进行解决。 

## <a name="software-version-requirements"></a>软件版本要求
基础结构组件必须运行所需的最低版本。 
                                                         
| 组件类型                                                          | 源环境要求                                                                   | 目标环境要求                                                                      |
| --- | --- | --- |
| vCenter Server                                                          | 5.1<br/><br/>如果使用 5.5 U1 或更低版本，请为 HCX 操作使用独立 HCX 用户界面。         | 6.0 U2 及更高版本                                                                                          |
| ESXi                                                                    | 5.0                                                                                               | ESXi 6.0 及更高版本                                                                                        |
| NSX                                                                     | 对于源上的逻辑交换机的 HCX 网络扩展：NSXv 6.2+ 或 NSX-T 2.4+              | NSXv 6.2+ 或 NSX-T 2.4+<br/><br/对于 HCX 邻近路由：NSXv 6.4+（NSX-T 不支持邻近路由） |
| vCloud Director                                                         | 不需要 - 源站点上不与 vCloud Director 进行互操作 | 当目标环境与 vCloud Director 集成时，最低版本为 9.1.0.2。              |

## <a name="prerequisites"></a>先决条件

* 应在本地与 AVS SDDC ER 线路之间配置 Global Reach。

* 应在本地与 AVS SDDC 之间打开所有所需端口（请参阅 [VMware HCX 文档](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)）。

* 一个 IP 地址用于本地的 HCX 管理器，并且至少有两个 IP 地址用于互连 (IX) 和网络扩展 (NE) 设备。

* 本地 HCX IX 和 NE 设备应该能够访问 vCenter 和 ESXi 基础结构。

* 若要部署 WAN 互连设备，除了在 Azure 门户中用于 SDDC 部署的 /22 CIDR 网络地址块以外，HCX 还需要一个 /29 块。 应将此纳入到网络规划中。

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>在本地部署 VMware HCX OVA

1. 登录 AVS SDDC vCenter，然后选择“HCX”。

    ![在 AVS vCenter 中选择 HCX](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. 若要下载 VMware HCX OVA 文件，请选择“管理” > “系统更新”。

    ![获取系统更新](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. 选择要部署到本地 vCenter 的 OVF 模板。  

    ![选择 OVF 模板](./media/hybrid-cloud-extension-installation/select-template.png)

1. 选择名称和位置，然后选择需要在其中部署 HCX 的资源/群集。 然后查看详细信息和所需资源。  

    ![查看模板详细信息](./media/hybrid-cloud-extension-installation/configure-template.png)

1. 查看许可条款，如果同意，则选择所需存储和网络。 然后，选择“下一步”。

1. 在“自定义模板”中，输入所有必需的信息。 

    ![自定义模板](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. 选择“下一步”，验证配置，然后选择“完成”以部署 HCX OVA。

## <a name="activate-hcx"></a>激活 HCX

安装之后，执行以下步骤。

1. 在 `https://HCXManagerIP:9443` 处打开 HCX 管理器，然后使用你的用户名和密码登录。 

1. 在“许可”中，输入“HCX Advanced 密钥”。  

    ![输入 HCX 密钥](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX 管理器必须开放 Internet 访问或配置代理。

1. 配置 vCenter。

    ![配置 VCenter](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. 在“数据中心位置”中，如有必要，编辑数据中心位置。

    ![数据库位置](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>配置 HCX 

1. 登录本地 vCenter，然后选择“主页” > “HCX”。

    ![VCenter 中的 HCX](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. 选择“基础结构” > “站点配对” > “添加站点配对”。

    ![添加站点配对](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. 输入“远程 HCX URL”、“用户名”和“密码”。 然后选择“连接”。

   系统会显示连接的站点。
   
    ![站点连接](./media/hybrid-cloud-extension-installation/site-connection.png)

1. 选择“互连” > “多站点服务网格” > “网络配置文件” > “创建网络配置文件”。

    ![创建网络配置文件](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. 输入 HCX IX 和 NE IP 地址范围（最少需要 2 个 IP 地址以用于 IX 和 NE 设备）。
    
   ![输入 IP 地址范围](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > 网络扩展设备 (HCX-NE) 与分布式虚拟交换机 (DVS) 具有一对一关系。  

1. 现在选择“计算配置文件” > “创建计算配置文件”。

1. 输入计算配置文件名称，然后选择“继续”。  

    ![创建计算配置文件](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. 选择要启用的服务，如迁移、网络扩展、pr 灾难恢复。 选择“继续”。

    ![选择服务](./media/hybrid-cloud-extension-installation/select-services.png)

1. 在“选择服务资源”中，选择应该为其启用所选 HCX 服务的一个或多个服务资源。 选择“继续”。
    
   ![选择服务资源](./media/hybrid-cloud-extension-installation/select-service-resources.png)
  
   > [!NOTE]
   > 选择特定群集，会针对其中的源 VM 使用 HCX 进行迁移。

1. 选择“数据存储”，然后选择“继续” 。 
      
    选择每个计算和存储资源，以用于部署 HCX 互连设备。 选择多个资源时，HCX 会使用所选的第一个资源，直到其容量用尽。  
    
    ![选择部署资源](./media/hybrid-cloud-extension-installation/deployment-resources.png)

1. 选择在“网络配置文件”中创建的管理网络配置文件，然后选择“继续”。  
      
    选择可以用于访问 vCenter 和 ESXi 主机的管理接口的网络配置文件。 如果尚未定义此类网络配置文件，则可以在此处进行创建。  
    
    ![选择管理网络配置文件](./media/hybrid-cloud-extension-installation/management-network-profile.png)

1. 选择“网络上行”，然后选择“继续” 。
      
    选择一个或多个网络配置文件，以便满足以下条件之一：  
    * 可以通过此网络访问远程站点上的互连设备  
    * 远程端设备可以通过此网络访问本地互连设备。  
    
    如果你有不在多个站点之间共享的点对点网络（如直接连接），则可以跳过此步骤，因为计算配置文件与多个站点共享。 在这种情况下，可以在创建互连服务网格期间替代和指定上行网络配置文件。  
    
    ![选择上行网络配置文件](./media/hybrid-cloud-extension-installation/uplink-network-profile.png)

1. 选择“vMotion 网络配置文件”，然后选择“继续”。
      
    选择可以用于访问 ESXi 主机的 vMotion 接口的网络配置文件。 如果尚未定义此类网络配置文件，则可以在此处进行创建。 如果没有 vMotion 网络，请选择“管理网络配置文件”。  
    
    ![选择 vMotion 网络配置文件](./media/hybrid-cloud-extension-installation/vmotion-network-profile.png)

1. 选择“vSphere Replication 网络配置文件”，然后选择“继续”。
      
    选择可以用于访问 ESXi 主机的 vSphere Replication 接口的网络配置文件。 在大多数情况下，此配置文件与管理网络配置文件相同。  
    
    ![选择 vSphere Replication 网络配置文件](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. 选择“用于网络扩展的分布式交换机”，然后选择“继续”。  
      
    选择分布式虚拟交换机，你会在这些交换机上建立将迁移的虚拟机要连接到的网络。

    ![选择分布式虚拟交换机](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. 查看连接规则，然后选择“继续”。 选择“完成”以创建完整配置文件。  

    ![创建计算配置文件](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

## <a name="configure-network-uplink"></a>配置网络上行

现在，在 AVS SDDC 中为网络上行配置网络配置文件更改。

1. 登录 SDDC NSX-T 以创建新的逻辑交换机，或使用现有逻辑交换机，该交换机可用于本地与 AVS SDDC 之间的网络上行。

1. 在 AVS SDDC 中为 HCX 上行创建网络配置文件，该配置文件可用于本地到 AVS SDDC 的通信。  
    
   ![为上行创建网络配置文件](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. 为网络配置文件输入名称，并基于所需的 L2 网络扩展输入至少 4-5 个可用 IP 地址。  
    
   ![为上行配置网络配置文件](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. 选择“创建”以完成 AVS SDDC 配置

## <a name="configure-service-mesh"></a>配置服务网格

现在，在本地与 AVS SDDC 之间配置服务网格。

1. 登录 AVS SDDC vCenter，然后选择“HCX”。

1. 选择“基础结构” > “互连” > “服务网格” > “创建服务网格”。  配置在前面步骤中创建的网络和计算配置文件。    
      
    ![配置服务网格](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

1. 选择“创建服务网格”，然后选择“继续”。  
      
    选择要在其之间启用混合移动性的配对站点。  
    
    ![选择配对站点](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

1. 选择“计算配置文件”，然后选择“继续” 。
      
    在源和远程站点中分别选择一个计算配置文件来启用混合性服务。 该选择会定义资源，其中虚拟机将能够使用 HCX 服务。  
      
    ![启用混合性服务](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

1. 选择要为 HCX 启用的服务，然后选择“继续”。  
      
    ![选择 HCX 服务](./media/hybrid-cloud-extension-installation/hcx-services.png)

1. 在“高级配置 - 替代上行网络配置文件”中，选择“继续”。  
      
    上行网络配置文件用于连接到可通过其访问远程站点互连设备的网络。  
      
    ![替代上行配置文件](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

1. 在“高级配置 – 网络扩展设备横向扩展”中，选择“配置网络扩展设备横向扩展”。 
      
    ![网络扩展横向扩展](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

1. 输入与 DVS 交换机计数对应的设备计数。  
      
    ![配置设备计数](./media/hybrid-cloud-extension-installation/appliance-scale.png)

1. 在“高级配置 - 流量工程”中，选择“继续”。  
      
    ![配置流量工程](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

1. 查看拓扑预览，然后选择“继续”。 然后为此服务网格输入用户友好名称，并选择“完成”以完成。  
      
    ![完成服务网格](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    服务网格会进行部署和配置。  
      
    ![已部署的服务网格](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>检查设备状态
若要检查设备的状态，请选择“互连” > “设备”。 
      
![设备状态](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>后续步骤

当设备互连“隧道状态”为“正常”并且为绿色时，便可以使用 HCX 迁移和保护 AVS VM。 请参阅 VMware 技术文档中的 [VMware HCX 文档](https://docs.vmware.com/en/VMware-HCX/index.html)和[使用 VMware HCX 迁移虚拟机](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)。
