---
title: Azure 实验室服务-管理员指南 |Microsoft Docs
description: 本指南可帮助使用 Azure 实验室服务创建和管理实验室帐户的管理员。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: af69b9105d55749267e6c54a6584566b499b4097
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118458"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure 实验室服务-管理员指南
管理大学云资源的信息技术（IT）管理员通常负责为学校设置实验室帐户。 设置实验室帐户后，管理员或教师会创建实验室帐户中包含的教室实验室。 本文详细介绍了所涉及的 Azure 资源以及创建这些资源的指南。

![实验室帐户中 Azure 资源的高级视图](../media/administrator-guide/high-level-view.png)

- 教室实验室托管在 Azure 实验室服务拥有的 Azure 订阅中。
- 实验室帐户、共享映像库和映像版本托管在你的订阅中。
- 你可以在同一资源组中包含实验室帐户和共享映像库。 在此图中，它们位于不同的资源组中。 

## <a name="subscription"></a>订阅
你的大学有一个或多个 Azure 订阅。 订阅用于管理在其中使用的所有 Azure resources\services （包括实验室帐户）的计费和安全性。

实验室帐户与其订阅之间的关系非常重要，因为：

- 计费通过包含实验室帐户的订阅进行报告。
- 可以向订阅的 Azure Active Directory （AD）租户中的用户授予对 Azure 实验室服务的访问权限。 可以添加一个用户作为实验室帐户 owner\contributor、课堂实验室创建者或教室实验室所有者。

课堂实验室及其虚拟机（Vm）在 Azure 实验室服务拥有的订阅中进行管理和托管。

## <a name="resource-group"></a>资源组
订阅包含一个或多个资源组。 资源组用于创建在同一解决方案中一起使用的 Azure 资源的逻辑分组。  

创建实验室帐户时，必须配置包含实验室帐户的资源组。 

