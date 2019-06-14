---
title: 使用 Service Fabric 群集在 Azure 开发测试实验室中创建环境 |Microsoft Docs
description: 了解如何使用独立的 Service Fabric 群集，创建环境以及开始和停止使用计划的群集。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 9848f197800c391285c4065685b910685f0ac64b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312021"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>使用 Azure 开发测试实验室中的独立 Service Fabric 群集创建环境
本文介绍如何使用 Azure 开发测试实验室中的独立 Service Fabric 群集中创建一个环境。 

## <a name="overview"></a>概述
开发测试实验室可以创建自包含的测试环境由 Azure 资源管理模板定义。 这些环境包含这两个 IaaS 资源，如虚拟机和 PaaS 资源，Service Fabric 等。 开发测试实验室，可通过提供命令来控制虚拟机管理在环境中的虚拟机。 这些命令将为您提供启动或停止虚拟机上计划的功能。 同样，开发测试实验室还有助于你管理的环境中的 Service Fabric 群集。 可以启动或停止在环境中的 Service Fabric 群集手动或通过计划。

## <a name="create-a-service-fabric-cluster"></a>创建 Service Fabric 群集
在开发测试实验室中使用环境创建 Service Fabric 群集。 每个环境由在 Git 存储库中的 Azure 资源管理器模板定义。 [公共 Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Environments/)开发测试实验室包含要创建 Service Fabric 群集中的资源管理器模板[ServiceFabric 群集](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)文件夹。 

1. 首先，实验室中 Azure 开发测试实验室创建以下文章中使用的说明：[创建实验室](devtest-lab-create-lab.md)。 请注意，**公共环境**选项是**上**默认情况下。 
2. 确认 Service Fabric 提供程序针对你的订阅注册，通过执行以下步骤：
    1. 选择**订阅**左侧导航菜单中，然后选择你**订阅**
    2. 上**订阅**页上，选择**资源提供程序**中**设置**在左侧菜单中的部分。 
    3. 如果**Microsoft.ServiecFabric**不是已注册，选择**注册**。 
3. 在实验室的“开发测试实验室”页上，选择工具栏中的“+ 添加”   。 
    
    ![在工具栏上添加按钮](./media/create-environment-service-fabric-cluster/add-button.png)
