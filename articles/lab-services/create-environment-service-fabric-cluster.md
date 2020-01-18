---
title: 在 Azure 开发测试实验室中创建 Service Fabric 群集环境
description: 了解如何创建具有自包含 Service Fabric 群集的环境，以及如何使用计划启动和停止群集。
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
ms.date: 01/16/2020
ms.author: enewman
ms.openlocfilehash: 71793b81d8735c80881fc25a9b7ec31bc4fc6762
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170339"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>使用 Azure 开发测试实验室中的独立 Service Fabric 群集创建环境
本文提供了有关如何使用 Azure 开发测试实验室中的自包含 Service Fabric 群集创建环境的信息。 

## <a name="overview"></a>概述
开发测试实验室可以根据 Azure 资源管理模板的定义创建自包含的测试环境。 这些环境包含 IaaS 资源，如虚拟机和 PaaS 资源，如 Service Fabric。 开发测试实验室允许您通过提供控制虚拟机的命令来管理环境中的虚拟机。 这些命令使你能够按计划启动或停止虚拟机。 同样，开发测试实验室还可以帮助你管理环境中 Service Fabric 群集。 您可以手动或通过计划在环境中启动或停止 Service Fabric 群集。

## <a name="create-a-service-fabric-cluster"></a>创建 Service Fabric 群集
Service Fabric 群集是使用开发测试实验室中的环境创建的。 每个环境都是通过 Git 存储库中的 Azure 资源管理器模板来定义的。 用于开发测试实验室的[公共 Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Environments/)包含用于在[ServiceFabric](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)文件夹中创建 Service Fabric 群集的资源管理器模板。 

1. 首先，使用以下文章中的说明在 Azure 开发测试实验室中创建实验室：[创建实验室](devtest-lab-create-lab.md)。 请注意，默认情况下 "**公共环境**" 选项处于**启用**状态。 
2. 通过执行以下步骤，确认是否已为你的订阅注册 Service Fabric 提供程序：
    1. 选择左侧导航菜单中的 "**订阅**"，然后选择**订阅**
    2. 在 "**订阅**" 页上，在左侧菜单的 "**设置**" 部分中选择 "**资源提供程序**"。 
    3. 如果**ServiecFabric**未注册，请选择 "**注册**"。 
3. 在实验室的“开发测试实验室”页上，选择工具栏中的“+ 添加”。 
    
    ![工具栏上的 "添加" 按钮](./media/create-environment-service-fabric-cluster/add-button.png)
