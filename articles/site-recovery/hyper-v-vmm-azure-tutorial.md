---
title: 使用 Azure Site Recovery 设置 Hyper-V（使用 VMM）灾难恢复
description: 了解如何使用 Site Recovery 设置 System Center VMM 云中的本地 Hyper-V VM 到 Azure 的灾难恢复。
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: a391d8eb3cf7bc43b52883cbf2e76170338c44c6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067581"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>设置 VMM 云中的本地 Hyper-V VM 到 Azure 的灾难恢复

本教程介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 为 System Center Virtual Machine Manager (VMM) 管理的本地 Hyper-V 虚拟机 (VM) 启用到 Azure 的复制。 如果未使用 VMM，则[按照此教程](hyper-v-azure-tutorial.md)进行操作。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 选择复制源和目标。
> * 设置源复制坏境，包括本地 Site Recovery 组件和目标复制环境。
> * 在 VMM VM 网络与 Azure 虚拟网络之间设置网络映射。
> * 创建复制策略。
> * 为 VM 启用复制。

> [!NOTE]
> 教程介绍了某个方案的最简单部署路径。 它们尽可能使用默认选项，并且不显示所有可能的设置和路径。 有关详细说明，请查看 [Site Recovery 文档](/azure/site-recovery/)的“操作指南”部分所列的文章  。

## <a name="prerequisites"></a>先决条件

本教程假设你已完成以下教程：

