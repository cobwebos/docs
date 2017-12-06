---
title: "发现和评估要使用 Azure Migrate 迁移到 Azure 的本地 VMware VM | Microsoft Docs"
description: "介绍如何发现和评估要使用 Azure Migrate 迁移到 Azure 的本地 VMware VM。"
services: migration-planner
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a2521630-730f-4d8b-b298-e459abdced46
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 1c21364c3ff5cfb61866c912a699b722f2668607
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>发现和评估要迁移到 Azure 的本地 VMware VM

[Azure Migrate](migrate-overview.md) 服务会评估要迁移到 Azure 的本地工作负荷。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 Azure Migrate 项目。
> * 设置一个本地收集器虚拟机 (VM)，用于发现要评估的本地 VMware VM。
> * 将 VM 分组并创建评估。


如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>先决条件

- **VMware**：运行版本 5.0 或更高版本的 ESXi 主机或群集上至少需要一个 VMware VM。 主机或群集必须由运行版本 5.5、6.0 或 6.5 的 vCenter 服务器托管。
- **vCenter 帐户**：需有一个对 vCenter 服务器拥有管理员凭据的只读帐户。 Azure Migrate 将使用此帐户发现 VM。
- **权限**：在 vCenter 服务器上，需要有权通过导入 .OVA 格式的文件来创建 VM。 
- **统计信息设置**：开始部署之前，应将 vCenter 服务器的统计信息设置指定为级别 3。 如果低于级别 3，可以正常评估，但不会收集存储和网络的性能数据。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户
登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-project"></a>创建一个项目

1. 在 Azure 门户中，单击“创建资源”。
2. 搜索 **Azure Migrate**，然后在搜索结果中选择服务“Azure Migrate (预览版)”。 然后单击“创建”。
3. 指定项目名称和项目的 Azure 订阅。
4. 创建新的资源组。
5. 指定创建项目的区域，单击“创建”。 从本地 VM 收集的元数据将存储在此区域中。 对于此预览版，只能在“美国中西部”区域创建 Azure Migrate 项目。 但是，可以评估不同位置的 VM。

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>下载收集器设备

Azure Migrate 会创建一个称作收集器设备的本地 VM。 此 VM 可发现本地 VMware VM，并将其相关元数据发送到 Azure Migrate 服务。 若要设置收集器设备，请下载一个 .OVA 文件，并将其导入到本地 vCenter 服务器以创建 VM。

