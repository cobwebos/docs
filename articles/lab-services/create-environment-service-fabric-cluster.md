---
title: 在 Azure 开发人员测试实验室中创建服务结构群集环境
description: 了解如何使用自包含的服务结构群集创建环境，并使用计划启动和停止群集。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170339"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>在 Azure 开发人员测试实验室中使用自包含的服务结构群集创建环境
本文提供有关如何在 Azure DevTest 实验室中使用自包含的服务结构群集创建环境的信息。 

## <a name="overview"></a>概述
DevTest 实验室可以创建由 Azure 资源管理模板定义的自包含测试环境。 这些环境包含 IaaS 资源（如虚拟机）和 PaaS 资源（如 Service Fabric）。 DevTest Labs 允许您通过提供控制虚拟机的命令来管理环境中的虚拟机。 这些命令使您能够按计划启动或停止虚拟机。 同样，DevTest Labs 还可以帮助您在环境中管理服务结构群集。 您可以手动或通过计划在环境中启动或停止 Service Fabric 群集。

## <a name="create-a-service-fabric-cluster"></a>创建 Service Fabric 群集
使用 DevTest 实验室中的环境创建服务交换矩阵群集。 每个环境都由 Git 存储库中的 Azure 资源管理器模板定义。 开发人员测试实验室[的公共 Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Environments/)包含资源管理器模板，用于在[ServiceFabric 群集](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)文件夹中创建服务结构群集。 

1. 首先，使用以下文章中的说明在 Azure 开发人员测试实验室中[创建实验室：创建实验室](devtest-lab-create-lab.md)。 请注意，默认情况下 **，"公共环境**"选项处于**打开状态**。 
2. 按照以下步骤确认服务交换矩阵提供程序已注册您的订阅：
    1. 选择左侧导航菜单上的**订阅**，然后选择 **"订阅"**
    2. 在 **"订阅"** 页上，在左侧菜单的 **"设置"** 部分中选择 **"资源提供程序**"。 
    3. 如果**Microsoft.ServiecFabric**未注册，请选择"**注册**"。 
3. 在实验室的“开发测试实验室”页上，选择工具栏中的“+ 添加”********。 
    
    ![工具栏上的“添加”按钮](./media/create-environment-service-fabric-cluster/add-button.png)
