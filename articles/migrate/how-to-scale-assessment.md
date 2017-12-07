---
title: "使用 Azure Migrate 执行规模发现和评估 | Microsoft 文档"
description: "介绍如何使用 Azure Migrate 服务访问大量本地计算机。"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dde0d07f-94b7-4b6a-a158-a89aa9324a35
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 930ec182cf329e7dda072dc49bd7f70abb413f2d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>发现和评估大型 VMware 环境

本文介绍如何使用 [Azure Migrate](migrate-overview.md) 访问大量本地计算机。 Azure Migrate 可评估计算机来检查其是否适合迁移到 Azure，并提供在 Azure 中运行计算机的大小和成本估计。

## <a name="prerequisites"></a>先决条件

- VMware：至少必须具有一个 VMware VM 位于运行版本 5.0 或更高版本的 ESXi 主机或群集上。 主机或群集必须由运行版本 5.5 或 6.0 的 vCenter 服务器托管。
- vCenter 帐户：需要具有一个对 vCenter 服务器拥有管理员凭据的只读帐户。 Azure Migrate 将使用此帐户发现 VM。
- 权限：在 vCenter 服务器上，需要有通过导入 .OVA 格式的文件来创建 VM 的权限。
- 统计信息设置：开始部署前，vCenter 服务器的统计信息设置应设置为级别 2 或更高级别。

## <a name="plan-azure-migrate-projects"></a>计划 Azure Migrate 项目

一个 Azure Migrate 项目可以评估最多 1500 台计算机。 项目中的单个发现可以发现最多 1000 台计算机。

- 如果要发现的计算机少于 1000 台，则需要使用含有单个发现的单个项目。
- 如果要发现的计算机介于 1000 到 1500 台之间，则需要使用含有两个发现的单个项目。
- 如果要发现的计算机超过 1500 台，则需要创建多个项目，并根据你的需求执行多个发现。 例如：
    - 如果要发现的计算机为 3000 台，则可以设置含有两个发现的两个项目，或含有单个发现的三个项目。
    - 如果要发现的计算机为 5000 台，则可以设置四个项目。 三个可以发现 1500 台计算机的项目，一个可以发现 500 台计算机的项目。 或者，也可以设置含有单个发现的五个项目。 
- 在 Azure Migrate 中执行发现操作时，可以将发现范围设置为 VMware 文件夹、数据中心或群集。
- 若要执行多个发现操作，请在 vCenter 中验证想要发现的 VM 是否位于支持 1000 台计算机限制的文件夹、数据中心或群集中。
- 出于评估的需要，我们建议让计算机在相同的项目和评估中保持相互依赖关系。 因此，在 vCenter 中，出于评估需要，请确保依赖计算机位于同一文件夹、数据中心或群集中。


## <a name="create-a-project"></a>创建一个项目

根据你的需要创建 Azure Migrate 项目。

1. 在 Azure 门户中，单击“创建资源”。
2. 搜索“Azure Migrate”，然后在搜索结果中选择服务“Azure Migrate（预览版）”。 然后单击“创建”。
3. 指定项目名称和项目的 Azure 订阅。
4. 创建新的资源组。
5. 指定创建项目的区域，然后单击“创建”。 从本地 VM 收集的元数据将存储在此区域中。

## <a name="set-up-the-collector-appliance"></a>设置收集器设备

Azure Migrate 将创建一个名为收集器设备的本地 VM。 此 VM 用于发现本地 VMware VM，并将有关其元数据发送到 Azure Migrate 服务。 若要设置收集器设备，需要下载 .OVA 文件，并将其导入到本地 vCenter 服务器，以创建 VM。

### <a name="download-the-collector-appliance"></a>下载收集器设备

如果你有多个项目，则只需将收集器设备一次下载到 vCenter 服务器即可。 下载和设置设备后，为每个项目运行该设备，并指定唯一项目 ID 和密钥。

1. 在 Azure Migrate 项目中，单击“入门” > “发现和评估” > “发现计算机”。
2. 在“发现计算机”中，单击“下载”以下载 .OVA 文件。
3. 在“复制项目凭据”中，请复制项目的 ID 和密钥。 在配置收集器时要使用这些信息。

   
### <a name="verify-the-collector-appliance"></a>验证收集器设备

在部署 .OVA 文件前请检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 OVA 的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 生成的哈希应匹配这些设置。

    **算法** | **哈希值**
    --- | ---
    MD5 | c283f00f46484bf673dc8383b01d0741 
    SHA1 | 8f49b47f53d051af1780bbc725659ce64e717ed4
    SHA256 | 7aecdbdb2aea712efe99d0c1444503f52d16de5768e783465e226fbbca71501d

## <a name="create-the-collector-vm"></a>创建收集器 VM

将下载的文件导入到 vCenter 服务器。