1. [准备 Azure](tutorial-prepare-azure.md)
1. [准备本地 Hyper-V 服务器](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>选择复制目标

1. 在 Azure 门户中转到“恢复服务保管库”，选择在[准备 Azure](tutorial-prepare-azure.md#create-a-recovery-services-vault) 教程中创建的 **ContosoVMVault** 保管库。 
1. 从“开始”中，选择“Site Recovery” > “准备基础结构”并配置以下设置：   
    1. 对于“保护目标” > “计算机位于何处?”，请选择“本地”。   
    1. 对于“要将计算机复制到何处?”，请选择“复制到 Azure”。  
    1. 对于“计算机是否已虚拟化?”，请选择“是，带有 Hyper-V”。  
    1. 对于“是否使用 System Center VMM 管理 Hyper-V 主机?”，请选择“是”。  
1. 选择“确定”  。

   ![复制目标](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>确认部署规划

1. 在“部署规划”中，若要规划大型部署，请通过页面上的链接下载适用于 Hyper-V 的部署规划器。  [详细了解](hyper-v-deployment-planner-overview.md) Hyper-V 部署规划。
1. 本教程不需要使用部署规划器。 在“是否已完成部署规划?”  中选择“我将稍后进行”  ，然后选择“确定”  。

## <a name="set-up-the-source-environment"></a>设置源环境

设置源环境时，将在 VMM 服务器上安装 Azure Site Recovery 提供程序，并在保管库中注册该服务器。 在每台 Hyper-V 主机上安装 Azure 恢复服务代理。

1. **准备基础结构**。 选择“源”。 
1. **准备源**。 选择“+ VMM”以添加 VMM 服务器。  在“添加服务器”中，检查“服务器类型”中是否已显示“System Center VMM 服务器”    。
1. 下载 Microsoft Azure Site Recovery 提供程序的安装程序。
1. 下载保管库注册密钥。 运行提供程序安装程序时需要此密钥。 生成的密钥有效期为 5 天。
1. 下载 Microsoft Azure 恢复服务代理的安装程序。

   ![下载提供程序、注册密钥和代理](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>安装 VMM 服务器上的提供程序

1. 在 Azure Site Recovery 提供程序安装向导中，选择“Microsoft 更新”。  选择使用“Microsoft 更新”来检查提供程序更新。
1. **安装**。 接受提供程序的默认安装位置，然后选择“安装”。 
1. 安装后，在 Microsoft Azure Site Recovery 注册向导中选择“保管库设置”>“浏览”，然后在“密钥文件”中选择下载的保管库密钥文件。   
1. 指定 Azure Site Recovery 订阅和保管库名称 (ContosoVMVault  )。 为 VMM 服务器指定友好名称以在保管库中标识此 VMM。
1. **代理设置**。 选择“在不使用代理的情况下直接连接到 Azure Site Recovery”。 
1. 接受用于加密数据的证书的默认位置。 故障转移时，会解密已加密的数据。
1. **同步云元数据**。 选择“将云元数据同步到 Site Recovery 门户”。  在每台服务器上只需执行此操作一次。 然后，选择“注册”  。
1. 服务器在保管库中注册后，选择“完成”。 

完成注册后，Azure Site Recovery 将检索服务器中的元数据，该 VMM 服务器显示在“Site Recovery 基础结构”中。 

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>在 Hyper-V 主机上安装恢复服务代理

在包含要复制的 VM 的每台 Hyper-V 主机上安装代理。

在 Microsoft Azure 恢复服务代理安装程序向导中配置以下设置：

1. **先决条件检查**。 选择“**下一页**”。 系统会自动安装任何缺少的必备组件。
1. **安装设置**。 接受安装位置和缓存位置。 缓存驱动器需要至少 5 GB 的存储空间。 我们建议使用 600 GB 或更多可用空间的驱动器。 然后，选择“安装”  。
1. **安装**。 安装完成后，选择“关闭”以完成向导。 

   ![安装代理](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>设置目标环境

1. 选择“准备基础结构” > “目标”。  
1. 选择在故障转移后要在其中创建 Azure VM 的订阅和资源组 (ContosoRG  )。
1. 选择“资源管理器”部署模型。 

Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。

## <a name="configure-network-mapping"></a>配置网络映射

1. **Site Recovery 基础结构** > **网络映射** > **网络映射**。 选择“+网络映射”图标。 
1. **添加网络映射**。 选择“源 System Center VMM”服务器。  对于“区域”，请选择“Azure”。 
1. 在故障转移后，验证订阅和部署模型。
1. **源网络**。 选择源本地 VM 网络。
1. **目标网络**。 选择在故障转移后创建副本 Azure VM 时，这些 VM 所在的 Azure 网络。 选择“确定”。 

   ![网络映射](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>设置复制策略

1. 选择“准备基础结构”   > “复制设置”   > “+创建和关联”  。
1. 在“创建和关联策略”  中指定策略名称。 我们将使用 **ContosoReplicationPolicy**。
1. 接受默认设置，然后选择“确定”。 
   - “复制频率”指示在完成初始复制后，将每隔五分钟复制一次增量数据。 
   - “恢复点保留期”指示每个恢复点将保留两小时。 
   - **应用一致性快照频率**指示每小时创建一次包含应用一致性快照的恢复点。
   - “初始复制开始时间”指示初始复制会立即开始  。
   - “加密 Azure 上存储的数据”设置为默认值（“关闭”），指示不会加密 Azure 中的静态数据。  
1. 创建策略后，选择“确定”  。 创建新策略时，该策略将自动关联到 VMM 云。

## <a name="enable-replication"></a>启用复制

1. **复制应用程序**。 选择“源”。 
1. **源**。 选择 VMM 云。 选择“确定”。 
1. **目标**。 验证目标 (Azure) 和保管库订阅，然后选择“资源管理器”模型。 
1. 选择“contosovmsacct1910171607”存储帐户和“ContosoASRnet”Azure 网络。  
1. **虚拟机** > **选择**。 选择要复制的 VM。 选择“确定”。 

   可以在“作业”   > “Site Recovery 作业”  中，跟踪“启用保护”  操作的进度。 “最后完成保护”作业完成后，初始复制即已完成，VM 可执行故障转移  。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [运行灾难恢复演练](tutorial-dr-drill-azure.md)
