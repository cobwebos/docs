---
title: Azure 实验室服务 - 管理员指南 |微软文档
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
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 8608aaab7bb8b6d10e67f27678c17f20a6c243da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370853"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure 实验室服务 - 管理员指南
管理大学云资源的信息技术 （IT） 管理员通常负责为其学校设置实验室帐户。 设置实验室帐户后，管理员或教育工作者将创建实验室帐户中包含的教室实验室。 本文提供了所涉及的 Azure 资源的高级概述以及创建这些资源的指导。

![实验室帐户中 Azure 资源的高级视图](../media/administrator-guide/high-level-view.png)

- 课堂实验室托管在 Azure 实验室服务拥有的 Azure 订阅中。
- 实验室帐户、共享图像库和映像版本托管在订阅中。
- 可以将实验室帐户和共享图像库放在同一资源组中。 在此图中，它们位于不同的资源组中。 

## <a name="subscription"></a>订阅
您的大学具有一个或多个 Azure 订阅。 订阅用于管理在其中使用的所有 Azure 资源、服务（包括实验室帐户）的计费和安全。

实验室帐户与其订阅之间的关系很重要，因为：

- 计费通过包含实验室帐户的订阅报告。
- 您可以向订阅中的 Azure 活动目录 （AD） 租户中的用户授予对 Azure 实验室服务的权限。 您可以将用户添加为实验室帐户所有者、参与者、课堂实验室创建者或教室实验室所有者。

课堂实验室及其虚拟机 （VM） 在 Azure 实验室服务拥有的订阅中为您管理和托管。

## <a name="resource-group"></a>资源组
订阅包含一个或多个资源组。 资源组用于创建在同一解决方案中一起使用的 Azure 资源的逻辑分组。  

创建实验室帐户时，必须配置包含实验室帐户的资源组。 

