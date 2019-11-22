---
title: 使用 Azure Site Recovery 设置 Hyper-V 灾难恢复
description: 了解如何使用 Site Recovery 为本地 Hyper-V VM（不包含 VMM）设置到 Azure 的灾难恢复。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 741d4718b5e6140f4ddd2bb22e1a2ec830763176
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74029946"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>对 Azure 设置本地 Hyper-V VM 的灾难恢复

[Azure Site Recovery](site-recovery-overview.md) 服务可管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复，因而有利于灾难恢复策略。

此教程为系列教程中的第三个教程。 本教程演示如何设置本地 Hyper-V VM 到 Azure 的灾难恢复。 本教程适用于不由 Microsoft System Center Virtual Machine Manager (VMM) 托管的 Hyper-V VM。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 选择复制源和目标。
> * 设置源复制坏境，包括本地 Site Recovery 组件和目标复制环境。
> * 创建复制策略。
> * 为 VM 启用复制。

> [!NOTE]
> 教程中介绍了某个方案的最简单部署路径。 它们尽可能使用默认选项，并且不显示所有可能的设置和路径。 有关详细说明，请查看 [Site Recovery 文档](https://docs.microsoft.com/azure/site-recovery)的“操作指南”部分所列的文章  。



## <a name="before-you-begin"></a>开始之前

此教程为系列教程中的第三个教程。 本教程假设你已完成前面教程中的任务：

1. [准备 Azure](tutorial-prepare-azure.md)
2. [准备本地 Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>选择复制目标

1. 在 Azure 门户中，转到“恢复服务保管库”，然后选择保管库  。 在前面的教程中，我们已准备好保管库 **ContosoVMVault**。
2. 在“入门”中选择“Site Recovery”，然后选择“准备基础结构”    。
3. 在“保护目标” > “计算机位于何处?”中，选择“本地”    。
4. 在“要将计算机复制到何处?”中，选择“复制到 Azure”   。
5. 在“计算机是否已虚拟化?”中，选择“是，带有 Hyper-V”   。
6. 在“是否要使用 System Center VMM 管理 Hyper-V 主机?”  中，选择“否”  。
7. 选择“确定”  。

    ![复制目标](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>确认部署规划

1. 在“部署规划”中，若要规划大型部署，请通过页面上的链接下载适用于 Hyper-V 的部署规划器。  [详细了解](hyper-v-deployment-planner-overview.md) Hyper-V 部署规划。
2. 本教程不需要使用部署规划器。 在“是否已完成部署规划?”  中选择“我将稍后进行”  ，然后选择“确定”  。

    ![部署规划](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>设置源环境

若要设置源环境，请创建 Hyper-V 站点，并将包含所要复制的 VM 的 Hyper-V 主机添加到该站点。 然后，在每个主机上下载并安装 Azure Site Recovery 提供程序和 Azure 恢复服务代理，并将 Hyper-V 站点注册到保管库中。

1. 在“准备基础结构”  下，选择“源”  。
2. 在“准备源”中，选择“+ Hyper-V 站点”   。
3. 在“创建 Hyper-V 站点”中，指定站点名称  。 我们将使用 **ContosoHyperVSite**。

    ![Hyper-V 站点](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. 创建站点后，在“准备源” > “步骤 1:   选择 Hyper-V 站点”中，选择创建的站点。
5. 选择“+ Hyper-V 服务器”  。

    ![Hyper-V Server](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. 下载 Microsoft Azure Site Recovery 提供程序的安装程序。
7. 下载保管库注册密钥。 需要使用此密钥来安装提供程序。 生成的密钥有效期为 5 天。

    ![下载提供程序和注册密钥](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>安装提供程序

将下载的安装程序文件 (AzureSiteRecoveryProvider.exe) 安装在要添加到 Hyper-V 站点的每个 Hyper-V 主机上。 安装程序在每个 Hyper-V 主机上安装 Azure Site Recovery 提供程序和 Recovery Services 代理。

1. 运行安装程序文件。
2. 在“Azure Site Recovery 提供程序安装程序向导”>“Microsoft 更新”中，选择使用 Microsoft 更新检查提供程序更新  。
3. 在“安装”中接受提供程序和代理的默认安装位置，并选择“安装”   。
4. 安装后，在“Microsoft Azure Site Recovery 向导”>“保管库设置”中，选择“浏览”，然后在“密钥文件”中选择下载的保管库密钥文件    。
5. 指定 Azure Site Recovery 订阅、保管库名称 (ContosoVMVault) 和 Hyper-V 服务器所属的 Hyper-V 站点 (ContosoHyperVSite)   。
6. 在“代理设置”中，选择“在不使用代理的情况下直接连接到 Azure Site Recovery”   。
7. 在保管库中注册服务器后，在“注册”中选择“完成”   。

Azure Site Recovery 将检索 Hyper-V 服务器中的元数据，该服务器显示在“Site Recovery 基础结构”   > “Hyper-V 主机”  中。 此过程最多可能需要 30 分钟。

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>在 Hyper-V 核心服务器上安装提供程序

如果运行的是 Hyper-V 核心服务器，请下载安装程序文件并执行以下操作：

1. 运行以下命令，将 AzureSiteRecoveryProvider.exe 中的文件提取到本地目录：

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. 运行 `.\setupdr.exe /i`。 结果将记录到 %Programdata%\ASRLogs\DRASetupWizard.log。

3. 运行此命令注册服务器：

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>设置目标环境

选择并验证目标资源：

1. 选择“准备基础结构” > “目标”。  
2. 选择在故障转移后要在其中创建 Azure VM 的订阅和资源组“ContosoRG”  。
3. 选择“资源管理器”部署模型  。

Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。

## <a name="set-up-a-replication-policy"></a>设置复制策略

1. 选择“准备基础结构”   > “复制设置”   > “+创建和关联”  。
2. 在“创建和关联策略”  中指定策略名称。 我们将使用 **ContosoReplicationPolicy**。
3. 本教程将保留默认设置：
    - “复制频率”指示（完成初始复制后）复制增量数据的频率  。 默认频率为五分钟一次。
    - “恢复点保留期”指示恢复点将保留两小时。 
    - **应用一致性快照频率**指示每小时创建一次包含应用一致性快照的恢复点。
    - “初始复制开始时间”指示初始复制会立即开始  。
4. 创建策略后，选择“确定”  。 当创建新策略时，该策略自动与指定的 Hyper-V 站点关联。 在本教程中为 ContosoHyperVSite  。

    ![复制策略](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>启用复制

1. 在“复制应用程序”中，选择“源”   。
2. 在“源”中，选择“ContosoHyperVSite”站点   。 选择“确定”。 
3. 在“目标”中验证目标 (Azure)，并验证保管库订阅，然后选择“资源管理器”部署模型   。
4. 如果使用教程中的设置，请选择在上一教程中为复制数据创建的 contosovmsacct1910171607 存储帐户  。 并选择故障转移后 Azure VM 所在的 ContosoASRnet 网络  。
5. 在“虚拟机” > “选择”中，选择要复制的 VM   。 选择“确定”。 

   可以在“作业”   > “Site Recovery 作业”  中，跟踪“启用保护”  操作的进度。 “最后完成保护”作业完成后，初始复制即已完成，VM 可执行故障转移  。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [运行灾难恢复演练](tutorial-dr-drill-azure.md)