3. 在 **"选择基本**"页上，在列表中选择 **"服务结构实验室群集**"。 

    ![在列表中选择服务结构实验室群集](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. 在 **"配置设置"** 页上，执行以下步骤： 
    1. 为群集**环境**指定**名称**。 这是要在 Azure 中创建服务结构群集的资源组的名称。 
    2. 选择群集虚拟机的**操作系统 （OS）。** 默认值为： **Windows**。
    3. 为群集的**管理员**指定名称。 
    4. 为管理员指定**密码**。 
    5. 对于**证书**，将证书信息输入为 Base64 编码字符串。 要创建证书，请执行以下步骤：
        1. 从[Git 存储库](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)下载**创建群集证书.ps1**文件。 或者，在计算机上克隆存储库。 
        2. 启动“PowerShell”****。 
        3. 使用 命令`.\Create-ClusterCertificate.ps1`运行**ps1**文件。 您会看到一个在记事本中打开的文本文件，其中包含填写此页面上与证书相关的字段所需的信息。 . 
    6. 输入**证书的密码**。
    7. 指定证书的**指纹**。
    8. 在 **"配置设置"** 页上选择 **"添加**"。 

        ![配置群集设置](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. 创建群集后，您将看到一个资源组，其名称为上一步骤中提供的环境。 展开时，可以看到其中的服务结构群集。 如果资源组的状态停留在 **"创建"** 中，则选择工具栏上的 **"刷新**"。 **Service Fabric 群集**环境在 Linux 或 Windows 上创建 5 节点 1 节点类型群集。

    在下面的示例中 **，mysfabricClusterrg**是专门为服务结构群集创建的资源组的名称。 请务必注意，实验室环境在创建它们的资源组中是独立的。 这意味着定义环境的模板，该模板只能访问新创建的资源组或[配置为由实验室使用的虚拟网络](devtest-lab-configure-vnet.md)中的资源。 上面的此示例将在同一资源组中创建所有必需的资源。

    ![群集已创建](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>启动或停止群集
可以从 DevTest 实验室页面本身或 DevTest Labs 提供的服务交换矩阵群集页面启动或停止群集。 

### <a name="from-the-devtest-lab-page"></a>从开发人员测试实验室页面
您可以在实验室的 DevTest 实验室页面上启动或停止群集。 

1. 为服务结构群集选择**三个点 （...），** 如下图所示： 

    ![启动和停止群集的命令](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. 在上下文菜单中可以看到两个命令来**启动**和**停止**群集。 Start 命令启动群集中的所有节点。 停止命令停止群集中的所有节点。 群集停止后，Service Fabric 群集本身将保持就绪状态，但在实验室中的群集上重新发出 Start 命令之前，没有节点可用。

    在测试环境中使用 Service Fabric 群集时，需要注意一些注意事项。 重新启动节点后，服务结构群集可能需要一些时间才能完全重新补充。 要将数据从关机保留到启动，数据必须保存在连接到虚拟机的托管磁盘上。 使用附加的托管磁盘时会产生性能影响，因此建议仅针对测试环境。 如果未备份用于数据存储的磁盘，则在群集上发出停止命令时，数据将丢失。

### <a name="from-the-service-fabric-cluster-page"></a>从"服务结构群集"页 
还有另一种方法来启动或停止群集。 

1. 在 DevTest 实验室页面上的树视图中选择您的服务结构群集。 

    ![选择群集](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. 在群集的服务**结构群集**页上，可以看到工具栏上用于启动或停止群集的命令。 

    ![在"服务结构群集"页中启动或停止命令](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>配置自动启动和自动关闭计划
服务结构群集也可以按计划启动或停止。 此体验类似于实验室中虚拟机的体验。 为了节省资金，默认情况下，在实验室中创建的每个群集都会在实验室[关闭策略](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)定义的时间自动关闭。 可以通过指定是否应关闭群集或指定群集关闭的时间来覆盖。 

![自动启动和自动关机的现有计划](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>选择加入自动启动计划
要选择加入启动计划，执行以下步骤：

1. 选择左侧菜单上**的自动启动**
2. 选择 **"打开**"**以允许此服务结构群集计划自动启动**。 仅当实验室所有者允许用户自动启动其虚拟机或 Service Fabric 群集时，才会启用此页面。
3. 在工具栏上选择“保存”。**** 

    ![自动星形页面](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>配置自动关机设置 
要更改关机设置，请执行以下步骤：

1. 选择左侧菜单上**的自动关闭**。 
2. 在此页上，您可以通过选择 **"已启用的关闭"** 来选择退出自动关闭**Enabled**。 
3. 如果选择了 **"启用****"，** 请按照以下步骤操作：
    1. 指定关机**时间**。
    2. 指定时间的**时区**。 
    3. 指定是否希望 DevTest Labs 在自动关闭之前发送**通知**。 
    4. 如果为通知选项选择了 **"是**"，请指定**Webhook URL**和/或**电子邮件地址**以发送通知。 
    5. 在工具栏上选择“保存”。****

        ![自动关闭页面](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>查看服务结构群集中的节点
在前面的步骤中看到的服务结构群集页面特定于 DevTest Labs 页面。 它不显示群集中的节点。 要查看有关群集的详细信息，请按照以下步骤操作：

1. 在实验室的**DevTest 实验室**页面上，在"**我的虚拟机"** 部分的树视图中选择**资源组**。

    ![选择资源组](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. 在"**资源组"** 页上，您可以看到列表中资源组中的所有资源。 从列表中选择**您的服务结构群集**。 

    ![在列表中选择群集](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. 您将看到群集的服务**结构群集**页面。 这是服务结构提供的页面。 您将看到有关群集的所有信息，如节点、节点类型等。

    ![服务结构群集主页](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>后续步骤
有关环境的详细信息，请参阅以下文章： 

- [使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [在 Azure 开发测试实验室中配置和使用公共环境](devtest-lab-configure-use-public-environments.md)