3. 在 "**选择基本**页" 列表中，选择 " **Service Fabric 实验室群集**"。 

    ![在列表中选择 Service Fabric 实验室群集](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. 在 "**配置设置**" 页上，执行以下步骤： 
    1. 指定群集**环境**的**名称**。 这是要在其中创建 Service Fabric 群集的 Azure 中资源组的名称。 
    2. 选择群集虚拟机的**操作系统（OS）** 。 默认值为： **Windows**。
    3. 指定群集**管理员**的名称。 
    4. 指定管理员的**密码**。 
    5. 对于**证书**，请将证书信息输入为 Base64 编码的字符串。 若要创建证书，请执行以下步骤：
        1. 从[Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)下载**Create-ClusterCertificate**文件。 或者，将存储库克隆到计算机上。 
        2. 启动 **PowerShell**。 
        3. 使用命令 `.\Create-ClusterCertificate.ps1`运行**ps1**文件。 你将看到在 "记事本" 中打开的文本文件，其中包含需要在此页上填写证书相关字段的信息。 。 
    6. 输入**证书的密码**。
    7. 指定证书的**指纹**。
    8. 选择 "**配置设置**" 页上的 "**添加**"。 

        ![配置群集设置](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. 创建群集后，会看到一个资源组，其中包含上一步骤中提供的环境的名称。 展开后，会看到其中的 Service Fabric 群集。 如果在**创建**时停止了资源组的状态，请在工具栏上选择 "**刷新**"。 **Service Fabric 群集**环境在 Linux 或 Windows 上创建一个5节点的1节点群集。

    在以下示例中， **mysfabricclusterrg**是专门为 Service Fabric 群集创建的资源组的名称。 需要特别注意的是，实验室环境是自包含在其中创建它们的资源组中的。 这意味着定义环境的模板，它只能访问[配置为由实验室使用](devtest-lab-configure-vnet.md)的新创建的资源组或虚拟网络中的资源。 上面的示例创建了同一资源组中所需的所有资源。

    ![已创建群集](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>启动或停止群集
可以从开发测试 Lab 页面本身或开发测试实验室提供的 Service Fabric 群集页启动或停止群集。 

### <a name="from-the-devtest-lab-page"></a>从 "开发测试实验室" 页
你可以在实验室的开发测试实验室页上启动或停止群集。 

1. 为 Service Fabric 群集选择**三个点（...）** ，如下图所示： 

    ![群集的启动和停止命令](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. 在上下文菜单中可以看到两个命令来**启动**和**停止**群集。 Start 命令启动群集中的所有节点。 Stop 命令停止群集中的所有节点。 群集停止后，Service Fabric 群集本身仍处于 "就绪" 状态，但在实验室中的群集上重新颁发了 "启动" 命令后，才能使用节点。

    在测试环境中使用 Service Fabric 群集时，需要注意几个事项。 在节点重新启动后，Service Fabric 群集可能需要一段时间来完全解除冻结。 若要将数据从关闭中保留到启动，必须将数据保存在附加到虚拟机的托管磁盘上。 使用附加的托管磁盘时有性能影响，因此建议仅用于测试环境。 如果不支持用于数据存储的磁盘，则在群集上发出 stop 命令后，数据将丢失。

### <a name="from-the-service-fabric-cluster-page"></a>从 "Service Fabric 群集" 页 
还可以通过另一种方式启动或停止群集。 

1. 在开发测试 Lab 页面上的树视图中选择 Service Fabric 群集。 

    ![选择群集](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. 在群集的 " **Service Fabric 群集**" 页上，可以看到工具栏上的命令来启动或停止群集。 

    ![在 Service Fabric 群集页中启动或停止命令](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>配置自动启动和自动关闭计划
Service Fabric 群集也可以按计划启动或停止。 此体验类似于实验室中虚拟机的体验。 为了节省资金，默认情况下，在实验室中创建的每个群集会在实验室[关闭策略](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)定义的时间关闭。 可以通过指定是否应关闭群集或指定群集关闭的时间来覆盖。 

![自动启动和自动关闭的现有计划](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>选择启用自动启动计划
若要选择启动计划，请执行以下步骤：

1. 在左侧菜单中选择 "**自动启动**"
2. 对于 "**允许将此 service fabric 群集计划为自动启动**"，选择 **"启用"** 。 仅当实验室所有者允许用户自动启动其虚拟机或 Service Fabric 群集时，才会启用此页。
3. 在工具栏上选择“保存”。 

    ![自动星形页面](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>配置自动关闭设置 
若要更改关闭设置，请执行以下步骤：

1. 选择左侧菜单上的 "**自动关闭**"。 
2. 在此页上，可以选择 "**关闭**" 以**启用**自动关闭。 
3. 如果已在启用**上选择 "** **启用**"，请执行以下步骤：
    1. 指定关闭的**时间**。
    2. 指定时间的**时区**。 
    3. 指定是否希望开发测试实验室在自动关闭前发送**通知**。 
    4. 如果为 "通知" 选项选择 **"是"** ，请指定用于发送通知的**Webhook URL**和/或**电子邮件地址**。 
    5. 在工具栏上选择“保存”。

        ![自动关闭页面](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>查看 Service Fabric 群集中的节点
您在前面的步骤中见到的 Service Fabric 群集页特定于开发测试实验室页。 它不会显示群集中的节点。 若要查看有关群集的详细信息，请执行以下步骤：

1. 在实验室的 "**开发测试实验室**" 页上，在 "**我的虚拟机**" 部分中的树视图中选择**资源组**。

    ![选择资源组](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. 在 "**资源组**" 页上，可以在列表中看到资源组中的所有资源。 从列表中选择**Service Fabric 群集**。 

    ![在列表中选择群集](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. 你会看到群集的 " **Service Fabric 群集**" 页。 这是 Service Fabric 提供的页面。 你将看到有关群集的所有信息，如节点、节点类型等。

    ![Service Fabric 群集主页](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>后续步骤
有关环境的详细信息，请参阅以下文章： 

- [使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [在 Azure 开发测试实验室中配置和使用公共环境](devtest-lab-configure-use-public-environments.md)
