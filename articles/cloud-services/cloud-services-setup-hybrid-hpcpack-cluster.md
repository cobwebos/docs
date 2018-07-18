---
title: 在 Azure 中设置混合 HPC Pack 群集 | Microsoft Docs
description: 了解如何使用 Microsoft HPC Pack 和 Azure 设置小型的混合高性能计算 (HPC) 群集
services: cloud-services
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: hpc-pack
ms.assetid: ''
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: danlep
ms.openlocfilehash: ad5c13723eef352148a40e3e7f4f2ff616867296
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2017
ms.locfileid: "23985300"
---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>使用 Microsoft HPC Pack 和按需 Azure 计算节点设置混合高性能计算 (HPC) 群集
使用 Microsoft HPC Pack 2012 R2 和 Azure 可以设置小型混合高性能计算 (HPC) 群集 本文中所示的群集包含一个本地 HPC Pack 头节点和一些在 Azure 云服务中按需部署的计算节点。 可以在混合群集上运行计算作业。

![混合 HPC 群集][Overview] 

本教程展示了一种方法（有时称作群集“迸发至云”）来使用可伸缩的按需 Azure 资源运行计算密集型应用程序。

本教程假定你之前未使用过计算群集或 HPC Pack。 它只是为了出于演示目的帮助你快速部署混合群集。 有关在生产环境中以更大规模部署混合 HPC Pack 群集或使用 HPC Pack 2016 的注意事项和步骤，请参阅[详细指南](http://go.microsoft.com/fwlink/p/?LinkID=200493)。 有关使用 HPC Pack 的其他方案，包括在 Azure 虚拟机中自动执行群集部署，请参阅 [Azure 中 Microsoft HPC Pack 的 HPC 群集选项](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="prerequisites"></a>先决条件
* **Azure 订阅** - 如果没有 Azure 订阅，只需几分钟就能创建一个[免费帐户](https://azure.microsoft.com/free/)。
* **运行 Windows Server 2012 R2 或 Windows Server 2012 的本地计算机** - 将该计算机用作 HPC 群集的头节点。 如果尚未运行 Windows Server，可下载并安装[评估版](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2)。
  
  * 该计算机必须加入 Active Directory 域中。 出于测试目的，可将头节点计算机配置为域控制器。 若要添加 Active Directory 域服务服务器角色并将头节点计算机提升为域控制器，请参阅 Windows Server 文档。
  * 为了支持 HPC Pack，必须采用以下语言之一安装操作系统：英语、日语或简体中文。
  * 确认安装了重要更新和关键更新。
* **HPC Pack 2012 R2** - 免费[下载](http://go.microsoft.com/fwlink/p/?linkid=328024)最新版本的安装包并将文件复制到头节点计算机。 选择语言与你所安装的 Windows Server 的语言相同的安装文件。

    >[!NOTE]
    > 如果要使用 HPC Pack 2016（而不是 HPC Pack 2012 R2），需进行其他配置。 请参阅[详细指南](http://go.microsoft.com/fwlink/p/?LinkID=200493)。
    > 
* **域帐户** - 必须为此帐户配置头节点上的本地管理员权限，才能安装 HPC Pack。
* **端口 443 上的 TCP 连接**：从头节点到 Azure。

## <a name="install-hpc-pack-on-the-head-node"></a>在头节点上安装 HPC Pack
首先将 Microsoft HPC Pack 安装到运行着 Windows Server 的本地计算机上。 此计算机便成为群集的头节点。

1. 通过使用具有本地管理员权限的域帐户登录到头节点。

2. 通过从 HPC Pack 安装文件运行 Setup.exe 启动 HPC Pack 安装向导。

3. 在“**HPC Pack 2012 R2 安装**”屏幕上，单击“**新建安装或将新功能添加到现有安装**”。

    ![HPC Pack 2012 安装][install_hpc1]

4. 在“**Microsoft 软件用户协议**”页上，单击“**下一步**”。

5. 在“**选择安装类型**”页上，单击“**通过创建头节点来创建新的 HPC 群集**”，并单击“**下一步**”。

6. 该向导会运行若干个安装前测试。 如果通过了全部测试，则在“**安装规则**”页上单击“**下一步**”。 否则，请查看提供的信息并在环境中进行必需的更改。 然后再次运行测试或者根据需要再次启动安装向导。
7. 在“**HPC DB 配置**”页上，确保对于所有 HPC 数据库都选择了“**头节点**”，并单击“**下一步**”。 

    ![数据库配置][install_hpc4]

8. 在其余向导页上接受默认选择。 在“**安装所需的组件**”页上，单击“**安装**”。
   
    ![安装][install_hpc6]

9. 在安装完毕后，取消选中“**启动 HPC 群集管理器**”，并单击“**完成**”。 （可在后面的步骤中启动 HPC 群集管理器。）
   
    ![完成][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>准备 Azure 订阅
在 [Azure 门户](https://portal.azure.com)中执行以下 Azure 订阅步骤。 完成这些步骤后，可从本地头节点部署 Azure 节点。 
  
  > [!NOTE]
  > 另外请记下 Azure 订阅 ID，后面将需要用到它。 在门户的“订阅”中查找此 ID。
  > 

### <a name="upload-the-default-management-certificate"></a>上传默认管理证书
HPC Pack 会在头节点上安装称作默认 Microsoft HPC Azure 管理证书的自签名证书，可以将此证书作为 Azure 管理证书上传。 此证书用于测试和概念证明部署，以保护头节点和 Azure 之间的连接。

1. 从头节点计算机登录到 [Azure 门户](https://portal.azure.com)。

2. 单击“订阅” > your_subscription_name。

3. 单击“管理证书” > “上传”。

4. 在头节点上浏览找到文件 C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer。 然后单击“上传”。

   
管理证书列表中将出现“默认 HPC Azure 管理”证书。

### <a name="create-an-azure-cloud-service"></a>创建 Azure 云服务
> [!NOTE]
> 为了获得最佳性能，请在同一地理区域中创建云服务和存储帐户（在后面的步骤中）。
> 
> 

1. 在门户中，单击“云服务（经典）” > “+添加”。

2.  键入服务的 DNS 名称，选择资源组和位置，然后单击“创建”。


### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户
1. 在门户中，单击“存储帐户（经典）” > “+添加”。

2. 键入该帐户的名称，然后选择“经典”部署模型。

3. 选择资源组和位置，并将其他设置保留为默认值。 然后单击“创建”。

## <a name="configure-the-head-node"></a>配置头节点
若要使用 HPC 群集管理器部署 Azure 节点和提交作业，必须首先执行一些必需的群集配置步骤。

1. 在头节点上，启动 HPC 群集管理器。 如果出现“**选择头节点**”对话框，则单击“**本地计算机**”。 这时会出现“**待执行部署任务列表**”。

2. 在“**需完成的部署任务**”下，单击“**配置网络**”。
   
    ![配置网络][config_hpc2]

3. 在网络配置向导中，选择“**仅在企业网络上的所有节点**”（拓扑 5）。 此网络配置是用于演示目的的最简单配置。
   
    ![拓扑 5][config_hpc3]
   
4. 单击“**下一步**”，接受向导其余页上的默认值。 然后，在“审阅”选项卡上，单击“配置”，完成网络配置。

5. 在“**待执行部署任务列表**”中，单击“**提供安装帐号**”。

6. 在“**安装帐号**”对话框中，键入用于安装 HPC Pack 的域帐户的凭据。 然后单击“确定”。 
   
    ![安装凭据][config_hpc6]
   
7. 在“**待执行部署任务列表**”中，单击“**配置新节点的命名规则**”。

8. 在“**指定节点命名序列**”对话框中，接受默认命名系列，并单击“**确定**”。 完成此步骤，即使已自动命名在本教程中添加的 Azure 节点。
   
    ![节点名称][config_hpc8]
   
9. 在“**待执行部署任务列表**”中，单击“**创建节点模板**”。 稍后本教程中将使用节点模板向群集添加 Azure 节点。

10. 在创建节点模板向导中，执行以下操作：
    
    a. 在“选择节点模板类型”页上，单击“Microsoft Azure 节点模板”，并单击“下一步”。
    
    ![节点模板][config_hpc10]
    
    b. 单击“**下一步**”以接受默认模板名称。
    
    c. 在“**提供订阅信息**”页上，输入 Azure 订阅 ID（在 Azure 帐户信息中提供）。 然后，在“管理证书”中，浏览“默认 Microsoft HPC Azure 管理”。 然后单击“下一步”。
    
    ![节点模板][config_hpc12]
    
    d.单击“下一步”。 在“提供服务信息”页上，选择你在前一步中创建的云服务和存储帐户。 然后单击“下一步”。
    
    ![节点模板][config_hpc13]
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 单击“**下一步**”，接受向导其余页上的默认值。 然后，在“审查”选项卡上，单击“创建”，以创建节点模板。
    
    > [!NOTE]
    > 默认情况下，Azure 节点模板包括用于通过 HPC 群集管理器手动启动（设置）和停止节点的设置。 可以选择配置计划以便自动启动和停止 Azure 节点。
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>向群集添加 Azure 节点
现在，使用节点模板向群集添加 Azure 节点。 将节点添加到群集可存储其配置信息，使你能够随时在云服务中对其进行启动（设置）。 在云服务中运行实例后，仅针对 Azure 节点向订阅收费。

请按照以下步骤添加两个小型节点。

1. 在 HPC 群集管理器中，单击“节点管理”（在当前版本的 HPC Pack 中称为“资源管理”）>“添加节点”。
   
    ![添加节点][add_node1]

2. 在“添加节点”向导的“选择部署方法”页上，单击“添加 Windows Azure 节点”，并单击“下一步”。
   
    ![添加 Azure 节点][add_node1_1]

3. 在“指定新节点”页上，选择你之前创建的 Azure 节点模板（默认称作“默认 AzureNode 模板”）。 然后指定大小为“**小**”的 **2** 个节点，并单击“**下一步**”。
   
    ![指定节点][add_node2]
   
4. 在“**完成添加节点向导**”页上，单击“**完成**”。
    
     现在，HPC 群集管理器中会出现名为“**AzureCN-0001**”和“**AzureCN-0002**”的两个 Azure 节点。 它们的状态都是“**未部署**”。
   
    ![已添加节点][add_node3]

## <a name="start-the-azure-nodes"></a>启动 Azure 节点
当希望在 Azure 中使用群集资源时，请使用 HPC 群集管理器启动（设置）Azure 节点并使它们联机。

1. 在 HPC 群集管理器中，单击“节点管理”（在当前版本的 HPC Pack 中称为“资源管理”），并选择 Azure 节点。

2. 单击“开始”，然后单击“确定”。
   
   ![启动节点][add_node4]
   
    该节点将转换到“**正在预配**”状态。 查看预配日志可跟踪预配进度。
   
    ![设置节点][add_node6]

3. 几分钟后，Azure 节点将完成预配并且处于“**脱机**”状态。 在此状态下，角色实例尽管在运行，但不会接受群集作业。

4. 要确认角色实例正在运行，在 Azure 门户中，单击“云服务（经典）” > your_cloud_service_name。
   
   应会看到服务中正在运行两个“HpcWorkerRole”实例（节点）。 HPC Pack 还会自动部署两个 **HpcProxy** 实例（中等大小），以便处理头节点和 Azure 之间的通信。

   ![运行实例][view_instances1]

5. 要使 Azure 节点联机以运行群集作业，请选择这些节点，右键单击，并单击“**联机**”。
   
    ![脱机节点][add_node7]
   
    HPC 群集管理器指示节点处于“**联机**”状态。

## <a name="run-a-command-across-the-cluster"></a>跨群集运行命令
若要检查安装，可使用 HPC Pack 的 **clusrun** 命令在一个或多个群集节点上运行命令或应用程序。 下面举一个简单的例子，介绍如何使用 **clusrun** 获取 Azure 节点的 IP 配置。

1. 在头节点上，以管理员身份打开命令提示符。

2. 输入以下命令：
   
    `clusrun /nodes:azurecn* ipconfig`

3. 如果出现提示，输入群集管理员密码。 应该会看到与下面类似的命令输出。
   
    ![Clusrun][clusrun1]

## <a name="run-a-test-job"></a>运行测试作业
现在，提交在混合群集上运行的测试作业。 此示例是一个简单的参数扫描作业（一种固有的并行计算）。 此示例将运行多个子任务，这些子任务通过使用 **set /a** 命令向其本身添加整数。 群集中的所有节点都参与完成针对 1 到 100 的整数的子任务。

1. 在 HPC 群集管理器中，单击“作业管理” > “新建参数扫描分析作业”。
   
    ![新作业][test_job1]

2. 在“**新建参数扫描作业**”对话框的“**命令行**”中，键入 `set /a *+*`（覆盖出现的默认命令行）。 对于其余设置保留使用默认值，并单击“**提交**”，提交作业。
   
    ![参数分析][param_sweep1]

3. 在该作业完成后，双击“**我的扫描任务**”作业。

4. 单击“**查看任务**”，并单击某一子任务，查看该子任务的计算输出。
   
    ![任务结果][view_job361]

5. 若要查看哪一节点执行了针对该子任务的计算，请单击“**已分配节点数**”。 （群集可能会显示一个不同的节点名称。）
   
    ![任务结果][view_job362]

## <a name="stop-the-azure-nodes"></a>停止 Azure 节点
在试用群集后，可停止 Azure 节点，以免向帐户收取不必要的费用。 此步骤将停止云服务并且删除 Azure 角色实例。

1. 在 HPC 群集管理器中的“节点管理”（在以前版本的 HPC Pack 中称为“资源管理”）中，选择这两个 Azure 节点。 然后单击“停止”。
   
    ![停止节点][stop_node1]

2. 在“停止 Windows Azure 节点”对话框中，单击“停止”。 
   
3. 节点将转换到“**正在停止**”状态。 几分钟后，HPC 群集管理器会显示节点处于“**未部署**”状态。
   
    ![未部署节点][stop_node4]

4. 要确认角色实例不再在 Azure 中运行，请在门户中单击“云服务（经典）” > your_cloud_service_name。 将不会有任何实例部署在生产环境中。 
   
    本教程至此完毕。

## <a name="next-steps"></a>后续步骤
* 浏览 [HPC Pack](https://technet.microsoft.com/library/cc514029) 的文档。
* 若要以更大的规模设置混合 HPC Pack 群集部署，请参阅[使用 Microsoft HPC Pack 迸发到 Azure 辅助角色实例](http://go.microsoft.com/fwlink/p/?LinkID=200493)。
* 有关在 Azure 中创建 HPC Pack 群集的其他方法，包括使用 Azure 资源管理器模板，请参阅[在 Azure 中使用 Microsoft HPC Pack 时的 HPC 群集选项](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png
