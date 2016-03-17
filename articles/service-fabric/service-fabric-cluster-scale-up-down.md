<properties
   pageTitle="扩展或缩减 Service Fabric 群集 | Microsoft Azure"
   description="根据要求通过添加或删除虚拟机节点来扩展或缩减 Service Fabric 群集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="01/29/2016"
   wacn.date=""/>


# 通过添加或删除虚拟机来扩展或缩减 Service Fabric 群集

你可以根据要求，通过添加或删除虚拟机来扩展或缩减 Azure Service Fabric 群集。

>[AZURE.NOTE] 你的订阅必须有足够的核心用于添加构成此群集的新 VM。

## 手动缩放 Service Fabric 群集

### 选择要缩放的节点类型

如果群集包含多个节点类型，你必须在特定节点类型中添加或删除 VM。方法如下：

1. 登录到[管理门户](https://manage.windowsazure.cn/)。

2. 导航到“Service Fabric 群集”。
 ![Azure 门户中的 Service Fabric 群集页。][BrowseServiceFabricClusterResource]

3. 选择想要扩展或缩减的群集。

4. 在群集仪表板上导航到“设置”边栏选项卡。如果没看到“设置”边栏选项卡，请单击群集仪表板上基本信息部分中的“所有设置”。

5. 单击“NodeTypes”，随后将打开“NodeTypes 列表”边栏选项卡。

6. 单击想要扩展或缩减的节点类型，随后将打开“NodeType 详细信息”边栏选项卡。

### 通过添加节点来扩展

根据需要调大 VM 数量，然后保存。部署完成后，将会增加节点/VM。

### 通过删除节点来缩减

删除节点是包含两个步骤的过程：

1. 根据需要调小 VM 数量，然后保存。滑块下方的值表示该节点类型所需的最少 VM 数量。

    >[AZURE.NOTE] 必须为主节点类型至少保留 5 个 VM。

2. 该部署完成后，系统告知现在可以删除的 VM 名称。然后，你需要导航到 VM 资源并将其删除：

    a.返回到群集仪表板并单击“资源组”。随后将打开“资源组”边栏选项卡。

    b.查看“摘要”下面的信息，或单击“...”打开资源列表。

    c.单击系统指出可以删除的 VM 名称。

    d.单击“删除”图标删除该 VM。

## 自动缩放 Service Fabric 群集

Service Fabric 群集目前不支持自动缩放。不久之后，群集将构建在虚拟机缩放集的基础之上，到时你便可以使用自动缩放功能，其行为与云服务的自动缩放行为类似。

## 使用 PowerShell/CLI 缩放群集

本文介绍使用门户缩放群集。但是，你也可以从命令行执行相同的操作，方法是对群集资源使用 Azure Resource Manager 命令。群集资源的 GET 响应提供已禁用的节点列表。

## 后续步骤

- [了解群集升级](/documentation/articles/service-fabric-cluster-upgrade)
- [了解如何为有状态服务分区以最大程度地实现缩放](/documentation/articles/service-fabric-concepts-partitioning)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=Mooncake_0307_2016-->