创建[共享映像库](#shared-image-gallery)时，还需要资源组。 你可以选择将实验室帐户和共享映像库放在两个不同的资源组中，这在你计划跨不同解决方案共享映像库时很常见。 或者，你可以选择将它们放在同一资源组中。

创建实验室帐户时，可以同时自动创建并附加共享映像库。  此选项会导致在单独的资源组中创建实验室帐户和共享映像库。 使用本教程中所述的步骤时，你将看到此行为：[在创建实验室帐户时配置共享映像库](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation)。 本文顶部的图像还使用此配置。 

建议提前投入时间来规划资源组的结构，因为创建实验室帐户或共享映像库的资源组后，*不*能对其进行更改。 如果需要更改这些资源的资源组，则需要删除并重新创建实验室帐户和/或共享映像库。

## <a name="lab-account"></a>实验室帐户

实验室帐户充当一个或多个教室实验室的容器。 开始使用 Azure 实验室服务时，通常只有一个实验室帐户。 随着实验室使用情况的扩展，稍后可以选择创建更多实验室帐户。

以下列表突出显示了多个实验室帐户可能有用的方案：

- **跨教室实验室管理不同的策略要求**

    设置实验室帐户时，将设置适用于实验室帐户下*所有*教室实验室的策略，例如：
    - 具有教室实验室可以访问的共享资源的 Azure 虚拟网络。 例如，你可能有一组教室实验室需要访问虚拟网络中的共享数据集。
    - 教室实验室可用于创建 Vm 的虚拟机（VM）映像。 例如，你可能有一组教室实验室需要访问[适用于 Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Marketplace 映像的 Data Science VM。

    如果您的课堂实验室具有独特的策略要求，则创建单独的实验室帐户以单独管理这些教室实验室可能会有好处。

- **按实验室帐户区分预算**
  
    你可能需要更清晰的预算，而不是通过单个实验室帐户报告所有教室实验室成本。 例如，可以为大学的数学系、计算机科学部门等创建实验室帐户，以便将预算分散到不同的部门。  然后，你可以使用[Azure 成本管理](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)查看每个单独实验室帐户的成本。

- **将试验实验室与 active\production 实验室隔离**
  
    你可能会遇到这样的情况：你想要试验实验室帐户的策略更改，而不会影响 active\production 实验室。 在这种情况下，为试验目的创建单独的实验室帐户允许隔离更改。 

## <a name="classroom-lab"></a>课堂实验室

教室实验室包含每个虚拟机（Vm），每个虚拟机都分配给一名学生。  通常，您可以：

- 每个类都有一个教室实验室。
- 创建一组新的教室实验室，每个学期（或为你的类提供的每个时间范围）。 通常，对于具有相同映像需求的类，应使用[共享映像库](#shared-image-gallery)跨实验室和学期重复使用映像。

确定如何构造教室实验室时，请注意以下几点：

- **使用已发布的同一映像部署教室实验室中的所有 Vm**

    因此，如果你有一个需要同时发布不同实验室映像的类，则必须为每个用户创建单独的教室实验室。
  
- **使用配额在实验室级别设置，适用于实验室中的所有用户**

    若要为用户设置不同的配额，必须创建单独的教室实验室。 但是，在设置配额后，可以将更多的时间添加到特定用户。
  
- **启动或关闭计划在实验室级别设置，适用于实验室中的所有 Vm**

    与上一点类似，如果需要为用户设置不同的计划，则需要创建单独的教室实验室。

默认情况下，每个教室实验室都有其自己的虚拟网络。  如果已启用 vnet 对等互连，则每个教室实验室都将有自己的对等互连到指定虚拟网络的子网。

## <a name="shared-image-gallery"></a>共享映像库

共享映像库附加到实验室帐户，用作存储图像的中央存储库。 当教师从课堂实验室模板虚拟机（VM）中选择导出时，图像将保存在库中。 当教师每次更改模板 VM 和导出时，将保存新版本的映像，同时保留以前的版本。

当教师创建新的教室实验室时，教师可以从共享映像库发布映像版本。 尽管库存储了多个版本的映像，但教师只能在创建实验室的过程中选择最新版本。

共享映像库是一种可选资源，如果仅从几个教室实验室开始，你可能不需要此资源。 然而，使用共享图像库有很多好处，因为缩放以获得更多的课堂实验室：

- **使你能够保存和管理模板 VM 映像的版本**

    创建自定义映像，或将其更改（软件、配置等）到公共 Marketplace 库中的映像很有用。  例如，教师通常需要安装不同的 software\tooling。 不同版本的模板 VM 映像可以导出到共享映像库，而不是要求学生手动安装这些必备组件。 然后，可以在创建新的教室实验室时使用这些映像版本。
- **跨教室实验室启用模板 VM 映像的 sharing\reuse**

    可以保存并重复使用映像，这样就不必在每次创建新的教室实验室时从头开始配置映像。 例如，如果提供了需要同一映像的多个类，则此映像只需创建一次，并导出到共享映像库，以便可以跨教室实验室共享。
- **通过复制确保映像可用性**

    当你从教室实验室保存到共享映像库时，你的图像将自动复制到[同一地域中](https://azure.microsoft.com/global-infrastructure/regions/)的其他区域。 如果某个区域发生服务中断，则将该映像发布到教室实验室不受影响，因为可以使用来自另一个区域的映像副本。  从多个副本发布 Vm 还可以帮助提高性能。

若要对共享映像进行逻辑分组，可以使用以下几个选项：

- 创建多个共享映像库。 每个实验室帐户只能连接到一个共享映像库，因此此选项还将要求你创建多个实验室帐户。
- 或者，可以使用由多个实验室帐户共享的单个共享映像库。 在这种情况下，每个实验室帐户只能启用适用于其包含的教室实验室的映像。

## <a name="naming"></a>命名

在 Azure 实验室服务入门中，我们建议你为资源组、实验室帐户、教室实验室和共享映像库建立命名约定。 尽管你建立的命名约定对于组织的需求是唯一的，但下表概述了一般准则。

| 资源类型 | 角色 | 建议的模式 | 示例 |
| ------------- | ---- | ----------------- | -------- | 
| 资源组 | 包含一个或多个实验室帐户以及一个或多个共享映像库 | \<组织短名称 \> - \< 环境 \> -rg<ul><li>**组织短名称**标识资源组支持的组织的名称</li><li>**环境**标识资源的环境，如试点或生产</li><li>**Rg**代表资源类型：资源组。</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-rg<br/>contosouniversitylabs |
| 实验室帐户 | 包含一个或多个实验室 | \<组织短名称 \> - \< 环境 \> -la<ul><li>**组织短名称**标识资源组支持的组织的名称</li><li>**环境**标识资源的环境，如试点或生产</li><li>**La**代表资源类型：实验室帐户。</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-la<br/>sciencedeptlabs-照 |
| 课堂实验室 | 包含一个或多个 Vm |\<类名 \> - \< 时间范围 \> - \< 教师标识符\><ul><li>**类名称**标识实验室支持的类的名称。</li><li>**时间范围**确定提供类的时间范围。</li>**教育标识符**标识拥有实验室的教师。</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| 共享映像库 | 包含一个或多个 VM 映像版本 | \<组织短名称 \> 库 | contosouniversitylabsgallery |

有关命名其他 Azure 资源的详细信息，请参阅[Azure 资源的命名约定](/azure/architecture/best-practices/naming-conventions)。

## <a name="regionslocations"></a>Regions\locations

设置 Azure 实验室服务的资源时，需要提供将托管资源的数据中心的一个或多个区域。 下面更详细地介绍了区域如何影响设置实验室所涉及的每个资源。

### <a name="resource-group"></a>资源组

区域指定数据中心，其中存储了有关资源组的信息。 资源组中包含的 Azure 资源可以位于其父组中的不同区域。

### <a name="lab-account"></a>实验室帐户

实验室帐户的位置指示此资源存在于其中的区域。  

### <a name="classroom-lab"></a>课堂实验室

教室实验室存在的位置根据以下因素而异：

  - **实验室帐户对等互连虚拟网络（VNet）**
  
    如果某一实验室帐户位于同一区域，则可以[使用该帐户对等互连](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)。  当使用 VNet 对等互连实验室帐户时，将在实验室帐户和 VNet 所在的同一区域中自动创建教室实验室。

    > [!NOTE]
    > 当使用 VNet 对等互连实验室帐户时，将禁用 "**允许实验室创建者选取实验室位置**" 设置。 有关此设置的详细信息，请参阅文章：[允许 lab creator 选取实验室的位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)。
    
  - **不对等互连 VNet ***，*** 实验室创建者不允许选择实验室位置**
  
    如果没有具有实验室帐户**的 VNet 对等互连**，*并且* [**不**允许实验室创建者选取实验室位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)，则会在具有可用 VM 容量的区域中自动创建教室实验室。  具体而言，Azure 实验室服务会在与[实验室帐户相同的地理区域](https://azure.microsoft.com/global-infrastructure/regions)中查找可用性。

  - **不对等互连 VNet ***，并且***允许实验室创建者选取实验室位置**
       
    当**没有 VNet 对等互连**并且[实验室创建者允许选取实验室位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)时，实验室创建者可以选择的位置基于可用容量。

> [!NOTE]
> 若要帮助确保有足够的 VM 容量用于区域，请务必先通过实验室帐户或创建实验室来请求容量。

一般规则是将资源的区域设置为离其用户最近的区域。 对于课堂实验室，这意味着创建离学生最近的教室实验室。 对于学生遍布世界各地的在线课程，你需要使用最佳判断来创建一个集中定位的教室实验室。 或者，将一个类拆分为多个基于学生区域的教室实验室。

### <a name="shared-image-gallery"></a>共享映像库

区域指示存储第一个映像版本的源区域，然后将其自动复制到目标区域。

## <a name="vm-sizing"></a>VM 大小调整

当管理员或实验室创建者创建教室实验室时，他们可以根据教室的需要从以下 VM 大小中进行选择。 请记住，可用的计算大小取决于实验室帐户所在的区域：

| 大小 | 规格 | 系列 | 建议使用 |
| ---- | ----- | ------ | ------------- |
| 小型| <ul><li>双核</li><li>3.5 GB RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最适用于命令行、打开 web 浏览器、低流量 web 服务器、小到中型数据库。 |
| 中型 | <ul><li>4核</li><li>7 GB RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最适合用于关系数据库、内存中缓存和分析。 |
| 中（嵌套虚拟化） | <ul><li>4核</li><li>16 GB 内存</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | 此大小最适合用于关系数据库、内存中缓存和分析。  此大小还支持嵌套虚拟化。 |
| 大型 | <ul><li>8 核</li><li>32 GB RAM</li></ul>  | [Standard_DC8_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最适用于需要更快的 Cpu、更好的本地磁盘性能、大型数据库、大内存缓存的应用程序。  此大小还支持嵌套虚拟化。 |
| 小型 GPU （可视化效果） | <ul><li>6核</li><li>56 GB RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | 此大小最适合用于使用框架（如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏、编码。 |
| 小型 GPU （计算） | <ul><li>6核</li><li>56 GB RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |此大小最适用于计算机密集型应用程序，如人工智能和深度学习。 |
| 中等 GPU （可视化效果） | <ul><li>12核</li><li>112 GB RAM</li></ul>  | [Standard_NC12](https://docs.microsoft.com/azure/virtual-machines/nc-series) | 此大小最适合用于使用框架（如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏、编码。 |

## <a name="manage-identity"></a>管理标识

使用[Azure 的基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)时，可以分配以下角色以授予对实验室帐户和教室实验室的访问权限：

- **实验室帐户所有者**

    创建实验室帐户的管理员会自动添加到实验室帐户的**所有者**角色。  分配有**所有者**角色的管理员可以：
     - 更改实验室帐户的设置。
     - 使其他管理员可以访问实验室帐户作为所有者或参与者。
     - 为创建者、所有者或参与者授予课堂实验室的教师访问权限。
     - 在实验室帐户中创建和管理所有教室实验室。

- **实验室帐户参与者**

    分配有 "**参与者**" 角色的管理员可以：
    - 更改实验室帐户的设置。
    - 在实验室帐户中创建和管理所有教室实验室。

    但是，他们*不能*向其他用户授予对实验室帐户或教室实验室的访问权限。

- **课堂实验室创建者**

    若要在实验室帐户中创建教室实验室，教师必须是**实验室创建者**角色的成员。  当教师创建教室实验室时，它们会自动作为实验室的所有者添加。  请参阅教程，了解如何[将用户添加到**Lab Creator**角色](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)。 

- **课堂实验室 owner\contributor**
  
    当教师是实验室**所有者**或**参与者**角色的成员时，他们可以查看和更改教室实验室的设置;它们还必须是实验室帐户的 "**读取**者" 角色的成员。

    实验室**所有者**和**参与者**角色之间的主要区别在于，参与者*无法*向其他用户授予管理仅实验室所有者的权限，从而使其他用户能够访问管理实验室。

    此外，除非教师也是 "**实验室创建者**" 角色的成员，否则*不能*创建新的教室实验室。

- **共享映像库**

    将共享映像库附加到实验室帐户时，会自动向实验室帐户 owners\contributors 和 lab creators\owners\contributors 提供访问权限，以便在库中查看和保存图像。

下面是一些有助于分配角色的提示：
   - 通常，只有管理员才应该是实验室帐户的**所有者**或**参与者**角色的成员;可能有多个 owner\contributor。
   - 让教师能够创建新的教室实验室并管理他们创建的实验室;只需分配对**实验室创建者**角色的访问权限。
   - 为教师授予管理特定教室实验室的能力，但*不*允许创建新的实验室;应为要管理的每个教室实验室分配**所有者**或**参与者**角色的访问权限。  例如，你可能想要允许教授和教授助手共同拥有教室实验室。  请参阅有关如何[将用户作为所有者添加到教室实验室](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner)的指南。

## <a name="pricing"></a>定价

### <a name="azure-lab-services"></a>Azure 实验室服务

以下文章中介绍了 Azure 实验室服务的定价： [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

如果你打算使用共享映像库来存储和管理映像版本，还需要考虑它的定价。 

### <a name="shared-image-gallery"></a>共享映像库

创建共享映像库并将其附加到实验室帐户是免费的。 在将图像版本保存到库中之前，不会产生成本。 通常，使用共享图像库的价格相当不多，但要了解它是如何计算的，因为它不包括在 Azure 实验室服务的定价中。  

#### <a name="storage-charges"></a>存储费用

为了存储映像版本，共享映像库使用标准 HDD 托管磁盘。 所使用的 HDD 托管磁盘的大小取决于所存储的映像版本大小。 请参阅以下文章，查看定价：[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。

#### <a name="replication-and-network-egress-charges"></a>复制和网络出口费用

使用课堂实验室模板虚拟机（VM）保存映像版本时，Azure 实验室服务首先将其存储在源区域中，然后自动将源映像版本复制到一个或多个目标区域。 需要特别注意的是，Azure 实验室服务会自动将源映像版本复制到位于教室实验室所在[区域内](https://azure.microsoft.com/global-infrastructure/regions/)的所有目标区域。 例如，如果你的教室实验室在美国地理位置，则会将映像版本复制到美国境内每个

将映像版本从源区域复制到其他目标区域时，会产生网络出口费用。 根据映像的数据最初从源区域传输出站时的图像版本大小，计费量。  有关定价的详细信息，请参阅以下文章：[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。

[教育解决方案](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3)客户可能会因支付出口费用而停征。 请与帐户管理员联系以了解详细信息。  有关详细信息，请参阅链接文档中**的 "常见问题**" 部分，尤其是 "面向学术客户的数据传输程序和如何限定？" 的问题。

#### <a name="pricing-example"></a>定价示例

为了概述上述定价，我们来看一个将模板 VM 映像保存到共享映像库的示例。 假设以下方案：

- 你有一个自定义 VM 映像。
- 正在保存图像的两个版本。
- 你的实验室在美国，共8个区域。
- 每个映像版本大小为 32 GB;因此，HDD 托管的磁盘价格是每月 $1.54。

总体成本估算为：

映像数×版本数×副本数×托管磁盘价格

在此示例中，成本为：

1个自定义映像（32 GB） x 2 版本 x 8 个美国区域 x $1.54 = $24.64/月

#### <a name="cost-management"></a>成本管理

实验室帐户管理员可以通过定期从库中删除不需要的映像版本来管理成本。 

不应删除到特定区域的复制，因为这样可以降低成本（此选项存在于共享映像库中）。 复制更改可能会对 Azure 实验室服务从共享映像库中保存的映像发布 Vm 的能力产生负面影响。

## <a name="next-steps"></a>后续步骤

请参阅教程，了解如何创建实验室帐户和实验室：[设置指南](tutorial-setup-lab-account.md)
