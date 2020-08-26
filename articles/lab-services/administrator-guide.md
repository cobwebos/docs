---
title: Azure 实验室服务 - 管理员指南 | Microsoft Docs
description: 本指南可帮助使用 Azure 实验室服务创建和管理实验室帐户的管理员。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad3bc110d93efb5b735f77fb8a0b2af9e4f9a7cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444142"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure 实验室服务 - 管理员指南
管理大学云资源的信息技术 (IT) 管理员通常负责为学校设置实验室帐户。 设置实验室帐户后，管理员或教师会创建实验室帐户中包含的课堂实验室。 本文简要概述了所涉及的 Azure 资源以及创建这些资源的指导。

![实验室帐户中的 Azure 资源的高级视图](./media/administrator-guide/high-level-view.png)

- 课堂实验室在 Azure 实验室服务拥有的 Azure 订阅中进行托管。
- 实验室帐户、共享映像库和映像版本在订阅中进行托管。
- 你可以使实验室帐户和共享映像库处于相同的资源组中。 在此图中，它们处于不同的资源组中。 

## <a name="subscription"></a>订阅
你的大学具有一个或多个 Azure 订阅。 订阅用于为其中使用的所有 Azure 资源\服务（包括实验室帐户）管理计费和安全性。

实验室帐户与其订阅之间的关系非常重要，因为：

- 计费通过包含实验室帐户的订阅进行报告。
- 可以向订阅的 Azure Active Directory (AD) 租户中的用户授予对 Azure 实验室服务的访问权限。 可以添加用户作为实验室帐户所有者\参与者、课堂实验室创建者或课堂实验室所有者。

课堂实验室及其虚拟机 (VM) 在 Azure 实验室服务拥有的订阅中进行管理和托管。

## <a name="resource-group"></a>资源组
订阅包含一个或多个资源组。 资源组用于创建在同一解决方案中一起使用的 Azure 资源的逻辑分组。  

创建实验室帐户时，必须配置包含实验室帐户的资源组。 