1. 在 Azure Migrate 项目中，单击“开始” > “发现和评估” > “发现计算机”。
2. 在“发现计算机”中，单击“下载”以下载 .OVA 文件。
3. 在“复制项目凭据”中，复制项目 ID 和密钥。 在配置收集器时要使用这些信息。

    ![下载 .ova 文件](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>验证收集器设备

在部署 .OVA 文件之前请检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 OVA 的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 生成的哈希应匹配这些设置。
    
    适用于 OVA 版本 1.0.8.38
    **算法** | **哈希值**
    --- | ---
    MD5 | dd27dd6ace28f9195a2b5d52a4003067 
    SHA1 | d2349e06a5d4693fc2a1c0619591b9e45c36d695
    SHA256 | 1492a0c6d6ef76e79269d5cd6f6a22f336341e1accbc9e3dfa5dad3049be6798

    适用于 OVA 版本 1.0.8.40
    **算法** | **哈希值**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>创建收集器 VM

将下载的文件导入 vCenter 服务器。

1. 在 vSphere 客户端控制台中，单击“文件” > “部署 OVF 模板”。

    ![部署 OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. 在“部署 OVF 模板向导”>“源”中，指定 .ova 文件的位置。
3. 在“名称”和“位置”中，为收集器 VM 指定一个友好名称，以及要托管 VM 的库存对象。
5. 在“主机/群集”中，指定要在其上运行收集器 VM 的主机或群集。
7. 在存储中，指定收集器 VM 的存储目标。
8. 在“磁盘格式”中，指定磁盘类型和大小。
9. 在“网络映射”中，指定收集器 VM 要连接到的网络。 网络需要与 Internet 建立连接才能向 Azure 发送元数据。 
10. 检查并确认设置，然后单击“完成”。

## <a name="run-the-collector-to-discover-vms"></a>运行收集器以发现 VM。

1. 在 vSphere 客户端控制台中，右键单击“VM”>“打开控制台”。
2. 提供设备的语言、时区和密码首选项。
3. 在桌面上，单击“运行收集器”快捷方式。
4. 在 Azure Migrate 收集器中，打开“设置必备组件”。
    - 接受许可条款，并阅读第三方信息。
    - 收集器将会检查 VM 是否可访问 Internet。
    - 如果 VM 通过代理访问 Internet，请单击“代理设置”，并指定代理地址和侦听端口。 如果代理需要身份验证，请指定凭据。
    - 收集器将会检查 Windows 探查器服务是否正在运行。 该服务默认安装在收集器 VM 上。
    - 下载并安装 VMware PowerCLI。
。 在“发现计算机”中执行以下操作：
    - 指定 vCenter 服务器的名称 (FQDN) 或 IP 地址。
    - 在“用户名称”和“密码”中，指定收集器用来发现 vCenter 服务器上的 VM 的只读帐户凭据。
    - 在“收集范围”中，选择 VM 发现的范围。 收集器只能发现指定范围内的 VM。 可将范围设置为特定文件夹、数据中心或群集。 包含的范围不应超过 1000 个 VM。 
    - 在“用于分组的标记类别”中，选择“无”。
1. 在“选择项目”中，指定从门户复制的 Azure Migrate 项目 ID 和密钥。 如果未复制这些信息，请从收集器 VM 中打开 Azure 门户。 在项目“概述”页中，单击“发现计算机”，然后复制结果。  
2. 在“完成发现”中，监视发现过程，并检查从 VM 中收集的元数据是否在范围内。 收集器提供一个近似的发现时间。

> [!NOTE]
> 收集器仅支持使用“英语(美国)”作为操作系统语言和收集器界面语言。 即将支持更多语言。


### <a name="verify-vms-in-the-portal"></a>在门户中验证 VM

发现所需的时间取决于所发现的 VM 数。 一般情况下，在收集器完成运行后，发现 100 个 VM 大概需要一个小时的时间来完成。 

1. 在 Migration Planner 项目中，单击“管理” > “计算机”。
2. 检查想要发现的 VM 是否出现在门户中。


## <a name="create-and-view-an-assessment"></a>创建和查看评估

发现 VM 后，可将其分组并创建评估。 

1. 在项目的“概述”页中，单击“+ 创建评估”。
2. 单击“查看所有”查看评估设置。
3. 创建组并指定组名称。
4. 选择要添加到该组的计算机。
5. 单击“创建评估”，以创建该组和评估。
6. 创建评估后，在“概述” > “仪表板”中查看该评估。
7. 单击“导出评估”，将评估下载为 Excel 文件。

### <a name="sample-assessment"></a>示例评估

下面是一份示例评估报告。 其中包括有关 VM 是否与 Azure 兼容，以及每月估算费用的信息。 

![评估报告](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Azure 迁移就绪性

此视图显示每台计算机的就绪状态。

- 对于准备就绪的 VM，Azure Migrate 会推荐一个适合 Azure 的 VM 大小。
- 对于未准备就绪的 VM，Azure Migrate 会解释原因，并提供补救步骤。
- Azure Migrate 会推荐可用于迁移的工具。 如果计算机适合直接迁移，则建议使用 Azure Site Recovery 服务。 对于数据库计算机，建议使用 Azure 数据库迁移服务。

  ![评估就绪](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>每月估算费用

此视图显示每台计算机的计算和存储费用。 该费用是使用针对计算机及其磁盘建议的基于性能的大小以及评估属性估算的。

将会对于组中的所有 VM 合计计算和存储的每月估算费用。 可以单击每台计算机深入查看详细信息。 

![评估 VM 费用](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

可以向下钻取以查看特定计算机的费用。

![评估 VM 费用](./media/tutorial-assessment-vmware/assessment-vm-drill.png) 

## <a name="next-steps"></a>后续步骤

- [了解](how-to-scale-assessment.md)如何设置大量本地计算机的评估。
- 了解如何使用[计算机依赖关系映射](how-to-create-group-machine-dependencies.md)创建更详细的评估组。
- [详细了解](concepts-assessment-calculation.md)评估计算方式。
