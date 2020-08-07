---
title: 安装混合云扩展 (HCX)
description: 设置适用于 Azure VMware 解决方案 (AVS) 私有云的 VMware 混合云扩展 (HCX) 解决方案
ms.topic: how-to
ms.date: 07/15/2020
ms.openlocfilehash: 84388c3ec53d9067df2580aabb21ca5885d154b8
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904987"
---
# <a name="install-hcx-for-azure-vmware-solution"></a>安装适用于 Azure VMware 解决方案的 HCX

本文介绍如何设置 VMWare 混合云扩展 (适用于 Azure VMWare 解决方案 (AVS) 私有云的 HCX) 解决方案。 HCX 允许将 VMware 工作负荷迁移到云，并通过各种内置 HCX 支持的迁移类型迁移到其他连接的站点。

HCX 高级（默认安装）支持 (本地或云到云) 的最多三个站点连接。 如果需要三个以上的站点连接，客户可以选择通过支持（当前为预览版）启用 HCX Enterprise 外接程序。 正式发布 (GA) 之后，HCX Enterprise 会向客户收取额外费用，但是会提供[其他功能](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/)。


首先，请仔细查看[开始之前](#before-you-begin)、[软件版本要求](#software-version-requirements)和[先决条件](#prerequisites)。 

接下来，我们将演练所有必要的过程，以便：

> [!div class="checklist"]
> * 部署本地 HCX .OVA (连接器) 
> * 激活并配置 HCX
> * 配置网络上行和服务网格
> * 通过检查设备状态来完成设置

完成设置后，可以按照本文末尾提供的建议后续步骤进行操作。  

## <a name="before-you-begin"></a>开始之前
    
* 查看基本的 AVS 软件定义数据中心 (SDDC) [教程系列](tutorial-network-checklist.md)。
* 查看并参考[VMWARE HCX 文档](https://docs.vmware.com/en/VMware-HCX/index.html)，其中包括 HCX 用户指南。
* 查看 VMware 文档[通过 Vmware 迁移虚拟机 HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)。
* 可以查看[VMWARE HCX 部署注意事项](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)。
* （可选）查看 HCX 的相关 VMware 材料，如 HCX 的 VMware vSphere [博客系列](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html)。 
* 通过 AVS 支持渠道请求 AVS HCX 企业激活。

当你准备使用 AVS 私有云 HCX 解决方案时，根据计算和存储资源调整工作负载规模是一种重要的规划步骤。 作为初始私有云环境规划的一部分，处理调整大小步骤。 

还可以通过在 Azure Migrate 门户 (中完成 AVS 评估来调整工作负荷的大小 https://docs.microsoft.com/azure/migrate/how-to-create-azure-vmware-solution-assessment) 。

## <a name="software-version-requirements"></a>软件版本要求

基础结构组件必须运行所需的最低版本。 
                                                         
| 组件类型    | 源环境要求    | 目标环境要求   |
| --- | --- | --- |
| vCenter Server   | 5.1<br/><br/>如果使用 5.5 U1 或更早版本，请为 HCX 操作使用独立的 HCX 用户界面。  | 6.0 U2 及更高版本   |
| ESXi   | 5.0    | ESXi 6.0 及更高版本   |
| NSX    | 对于源上的逻辑交换机的 HCX 网络扩展： NSXv 6.2 + 或 NSX-T 2.4 +   | NSXv 6.2+ 或 NSX-T 2.4+<br/><br/>对于 HCX 邻近路由：不) 支持 NSXv 6.4 + (近程路由 |
| vCloud Director   | 不需要 - 源站点上不与 vCloud Director 进行互操作 | 将目标环境与 Vcloud 集成控制器集成时，最小值为9.1.0.2。  |

## <a name="prerequisites"></a>先决条件

* 应在本地和 AVS SDDC ExpressRoute 线路之间配置 ExpressRoute Global Reach。

* 应在本地与 AVS SDDC 之间打开所有所需端口（请参阅 [VMware HCX 文档](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)）。

* HCX Manager 在本地提供一个 IP 地址，最少两个 IP 地址用于互连 (IX) 和网络扩展 (NE) 设备。

* 本地 HCX IX 和 NE 设备应该能够访问 vCenter 和 ESXi 基础结构。

* 若要部署 WAN 互连设备，除了在 Azure 门户中用于 SDDC 部署的 /22 CIDR 网络地址块以外，HCX 还需要一个 /29 块。 请确保将此要求分解为网络规划。

## <a name="deploy-the-vmware-hcx-ova-on-premises"></a>在本地部署 VMware HCX OVA

1. 登录 AVS SDDC vCenter，然后选择“HCX”。

    ![在 AVS vCenter 中选择 HCX](./media/hybrid-cloud-extension-installation/avs-vsphere-client.png)

1. 在 "**管理**" 下，选择 "**系统更新**"，然后选择 "**请求下载链接**" 下载 VMware HCX .ova 文件。

    ![获取系统更新](./media/hybrid-cloud-extension-installation/administration-updates.png)

1. 接下来，请前往本地 vCenter，选择要部署到本地 vCenter 的 OVF 模板。  

    ![选择 OVF 模板](./media/hybrid-cloud-extension-installation/select-template.png)

1. 选择名称和位置，然后选择需要在其中部署 HCX 的资源/群集。 然后查看详细信息和所需资源。  

    ![查看模板详细信息](./media/hybrid-cloud-extension-installation/configure-template.png)

1. 查看许可条款，如果同意，则选择所需存储和网络。 然后，选择“下一步”。

1. 在“自定义模板”中，输入所有必需的信息。 

    ![自定义模板](./media/hybrid-cloud-extension-installation/customize-template.png)  

1. 选择“下一步”，验证配置，然后选择“完成”以部署 HCX OVA。

## <a name="activate-hcx"></a>激活 HCX

安装之后，执行以下步骤。

1. 登录到本地 HCX manager `https://HCXManagerIP:9443` ，并通过用户名和密码进行登录。 

   > [!IMPORTANT]
   > 请确保将 `9443` 端口号包含在 HCX MANAGER IP 地址中。

1. 在“许可”中，输入“HCX Advanced 密钥”。  

    ![输入 HCX 密钥](./media/hybrid-cloud-extension-installation/hcx-key.png)  
    
    > [!NOTE]
    > HCX 管理器必须开放 Internet 访问或配置代理。

1. 如果需要，请在**vcenter**中编辑 vcenter 信息。

    ![配置 VCenter](./media/hybrid-cloud-extension-installation/configure-vcenter.png)

1. 在“数据中心位置”中，如有必要，编辑数据中心位置。

    ![数据库位置](./media/hybrid-cloud-extension-installation/system-location.png)

## <a name="configure-hcx"></a>配置 HCX 

1. 登录到本地 vCenter，然后在 "**主页**" 下选择 " **HCX**"。

    ![VCenter 中的 HCX](./media/hybrid-cloud-extension-installation/hcx-vcenter.png)

1. 在 "**基础结构**" 下，选择 "**站点配对**" "  >  **添加站点配对**"。

    ![添加站点配对](./media/hybrid-cloud-extension-installation/site-pairing.png)

1. 输入远程 HCX URL 或 IP 地址、AVS cloudadmin 用户名和密码，然后选择 "**连接**"。

   系统会显示连接的站点。
   
    ![站点连接](./media/hybrid-cloud-extension-installation/site-connection.png)

1. 在 "**基础结构**" 下，选择 "**互连**  >  **多站点服务网格**  >  **网络配置**文件" "  >  **创建网络配置文件**"。

    ![创建网络配置文件](./media/hybrid-cloud-extension-installation/create-network-profile.png)

1. 对于新的网络配置文件，输入 HCX IX 和 NE IP 地址范围 (IX 和 NE 装置) 至少需要两个 IP 地址。
    
   ![输入 IP 地址范围](./media/hybrid-cloud-extension-installation/enter-address-ranges.png)
  
   > [!NOTE]
   > 网络扩展设备 (HCX-NE) 与分布式虚拟交换机 (DVS) 具有一对一关系。  

1. 现在选择“计算配置文件” > “创建计算配置文件”。

1. 输入计算配置文件名称，然后选择“继续”。  

    ![创建计算配置文件](./media/hybrid-cloud-extension-installation/create-compute-profile.png)

1. 选择要启用的服务，如迁移、网络扩展或灾难恢复，然后选择 "**继续**"。

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

1. 从 "**选择 VSphere 复制网络配置文件**" 中，选择网络配置文件 VSphere 的 ESXi 主机的复制接口，然后选择 "**继续**"。
      
   在大多数情况下，此配置文件与管理网络配置文件相同。  
    
   ![选择 vSphere Replication 网络配置文件](./media/hybrid-cloud-extension-installation/vsphere-replication-network-profile.png)

1. 从 "**选择网络扩展的分布式交换机**" 中，选择要将集成并连接到其网络的虚拟机所在的 DVS。  选择“继续”。  
      
    ![选择分布式虚拟交换机](./media/hybrid-cloud-extension-installation/distributed-switches.png)

1. 查看连接规则并选择 "**继续**"。  

    ![创建计算配置文件](./media/hybrid-cloud-extension-installation/complete-compute-profile.png)

1.  选择“完成”以创建完整配置文件。

## <a name="configure-network-uplink"></a>配置网络上行

现在，在 AVS SDDC 中为网络上行配置网络配置文件更改。

1. 登录到 SDDC NSX-T 以创建新的逻辑交换机，或使用现有的逻辑交换机，该交换机可用于本地和 AVS SDDC 之间的网络上行链路。

1. 在 AVS SDDC 中创建可用于本地到 AVS SDDC 通信的 HCX 上行的网络配置文件。  
    
   ![为上行创建网络配置文件](./media/hybrid-cloud-extension-installation/network-profile-uplink.png)

1. 为网络配置文件输入名称，并基于所需的 L2 网络扩展输入至少 4-5 个可用 IP 地址。  
    
   ![为上行配置网络配置文件](./media/hybrid-cloud-extension-installation/configure-uplink-profile.png)

1. 选择“创建”以完成 AVS SDDC 配置

## <a name="configure-service-mesh"></a>配置服务网格

现在，在本地与 AVS SDDC 之间配置服务网格。

1. 登录 AVS SDDC vCenter，然后选择“HCX”。

2. 在 "**基础结构**" 下，选择 "**互连**  >  **服务网格**  >  **创建服务网格**"，以配置在前面的步骤中创建的网络和计算配置文件。    
      
    ![配置服务网格](./media/hybrid-cloud-extension-installation/configure-service-mesh.png)

3. 选择配对站点以启用混合功能，然后选择**继续**。   
    
    ![选择配对站点](./media/hybrid-cloud-extension-installation/select-paired-sites.png)

4. 选择要启用混合性 services 的源和远程计算配置文件，然后选择 "**继续**"。
      
    选择定义资源，其中 Vm 可以使用 HCX 服务。  
      
    ![启用混合性服务](./media/hybrid-cloud-extension-installation/enable-hybridity.png)

5. 选择要启用的服务，并选择 "**继续**"。  
      
    ![选择 HCX 服务](./media/hybrid-cloud-extension-installation/hcx-services.png)

6. 在“高级配置 - 替代上行网络配置文件”中，选择“继续”。  
      
    上行网络配置文件用于连接到可通过其连接到远程站点的互连设备的网络。  
      
    ![替代上行配置文件](./media/hybrid-cloud-extension-installation/override-uplink-profiles.png)

7. 选择 **"配置网络扩展设备 Scale Out**。 
      
    ![网络扩展横向扩展](./media/hybrid-cloud-extension-installation/network-extension-scale-out.png)

8. 输入与 DVS 交换机计数对应的设备计数。  
      
    ![配置设备计数](./media/hybrid-cloud-extension-installation/appliance-scale.png)

9. 选择 "**继续**" 以跳过。  
      
    ![配置流量工程](./media/hybrid-cloud-extension-installation/traffic-engineering.png)

10. 查看拓扑预览，然后选择“继续”。 

11. 为此服务网格输入用户友好名称，然后选择 "**完成**" 完成操作。  
      
    ![完成服务网格](./media/hybrid-cloud-extension-installation/complete-service-mesh.png)

    服务网格会进行部署和配置。  
      
    ![已部署的服务网格](./media/hybrid-cloud-extension-installation/deployed-service-mesh.png)

## <a name="check-appliance-status"></a>检查设备状态
若要检查设备的状态，请选择“互连” > “设备”。 
      
![设备状态](./media/hybrid-cloud-extension-installation/appliance-status.png)

## <a name="next-steps"></a>后续步骤

当设备互连“隧道状态”为“正常”并且为绿色时，便可以使用 HCX 迁移和保护 AVS VM。 请参阅 VMware 技术文档中的 [VMware HCX 文档](https://docs.vmware.com/en/VMware-HCX/index.html)和[使用 VMware HCX 迁移虚拟机](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)。