1. 在 vSphere 客户端控制台中，单击“文件” > “部署 OVF 模板”。

    ![部署 OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. 在“部署 OVF 模板向导”>“源”中，指定 .ova 文件的位置。
3. 在“名称”和“位置”中，为收集器 VM 指定一个友好名称，以及要托管 VM 的清单对象。
5. 在“主机/群集”中，指定收集器 VM 将运行的主机或群集。
7. 在存储中，指定收集器 VM 的存储目标。
8. 在“磁盘格式”中，指定磁盘类型和大小。
9. 在“网络映射”中，指定收集器 VM 将连接到的网络。 网络需要 Internet 连接，以向 Azure 发送元数据。 
10. 查看并确认设置，然后单击“完成”。

## <a name="identify-the-key-and-id-for-each-project"></a>标识每个项目的密钥和 ID

如果具有多个项目，请确保标识每个项目的 ID 和密钥。 运行收集器来发现 VM 时，需要使用密钥。

1. 在项目中，单击“入门” > “发现和评估” > “发现计算机”。
2. 在“复制项目凭据”中，请复制项目的 ID 和密钥。 
    ![项目 ID](./media/how-to-scale-assessment/project-id.png)

## <a name="vcenter-statistics-level-to-collect-the-performance-counters"></a>收集性能计数器的 vCenter 统计信息级别
以下是在执行发现操作期间所收集的计数器的列表。 默认情况下，该计数器适用于 vCenter 服务器中的各种级别。 我们建议将统计信息级别设置为最高的常用级别（级别 3），以便正确收集所有计数器。 如果将 vCenter 设置的级别较低，则可能只完整收集几个计数器，而其他的计数器的收集数为 0。 因此，评估可能会显示不完整的数据。 下表还列出了在未收集某个特定计数器的情况下将会受到影响的评估结果。

|计数器                                  |级别    |每个设备级别  |评估影响                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |不可用                |建议的 VM 大小和成本                    |
|mem.usage.average                        | 1       |不可用                |建议的 VM 大小和成本                    |
|virtualDisk.read.average                 | #N/A       |#N/A                 |磁盘大小、存储成本和 VM 大小         |
|virtualDisk.write.average                | #N/A       |#N/A                 |磁盘大小、存储成本和 VM 大小         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |磁盘大小、存储成本和 VM 大小         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |磁盘大小、存储成本和 VM 大小         |
|net.received.average                     | #N/A       |3                 |VM 大小和网络成本                        |
|net.transmitted.average                  | #N/A       |3                 |VM 大小和网络成本                        |

> [!WARNING]
> 如果刚设置了更高的统计信息级别，将需要最多一天的时间来生成性能计数器。 因此，我们建议你在一天后运行发现操作。

## <a name="run-the-collector-to-discover-vms"></a>运行收集器以发现 VM。

对于需要执行的每个发现，请运行收集器以在所需的范围内发现 VM。 请依次运行发现。 不支持并发执行发现，每个发现必须具有不同的范围。

1. 在 vSphere 客户端控制台中，右键单击“VM”>“打开控制台”。
2. 提供设备的语言、时区和密码首选项。
3. 在桌面上，单击“运行收集器”快捷方式。
4. 在 Azure Migrate 收集器中，打开“设置先决条件”。
    - 接受许可条款，然后阅读第三方信息。
    - 收集器检查 VM 是否具有 Internet 访问权限。
    - 如果 VM 通过代理访问 Internet，请单击“代理设置”，并指定代理地址和侦听端口。 如果代理需要身份验证，请指定凭据。
    - 收集器检查 Windows 探查器服务是否正在运行。 该服务默认安装在收集器 VM 上。
    - 下载并安装 VMware PowerCLI。
。 在“发现计算机”中，执行以下操作：
    - 指定 vCenter 服务器的名称 (FQDN) 或 IP 地址。
    - 在“用户名称”和“密码”中，指定收集器将用于发现 vCenter 服务器上的 VM 的只读帐户凭据。
    - 在“收集范围”中，选择 VM 发现的范围。 收集器只能发现指定范围内的 VM。 可将范围设置为特定文件夹、数据中心或群集。 它不应包含超过 1000 台 VM。 
    - n 用于分组的标记类别，选择“无”。

        ![选择范围](./media/how-to-scale-assessment/select-scope.png)

1. 在“选择项目”中，指定项目的 ID 和密钥。 如果未复制这些信息，请从收集器 VM 中打开 Azure 门户。 在项目“概述”页中，单击“发现计算机”，然后复制结果。  
在“完成发现”中，监视发现过程，并检查从 VM 中收集的元数据是否在范围内。 收集器提供的是一个近似的发现时间。


### <a name="verify-vms-in-the-portal"></a>在门户中验证 VM

发现时间要取决于所发现的 VM 数。 通常情况下，对于 100 台 VM，在收集器完成运行后，大概需要一个小时的时间来完成发现。 

1. 在迁移规划工具项目中，单击“管理” > “计算机”。
2. 检查想要发现的 VM 是否出现在门户中。


## <a name="next-steps"></a>后续步骤

- 了解如何为评估[创建组](how-to-create-a-group.md)。
- [详细了解](concepts-assessment-calculation.md)如何计算评估。