创建[共享映像库](#shared-image-gallery)时，也需要资源组。 可以选择将实验室帐户和共享映像库放在两个单独的资源组中，这在计划跨不同解决方案共享映像库时十分典型。 或者，可以选择将它们放在同一个资源组中。

创建实验室帐户时，可以同时自动创建并附加共享映像库。  此选项会导致在单独的资源组中创建实验室帐户和共享映像库。 使用本教程中所述的步骤时，你将看到此行为：[在创建实验室帐户时配置共享映像库](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation)。 本文顶部的图像也使用此配置。 

建议提前投入时间来规划资源组的结构，因为实验室帐户或共享映像库的资源组在创建后无法更改。 如果需要更改这些资源的资源组，则需要删除并重新创建实验室帐户和/或共享映像库。

## <a name="lab-account"></a>实验室帐户

实验室帐户充当一个或多个课堂实验室的容器。 开始使用 Azure 实验室服务时，通常只有一个实验室帐户。 随着实验室使用的扩展，以后可能会选择创建更多实验室帐户。

以下列表突出显示了多个实验室帐户可能十分有益的方案：

- 跨课堂实验室管理不同策略要求

    设置实验室帐户时，会设置应用于实验室帐户下所有课堂实验室的策略例如：
    - 具有课堂实验室可以访问的共享资源的 Azure 虚拟网络。 例如，你可能有一组课堂实验室需要访问虚拟网络中的共享数据集。
    - 课堂实验室可用于创建虚拟机 (VM) 的 VM 映像。 例如，你可能有一组课堂实验室需要访问 [Data Science VM for Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 市场映像。

    如果你的课堂实验室彼此之间具有独特的策略要求，则创建单独实验室帐户以单独管理这些教室实验室可能会十分有益。

- 按实验室帐户的单独预算
  
    你可能需要更清晰的单独预算，而不是通过单个实验室帐户报告所有课堂实验室成本。 例如，可以为大学的数学系、计算机科学系等创建实验室帐户，以便将不同院系的预算分开。  随后可以使用 [Azure 成本管理](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)查看每个单独实验室帐户的成本。

- 将实验室与 active\production 试点实验室分隔
  
    你可能会遇到这样的情况：你要针对某个实验室帐户的试用策略更改，而不会影响活动\生产实验室。 在这种类型的情况下，为进行试点而创建单独实验室帐户允许隔离更改。 

## <a name="classroom-lab"></a>课堂实验室

课堂实验室包含各自分配给单个学生的虚拟机 (VM)。  一般而言，可以：

- 让一个课堂实验室用于每个课程。
- 每学期（或者对于提供课程的各个时间范围）创建一组新的课堂实验室。 通常，对于具有相同映像需求的课程，应使用[共享映像库](#shared-image-gallery)跨实验室和学期重复使用映像。

确定如何构造课堂实验室时，请注意以下几点：

- 使用已发布的相同映像部署课堂实验室中的所有 VM

    因此，如果你有一个需要同时发布不同实验室映像的课程，则必须为每个映像创建单独的课堂实验室。
  
- 使用配额在实验室级别进行设置，应用于实验室中的所有用户

    若要为用户设置不同的配额，必须创建单独的课堂实验室。 但是，设置配额之后，可以将更多时数添加到特定用户。
  
- 启动或关闭计划在实验室级别进行设置，应用于实验室中的所有 VM

    与上一点类似，如果需要为用户设置不同的计划，则需要创建单独的课堂实验室。

默认情况下，每个课堂实验室具有其自己的虚拟网络。  如果启用了 vnet 对等互连，则每个课堂实验室都将自己的子网对等互连到指定虚拟网络。

## <a name="shared-image-gallery"></a>共享映像库

共享映像库附加到实验室帐户，用作存储映像的中央存储库。 当教师选择从课堂实验室模板虚拟机 (VM) 中导出时，映像会保存在该库中。 教师每次更改模板 VM 和导出时，将保存新版本的映像，同时维护以前的版本。

当教师创建新的课堂实验室时，他们可以从共享映像库发布映像版本。 尽管该库存储了多个版本的映像，但教师只能在实验室创建过程中选择最新版本。

共享映像库是一种可选资源，仅从少量课堂实验室开始时，可能不会立即需要该资源。 然而，随着扩展为具有更多课堂实验室，使用共享映像库有很多好处：

- 使你可以保存和管理模板 VM 映像的各个版本

    创建自定义映像，或是对来自公共市场库的映像进行更改（软件、配置等）时很有用。  例如，教师常常需要安装不同的软件\工具。 可以将不同版本的模板 VM 映像导出到共享映像库，而不是要求学生手动安装这些必备组件。 随后可以在创建新的课堂实验室时使用这些映像版本。
- 实现跨课堂实验室共享\重复使用模板 VM 映像

    可以保存并重复使用映像，这样便不必在每次创建新的课堂实验室时都从头开始配置映像。 例如，如果提供需要同一个映像的多个课程，则此映像只需创建一次，并导出到共享映像库，便可以跨课堂实验室共享它。
- 通过复制确保映像可用性

    从课堂实验室保存到共享映像库时，映像会自动复制到[同一地域中的其他区域](https://azure.microsoft.com/global-infrastructure/regions/)。 如果某个区域发生中断，则将映像发布到课堂实验室不受影响，因为可以使用来自另一个区域的映像副本。  从多个副本发布 VM 还可以帮助提高性能。

若要对共享映像进行逻辑分组，可以使用以下几个选项：

- 创建多个共享映像库。 每个实验室帐户只能连接到一个共享映像库，因此此选项还要求创建多个实验室帐户。
- 或者，可以使用由多个实验室帐户共享的单个共享映像库。 在这种情况下，每个实验室帐户只能启用适用于它包含的课堂实验室的映像。

## <a name="naming"></a>命名

在刚开始使用 Azure 实验室服务时，建议为资源组、实验室帐户、课堂实验室和共享映像库建立命名约定。 尽管建立的命名约定对于组织的需求是独有的，不过下表概述了一般准则。

| 资源类型 | 角色 | 建议的模式 | 示例 |
| ------------- | ---- | ----------------- | -------- | 
| 资源组 | 包含一个或多个实验室帐户以及一个或多个共享映像库 | \<organization short name\>-\<environment\>-rg<ul><li>“组织短名称”标识资源组支持的组织的名称</li><li>“环境”标识资源的环境，如 Pilot 或 Production</li><li>Rg 表示资源类型：资源组。</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| 实验室帐户 | 包含一个或多个实验室 | \<organization short name\>-\<environment\>-la<ul><li>“组织短名称”标识资源组支持的组织的名称</li><li>“环境”标识资源的环境，如 Pilot 或 Production</li><li>La 表示资源类型：实验室帐户。</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| 课堂实验室 | 包含一个或多个 VM |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>“课程名称”标识实验室支持的课程的名称。</li><li>“时间范围”标识在其中提供课程的时间范围。</li>“教师标识符”标识拥有实验室的教师。</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| 共享映像库 | 包含一个或多个 VM 映像版本 | \<organization short name\>库 | contosouniversitylabsgallery |

有关命名其他 Azure 资源的详细信息，请参阅 [Azure 资源的命名约定](/azure/architecture/best-practices/naming-conventions)。

## <a name="regionslocations"></a>区域\位置

设置 Azure 实验室服务的资源时，需要提供将托管资源的数据中心的区域（或位置）。 下面更详细地介绍了区域如何影响设置实验室时所涉及的每个资源。

### <a name="resource-group"></a>资源组

区域指定存储有关资源组的信息的数据中心。 资源组中包含的 Azure 资源可以处于与其父组不同的区域中。

### <a name="lab-account"></a>实验室帐户

实验室帐户的位置指示此资源所存在的区域。  

### <a name="classroom-lab"></a>课堂实验室

课堂实验室所存在的位置因以下因素而异：

  - 实验室帐户对等互连到虚拟网络 (VNet)
  
    当实验室帐户和 VNet 处于同一个区域时，帐户可以[与 VNet 对等互连](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)。  实验室帐户与 VNet 对等互连时，会在实验室帐户和 VNet 所在的同一个区域中自动创建课堂实验室。

    > [!NOTE]
    > 实验室帐户与 VNet 对等互连时，会禁用设置“允许实验室创建者选取实验室位置”。 可以在以下文章中找到有个此设置的其他信息：[允许实验室创建者选取实验室的位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)。
    
  - 未与 VNet 对等互连 *****并且不允许实验室创建者选取实验室位置*****
  
    如果没有 VNet 与实验室帐户对等互连并且[不允许实验室创建者选取实验室位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)，则会在具有可用 VM 容量的区域中自动创建课堂实验室。  具体而言，Azure 实验室服务会在[与实验室帐户处于同一地域的区域](https://azure.microsoft.com/global-infrastructure/regions)中查找可用性。

  - 未与 VNet 对等互连 *****并且允许实验室创建者选取实验室位置*****
       
    如果没有与 VNet 对等互连，并且[允许实验室创建者选取实验室位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)，则实验室创建者可以选择的位置基于可用容量。

> [!NOTE]
> 若要帮助确保某个社区有足够的 VM 容量，请务必先通过实验室帐户或是在创建实验室时请求容量。

一般规则是将资源的区域设置为最靠近其用户的区域。 对于课堂实验室，这意味着创建最靠近学生的课堂实验室。 对于学生遍布世界各地的在线课程，需要根据自己的最佳判断来创建位于中心位置的课堂实验室。 或者，将课程拆分为多个基于学生区域的课堂实验室。

### <a name="shared-image-gallery"></a>共享映像库

区域指示在自动复制到目标区域之前存储第一个映像版本的源区域。

## <a name="vm-sizing"></a>VM 大小调整

当管理员或实验室创建者创建课堂实验室时，他们可以基于课堂的需求从以下 VM 大小中进行选择。 请记住，可用的计算大小取决于实验室帐户所在的区域：

| 大小 | 规格 | 系列 | 建议用途 |
| ---- | ----- | ------ | ------------- |
| 小型| <ul><li>2 个核心</li><li>3.5 GB RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最适用于命令行、打开 Web 浏览器、低流量 Web 服务器、中小型数据库。 |
| 中型 | <ul><li>4 个核心</li><li>7 GB RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最适用于关系数据库、内存中缓存和分析。 |
| 中等(嵌套虚拟化) | <ul><li>4 个核心</li><li>16 GB RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | 此大小最适用于关系数据库、内存中缓存和分析。
| 大型 | <ul><li>8 核</li><li>16 GB RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 此大小最适用于需要 CPU 更快、本地磁盘性能更佳、大型数据库、大型内存缓存的应用程序。  此大小还支持嵌套虚拟化。 |
| 大型(嵌套虚拟化) | <ul><li>8 核</li><li>16 GB RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 此大小最适用于需要 CPU 更快、本地磁盘性能更佳、大型数据库、大型内存缓存的应用程序。 |
| 小型 GPU (可视化) | <ul><li>6 个核心</li><li>56 GB RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | 此大小最适合使用框架（如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏和编码。 |
| 小型 GPU (计算) | <ul><li>6 个核心</li><li>56 GB RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |此大小最适用于计算密集型应用程序（如人工智能和深度学习）。 |
| 中等 GPU (可视化) | <ul><li>12 个核心</li><li>112 GB RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最适合使用框架（如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏和编码。 |

## <a name="manage-identity"></a>管理标识

使用 [Azure 基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)，可以分配以下角色以授予对实验室帐户和课堂实验室的访问权限：

- 实验室帐户所有者

    创建实验室帐户的管理员会自动添加到实验室帐户的所有者角色。  分配有所有者角色的管理员可以：
     - 更改实验室帐户的设置。
     - 向其他管理员授予作为所有者或参与者访问实验室帐户的权限。
     - 向教师授予作为创建者、所有者或参与者访问课堂实验室的权限。
     - 在实验室帐户中创建和管理所有课堂实验室。

- 实验室帐户参与者

    分配有参与者角色的管理员可以：
    - 更改实验室帐户的设置。
    - 在实验室帐户中创建和管理所有课堂实验室。

    但是，无法向其他用户授予访问实验室帐户或课堂实验室的权限。

- 课堂实验室创建者

    若要在实验室帐户中创建课堂实验室，教师必须是实验室创建者角色的成员。  教师创建课堂实验室时，他们会自动添加为该实验室的所有者。  请参阅有关如何[将用户添加到实验室创建者角色](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)的教程。 

- 课堂实验室所有者\参与者
  
    当教师是实验室所有者或参与者角色的成员时，他们可以查看和更改课堂实验室的设置；他们还必须是实验室帐户的读者角色的成员。

    实验室所有者与参与者角色之间的主要区别在于，参与者无法向其他用户授予管理实验室的访问权限 - 只有所有者才能向其他用户授予管理实验室的访问权限。

    此外，除非教师也是实验室创建者角色的成员，否则无法创建新的课堂实验室。

- 共享映像库

    将共享映像库附加到实验室帐户时，会自动向实验室帐户所有者\参与者和实验室创建者\所有者\参与者授予在库中查看和保存映像的访问权限。

下面是一些有助于分配角色的提示：
   - 通常，只有管理员才应该是实验室帐户的所有者或参与者角色的成员；可以有多个所有者\参与者。
   - 若要让教师能够创建新的课堂实验室并管理他们创建的实验室，只需分配对实验室创建者角色的访问权限。
   - 若要让教师能够管理特定课堂实验室，但无法创建新实验室，应为要管理的每个课堂实验室分配对所有者或参与者角色的访问权限。  例如，你可能要允许一个教授和一个助教共同拥有一个课堂实验室。  请参阅有关如何[将用户作为所有者添加到课堂实验室](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner)的指南。

## <a name="pricing"></a>定价

### <a name="azure-lab-services"></a>Azure 实验室服务

以下文章介绍 Azure 实验室服务的定价：[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

如果你计划使用共享映像库存储和管理映像版本，则还需要考虑其定价。 

### <a name="shared-image-gallery"></a>共享映像库

创建共享映像库并将它附加到实验室帐户是免费的。 在将映像版本保存到该库之前，不会产生成本。 通常，使用共享映像库的价格几乎可忽略不计，但了解其计算方式非常重要，因为它不包括在 Azure 实验室服务的定价中。  

#### <a name="storage-charges"></a>存储费用

为了存储映像版本，共享映像库使用标准 HDD 托管磁盘。 所使用的 HDD 托管磁盘的大小取决于所存储的映像版本大小。 若要查看定价，请参阅以下文章：[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。

#### <a name="replication-and-network-egress-charges"></a>复制和网络流出量费用

使用课堂实验室模板虚拟机 (VM) 保存映像版本时，Azure 实验室服务首先将它存储在源区域中，然后自动将源映像版本复制到一个或多个目标区域。 必须注意的一点是，Azure 实验室服务会自动将源映像版本复制到课堂实验室所在的[地域中的所有目标区域](https://azure.microsoft.com/global-infrastructure/regions/)。 例如，如果课堂实验室处于美国地域，则映像版本会复制到存在于美国的所有八个区域。

将映像版本从源区域复制到其他目标区域时，会产生网络流出量费用。 计费量基于映像数据最初从源区域进行出站传输时的映像版本大小。  有关定价详细信息，请参阅以下文章：[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。

[教育解决方案](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3)客户可能会被免于支付流出量费用。 请与客户经理联系以了解详细信息。  有关详细信息，请参阅链接文档中的常见问题解答部分，特别是问题“对于学术客户有哪些数据传输计划，我如何获得资格？”。

#### <a name="pricing-example"></a>定价示例

为了概述上述定价，我们来看一个将模板 VM 映像保存到共享映像库的示例。 假设以下情形：

- 你有一个自定义 VM 映像。
- 你要保存该映像的两个版本。
- 你的实验室处于美国（共有八个区域）。
- 每个映像版本的大小为 32 GB；因此，HDD 托管磁盘价格是每月 1.54 美元。

总体成本估算为：

映像数 × 版本数 × 副本数 × 托管磁盘价格

在此示例中，成本为：

1 个自定义映像 (32 GB) x 2 个版本 x 8 个美国区域 x 1.54 美元 = 24.64 美元/月

#### <a name="cost-management"></a>成本管理

实验室帐户管理员可通过定期从库中删除不需要的映像版本来管理成本，这十分重要。 

不应删除到特定区域的复制来降低成本（此选项存在于共享映像库中）。 复制更改可能会对 Azure 实验室服务从共享映像库中保存的映像发布 VM 的能力产生负面影响。

## <a name="next-steps"></a>后续步骤

请参阅教程以了解创建实验室帐户和实验室的分步说明：[设置指南](tutorial-setup-lab-account.md)