创建[共享图像库](#shared-image-gallery)时也需要资源组。 您可以选择将实验室帐户和共享图像库放在两个单独的资源组中，如果您计划跨不同解决方案共享图像库，则这是典型的。 或者，您可以选择将它们放在同一资源组中。

创建实验室帐户时，可以同时自动创建和附加共享图像库。  此选项会导致在单独的资源组中创建实验室帐户和共享映像库。 在使用本教程中描述的步骤时，您将看到此行为：[在创建实验室帐户时配置共享映像库](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation)。 本文顶部的图像也使用此配置。 

我们建议预先投入时间来规划资源组的结构，因为创建实验室帐户或共享图像库的资源组后*无法*更改它。 如果需要更改这些资源的资源组，则需要删除并重新创建实验室帐户和\或共享图像库。

## <a name="lab-account"></a>实验室帐户
实验室帐户用作一个或多个教室实验室的容器。 开始使用 Azure 实验室服务时，通常只拥有一个实验室帐户。 随着实验室使用量的扩展，您以后可能会选择创建更多实验室帐户。

以下列表突出显示了多个实验室帐户可能是有益的方案：

- **跨课堂实验室管理不同的策略要求** 
    
    设置实验室帐户时，将设置适用于实验室帐户下*所有*教室实验室的策略，例如：
    - 具有教室实验室可以访问的共享资源的 Azure 虚拟网络。 例如，您可能有一组需要访问虚拟网络中的共享数据集的教室实验室。
    - 教室实验室可用于创建 VM 的虚拟机 （VM） 映像。 例如，您可能有一组教室实验室需要访问用于 Linux 应用商店映像[的数据科学 VM。](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) 
    
    如果您有具有彼此独特策略要求的教室实验室，则创建单独的实验室帐户以单独管理这些教室实验室可能是有益的。

- **按实验室帐户划分单独的预算**
  
    您可能需要更明确地划分预算，而不是通过单个实验室帐户报告所有课堂实验室成本。 例如，您可以为大学数学系、计算机科学系等创建实验室帐户，以跨部门划分预算。  然后，您可以使用[Azure 成本管理](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)查看每个实验室帐户的成本。
    
- **将试验实验室与活动生产实验室隔离开来**
  
    您可能希望为实验室帐户进行策略更改，而不会影响活动和生产实验室。 在此类方案中，为试验目的创建单独的实验室帐户允许您隔离更改。 

## <a name="classroom-lab"></a>课堂实验室
课堂实验室包含虚拟机 （VM），每个虚拟机都分配给一个学生。 通常，您可以预期：

- 每节课有一个教室实验室。
- 每学期创建一组新的课堂实验室（或针对每次课程提供的时间框架）。 通常，对于具有相同图像需求的类，应使用[共享图像库](#shared-image-gallery)在实验室和学期之间重用图像。

在确定如何构建课堂实验室时，请考虑以下几点：

- **教室实验室中的所有 VM 都部署与已发布的映像相同的映像**

    因此，如果您有一个需要同时发布不同实验室图像的类，则必须为每个教室创建单独的教室实验室。
  
- **使用配额在实验室级别设置，并应用于实验室内的所有用户**
    
    要为用户设置不同的配额，必须创建单独的教室实验室。 但是，在设置配额后，可以向特定用户添加更多小时。
  
- **启动或关闭计划设置在实验室级别，并应用于实验室内的所有 VM**

    与前一点类似，如果需要为用户设置不同的计划，则需要创建单独的教室实验室。 

## <a name="shared-image-gallery"></a>共享映像库
共享图像库附加到实验室帐户，并用作存储图像的中央存储库。 当教育者选择从教室实验室的模板虚拟机 （VM） 导出时，图像将保存在库中。 每次教育者更改模板 VM 和导出时，都会保存映像的新版本，同时维护以前的版本。

教师可以在创建新的教室实验室时从共享图像库中发布图像版本。 尽管库可以存储图像的多个版本，但教育工作者只能在实验室创建期间选择最新版本。

共享图像库是一种可选资源，只需几个教室实验室，您就不需要立即使用。 但是，使用共享图像库有许多好处，在扩展到拥有更多教室实验室时非常有用：

- **使您能够保存和管理模板 VM 映像的版本**

    从公共应用商店库中创建自定义映像或对映像进行更改（软件、配置等）非常有用。  例如，教育工作者通常要求安装不同的软件\工具。 模板 VM 映像的不同版本可以导出到共享映像库，而不是要求学生自行手动安装这些先决条件。 然后，在创建新的教室实验室时，可以使用这些图像版本。
- **支持跨教室实验室共享和重用模板 VM 映像**

    您可以保存和重用映像，这样每次创建新的教室实验室时都不必从头开始配置映像。 例如，如果提供多个类需要相同的映像，则只需创建一次此映像并将其导出到共享图像库，即可跨教室实验室共享。
- **通过复制确保映像可用性**

    当您从教室实验室保存到共享图像库时，您的图像将自动复制到[同一地理位置中的其他区域](https://azure.microsoft.com/global-infrastructure/regions/)。 如果区域出现中断，则将映像发布到教室实验室不会受到影响，因为可以使用来自另一个区域的图像副本。  从多个副本发布 VM 也有助于提高性能。

要对共享映像进行逻辑分组，您有几个选项：

- 创建多个共享图像库。 每个实验室帐户只能连接到一个共享图像库，因此此选项还需要创建多个实验室帐户。
- 或者，您可以使用由多个实验室帐户共享的单个共享图像库。 在这种情况下，每个实验室帐户只能启用适用于它所包含的教室实验室的图像。

## <a name="naming"></a>命名
开始使用 Azure 实验室服务时，我们建议您为资源组、实验室帐户、教室实验室和共享映像库建立命名约定。 虽然您建立的命名约定将针对组织的需求而独一无二，但下表概述了一般准则。

| 资源类型 | 角色 | 建议的模式 | 示例 |
| ------------- | ---- | ----------------- | -------- | 
| 资源组 | 包含一个或多个实验室帐户和一个或多个共享图像库 | \<组织短名称\>-\<环境\>-rg<ul><li>**组织短名称**标识资源组支持的组织名称</li><li>**环境**标识资源的环境，如试验或生产</li><li>**Rg**代表资源类型：资源组。</li></ul> | 孔索大学实验室-rg<br/>康托索大学实验室-飞行员-rg<br/>孔索大学实验室-prod-rg |
| 实验室帐户 | 包含一个或多个实验室 | \<组织短名称\>-\<环境\>-la<ul><li>**组织短名称**标识资源组支持的组织名称</li><li>**环境**标识资源的环境，如试验或生产</li><li>**La**代表资源类型：实验室帐户。</li></ul> | 孔索大学实验室-拉<br/>数学计算实验室-拉<br/>科学实验室-飞行员-拉<br/>科学实验室-prod-la |
| 课堂实验室 | 包含一个或多个 VM |\<类名称\>-\<时间\>-帧\<教育者标识符\><ul><li>**类名称**标识实验室支持的类的名称。</li><li>**时间范围**标识课程的提供时间范围。</li>**教育标识符**标识拥有实验室的教育工作者。</li></ul> | CS1234-秋天2019-约翰多<br/>CS1234-弹簧2019-约翰多 | 
| 共享映像库 | 包含一个或多个 VM 映像版本 | \<组织短名称\>库 | 孔索大学实验室 |

有关命名其他 Azure 资源的详细信息，请参阅[为 Azure 资源命名约定](/azure/architecture/best-practices/naming-conventions)。

## <a name="regionslocations"></a>区域\位置

设置 Azure 实验室服务的资源时，需要提供承载资源的数据中心的区域（或位置）。 以下是有关区域如何影响设置实验室所涉及的每个资源的详细信息。

### <a name="resource-group"></a>资源组

该区域指定存储有关资源组信息的数据中心。 资源组中包含的 Azure 资源可以位于与其父资源不同的区域中。

### <a name="lab-account"></a>实验室帐户

实验室帐户的位置指示此资源存在的区域。  

### <a name="classroom-lab"></a>课堂实验室
    
教室实验室所在的位置因以下因素而异：

  - **实验室帐户与虚拟网络 （VNet） 对等**
  
    实验室帐户位于同一区域时，可以使用[VNet 对等](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)互连。  当实验室帐户与 VNet 对等时，教室实验室将自动创建在与实验室帐户和 VNet 相同的区域中。

    > [!NOTE]
    > 当实验室帐户与 VNet 对等时，将禁用**允许实验室创建者选择实验室位置**的设置。 有关此设置的其他信息，请参阅文章：[允许实验室创建者选择实验室的位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)。
    
  - **没有 VNet 是对等的 ***，并且***不允许实验室创建者选择实验室位置**
  
    当**没有**VNet 与实验室帐户对等 *，并且*[实验室创建者**不允许**选择实验室位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)时，教室实验室将自动在具有可用 VM 容量的区域创建。  具体而言，Azure 实验室服务查找[与实验室帐户位于同一地理位置的区域](https://azure.microsoft.com/global-infrastructure/regions)的可用性。

  - **没有 VNet 对等 ***，实验室***创建者可以选择实验室位置**
       
    当**没有**VNet 对等和[实验室创建者可以选择实验室位置](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)时，实验室创建者可以选择的位置将基于可用容量。

常规规则是将资源的区域设置为最接近其用户的区域。 对于课堂实验室，这意味着创建离学生最近的教室实验室。 对于学生遍布世界各地的在线课程，您需要使用最佳判断力创建位于中心位置的教室实验室。 或者，根据学生区域将班级拆分为多个教室实验室。

### <a name="shared-image-gallery"></a>共享映像库

该区域指示在将第一个图像版本自动复制到目标区域之前存储第一个图像版本的源区域。

## <a name="vm-sizing"></a>VM 大小调整
当管理员或实验室创建者创建教室实验室时，他们可以根据教室的需要从以下 VM 大小中进行选择。 请记住，可用的计算大小取决于您的实验室帐户所在的区域：

| 大小 | 规格 | 系列 | 建议使用 |
| ---- | ----- | ------ | ------------- |
| 小型| <ul><li>双核</li><li>3.5 GB 内存</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最适合命令行、打开 Web 浏览器、低流量 Web 服务器、中小型数据库。 |
| 中型 | <ul><li>4 核心</li><li>7 GB 内存</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最适合关系数据库、内存缓存和分析。 |
| 中等（嵌套虚拟化） | <ul><li>4 核心</li><li>16 GB 内存</li></ul> | [Standard_DC4s_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最适合关系数据库、内存缓存和分析。  此大小还支持嵌套虚拟化。 |
| 大型 | <ul><li>8 核</li><li>32 GB 内存</li></ul>  | [Standard_DC8_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 此大小最适合需要更快 CPU、更好的本地磁盘性能、大型数据库和大型内存缓存的应用程序。  此大小还支持嵌套虚拟化。 |
| 小型 GPU（可视化） | <ul><li>6 核心</li><li>56 GB 内存</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | 此大小最适合使用 OpenGL 和 DirectX 等框架进行远程可视化、流式传输、游戏和编码。 |
| 小型 GPU（计算） | <ul><li>6 核心</li><li>56 GB 内存</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |此尺寸最适合计算机密集型应用，如人工智能和深度学习。 |
| 中型 GPU（可视化） | <ul><li>12 核心</li><li>112 GB 内存</li></ul>  | [Standard_NC12](https://docs.microsoft.com/azure/virtual-machines/nc-series) | 此大小最适合使用 OpenGL 和 DirectX 等框架进行远程可视化、流式传输、游戏和编码。 |

## <a name="manage-identity"></a>管理标识
使用[Azure 基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)，可以分配以下角色以授予对实验室帐户和教室实验室的访问权限：

- **实验室帐户所有者**

    创建实验室帐户的管理员将自动添加到实验室**帐户的所有者角色**。  分配**所有者角色的管理员**可以：
     - 更改实验室帐户的设置。
     - 授予其他管理员作为所有者或贡献者对实验室帐户的访问权限。 
     - 使教育工作者能够作为创建者、所有者或贡献者访问教室实验室。
     - 在实验室帐户中创建和管理所有教室实验室。

- **实验室帐户参与者**

    分配了 **"参与者"** 角色的管理员可以：
    - 更改实验室帐户的设置。
    - 创建和管理实验室帐户中的所有教室实验室。
    
    但是，*它们不能*允许其他用户访问实验室帐户或教室实验室。

- **课堂实验室创建者**

    要在实验室帐户中创建教室实验室，教育者必须是**实验室创建者**角色的成员。  当教师创建教室实验室时，他们将自动添加为实验室的所有者。  请参阅有关如何[将用户添加到**实验室创建者**角色的](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)教程。 

- **课堂实验室所有者\贡献者**
  
    当教室实验室是实验室**的所有者**或**参与者**角色的成员时，教师可以查看和更改教室实验室的设置;他们还必须是实验室帐户的**读取器**角色的成员。

    实验室**的所有者**角色和**参与者**角色之间的一个关键区别是，参与者*无法*授予其他用户管理实验室的访问权限 - 只有所有者才能授予其他用户管理实验室的访问权限。
    
    此外，教育者*不能*创建新的教室实验室，除非他们也是**实验室创建者**角色的成员。

- **共享图像库**
    
    将共享图像库附加到实验室帐户时，实验室帐户所有者、参与者和实验室创建者\所有者\贡献者将自动获得在库中查看和保存图像的访问权限。 

以下是一些帮助分配角色的提示：
   - 通常，只有管理员应该是实验室帐户**的所有者**或**参与者**角色的成员;您可能有多个所有者\贡献者。

   - 使教育工作者能够创建新的课堂实验室并管理他们创建的实验室;您只需分配对**实验室创建者**角色的访问权限。
   
   - 使教育者能够管理特定的课堂实验室，*但不能*使教师能够创建新的实验室;您应该为他们将管理的每个教室实验室分配对 **"所有者**"或 **"参与者"** 角色的访问。  例如，您可能希望允许教授和助教共同拥有教室实验室。  请参阅有关如何[将用户作为所有者添加到教室实验室的](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner)指南。

## <a name="pricing"></a>定价

### <a name="azure-lab-services"></a>Azure 实验室服务
Azure 实验室服务的定价在以下文章中介绍[：Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

如果您计划使用它存储和管理映像版本，则还需要考虑共享映像库的定价。 

### <a name="shared-image-gallery"></a>共享映像库
创建共享图像库并将其附加到实验室帐户是免费的。 在将图像版本保存到库中之前，不会产生成本。 通常，使用共享映像库的定价可以忽略不计，但了解如何计算它非常重要，因为它未包含在 Azure 实验室服务的定价中。  

#### <a name="storage-charges"></a>存储费用
要存储映像版本，共享映像库使用标准 HDD 管理的磁盘。 使用的 HDD 托管磁盘的大小取决于要存储的图像版本的大小。 请参阅以下文章以查看定价：[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。


#### <a name="replication-and-network-egress-charges"></a>复制和网络出口费用
使用教室实验室的模板虚拟机 （VM） 保存映像版本时，Azure 实验室服务首先将其存储在源区域中，然后自动将源映像版本复制到一个或多个目标区域。 请务必注意，Azure 实验室服务会自动将源映像版本复制到教室实验室所在的[地理位置中的所有目标区域](https://azure.microsoft.com/global-infrastructure/regions/)。 例如，如果您的教室实验室位于美国地理中，则图像版本将复制到美国境内存在的八个区域中的每个区域。

当图像版本从源区域复制到其他目标区域时，将发生网络出口费用。 所收取的金额基于图像数据最初从源区域向外传输时的图像版本的大小。  有关定价详细信息，请参阅以下文章：[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。

[教育解决方案](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3)客户可以免除支付出口费用。 与您的客户经理交谈以了解更多信息。  有关详细信息，请参阅链接文档中的**常见问题**部分，特别是"学术客户存在哪些数据传输计划以及如何获得资格？"

#### <a name="pricing-example"></a>定价示例
为了回顾上述定价，让我们看一下将模板 VM 映像保存到共享映像库的示例。 假设以下方案：

- 您有一个自定义 VM 映像。
- 您正在保存映像的两个版本。
- 您的实验室位于美国，共有八个区域。
- 每个映像版本的大小为 32 GB;因此，硬盘管理的磁盘价格为每月 1.54 美元。

总成本估计为：

映像数 = 版本数 + 副本数 = 托管磁盘价格

在此示例中，成本为：

1 个自定义映像 （32 GB） x 2 个版本 x 8 个美国区域 x $1.54 = 每月 24.64 美元

#### <a name="cost-management"></a>成本管理
对于实验室帐户管理员来说，通过定期从库中删除不需要的图像版本来管理成本非常重要。 

不应删除对特定区域的复制，以降低成本（此选项存在于共享映像库中）。 复制更改可能会对 Azure 实验室服务从保存在共享映像库中的图像发布 VM 的能力产生不利影响。

## <a name="next-steps"></a>后续步骤
有关创建实验室帐户和实验室的分步说明，请参阅本教程：[设置指南](tutorial-setup-lab-account.md)