3. 上**选择基本**页上，选择**Service Fabric 实验室群集**列表中。 

    ![在列表中选择 Service Fabric 实验室群集](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. 上**配置设置**页上，执行以下步骤： 
    1. 指定**名称**群集**环境**。 这是在 Azure Service Fabric 群集要在其中创建资源组的名称。 
    2. 选择**操作系统 (OS)** 为群集虚拟机。 默认值为：**Windows**。
    3. 指定的名称**管理员**群集。 
    4. 指定**密码**管理员。 
    5. 有关**证书**，输入你的证书信息作为 Base64 编码的字符串。 若要创建的证书，请执行以下步骤：
        1. 下载**创建 ClusterCertificate.ps1**文件从[Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)。 或者，克隆您的计算机上的存储库。 
        2. 启动 **PowerShell**。 
        3. 运行**ps1**文件中使用该命令`.\Create-ClusterCertificate.ps1`。 请参阅在您需要填写此页上的证书相关字段的信息与记事本中打开一个文本文件。 . 
    6. 输入**证书密码**。
    7. 指定**指纹**证书。
    8. 选择**外**上**配置设置**页。 

        ![配置群集设置](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. 创建群集后，您将看到一个包含名称的上一步中提供的环境的资源组。 展开时，您会看到它在 Service Fabric 群集。 如果资源组的状态一直显示在**Creating**，选择**刷新**工具栏上。 **Service Fabric 群集**环境在 Linux 或 Windows 上创建一个 5 节点 1 nodetype 群集。

    在以下示例中， **mysfabricclusterrg**是专门为 Service Fabric 群集创建的资源组的名称。 请务必注意实验室环境是自包含在其中创建资源组中。 它表示定义的环境，仅可以访问新创建的资源组中的资源的模板或[配置为使用实验室的虚拟网络](devtest-lab-configure-vnet.md)。 上述此示例相同的资源组中创建所需的所有资源。

    ![创建群集](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>启动或停止群集
可以启动或停止群集从任一开发测试实验室页面本身或开发测试实验室提供的 Service Fabric 群集页。 

### <a name="from-the-devtest-lab-page"></a>从开发测试实验室页
可以启动或停止群集上开发测试实验室页为你的实验室。 

1. 选择**三个点 （...）** Service Fabric 群集，在下图中所示： 

    ![启动和停止的群集的命令](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. 请参阅到上下文菜单中的两个命令**启动**并**停止**群集。 开始命令启动群集中的所有节点。 停止命令停止群集中的所有节点。 群集停止后，Service Fabric 群集本身仍处于就绪状态，但没有节点都可用，直到开始命令在实验室中的群集上重新颁发。

    有在测试环境中使用 Service Fabric 群集时，请注意以下几点注意事项。 可能需要一些时间让 Service Fabric 群集完全解除冻结后重新启动节点。 若要保留启动到关闭的数据，必须在托管磁盘附加到虚拟机上保存数据。 使用已连接的托管的磁盘，因此建议为仅限测试环境时有性能产生影响。 如果磁盘用于数据存储不提供支持，然后数据将丢失在群集上发出停止命令时。

### <a name="from-the-service-fabric-cluster-page"></a>从 Service Fabric 群集页 
还有一种方法来启动或停止群集。 

1. 在开发测试实验室页在树视图中选择 Service Fabric 群集。 

    ![选择你的群集](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. 上**Service Fabric 群集**页工具栏启动或停止群集上群集，看到命令。 

    ![启动或停止 Service Fabric 群集页面中的命令](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>配置自动启动和自动关闭计划
Service Fabric 群集还可以启动或停止按计划。 这种体验是类似于在实验室中的虚拟机的体验。 若要节省开支、 默认情况下，在实验室中自动创建每个群集关闭时定义的实验室[关闭策略](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown)。 您可以重写通过指定群集是否应该关闭或通过指定群集已关闭的时间。 

![现有计划自动启动，并且自动关闭](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>选择启用自动启动计划
可以选择将启动计划，请执行以下步骤操作：

1. 选择**自动启动**在左侧菜单中
2. 选择**上**有关**允许安排为自动启动此 service fabric 群集**。 实验室所有者具有允许用户自动启动其虚拟机或 Service Fabric 群集时，才启用此页。
3. 在工具栏上选择“保存”。  

    ![自动星型页](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>配置自动关闭设置 
若要更改设置关闭，请执行以下步骤：

1. 选择**自动关闭**在左侧菜单中。 
2. 在此页上，可以通过选择取消自动关闭**Off**有关**已启用**。 
3. 如果选择了**上**有关**已启用**，请执行以下步骤：
    1. 指定**时间**关闭。
    2. 指定**时区**次。 
    3. 指定是否要发送的开发测试实验室**通知**之前自动关闭。 
    4. 如果所选**是**通知选项时，指定**Webhook URL**和/或**电子邮件地址**发送通知。 
    5. 在工具栏上选择“保存”。 

        ![自动关闭的情况下页](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>若要查看 Service Fabric 群集中的节点
前面步骤中所看到的 Service Fabric 群集页是特定于开发测试实验室页。 它不会显示的节点在群集中。 若要查看有关群集的详细信息，请执行以下步骤：

1. 上**开发测试实验室**页选择在实验室**资源组**中的树视图中**我的虚拟机**部分。

    ![选择资源组](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. 上**资源组**页上，请参阅在列表中的资源组中的所有资源。 选择你**Service Fabric 群集**从列表中。 

    ![在列表中选择你的群集](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. 您看到**Service Fabric 群集**群集的页。 这是 Service Fabric 提供的页面。 请参阅有关群集节点、 节点类型等的所有信息。

    ![Service Fabric 群集主页](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解有关环境的详细信息： 

- [使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [配置和在 Azure 开发测试实验室中使用公共环境](devtest-lab-configure-use-public-environments.md)
