---
title: "使用 Azure Migrate 执行批量发现和评估 | Microsoft Docs"
description: "介绍如何使用 Azure Migrate 服务访问大量本地计算机。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/08/2018
ms.author: raynew
ms.openlocfilehash: 67661e03e65cde3ec2f1aafd5ef755899cf0c77b
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2018
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>发现和评估大型 VMware 环境

本文介绍如何使用 [Azure Migrate](migrate-overview.md) 访问大量本地虚拟机 (VM)。 Azure 迁移将评估计算机，检查其是否适合迁移到 Azure。 此服务可提供在 Azure 中运行计算机的大小和成本估计。

## <a name="prerequisites"></a>系统必备

- **VMware**：计划迁移的 VM 必须由版本 5.5、6.0 或 6.5 的 vCenter Server 托管。 此外，需要一个运行 5.0 或更高版本的 ESXi 主机来部署收集器 VM。
- **vCenter 帐户**：需要只读帐户来访问 vCenter Server。 Azure Migrate 使用此帐户发现本地 VM。
- **权限**：在 vCenter Server 中，需要有通过导入 .OVA 格式的文件来创建 VM 的权限。
- **统计信息设置**：开始部署之前，应将 vCenter Server 的统计信息设置指定为级别 3。 如果低于级别 3，可以正常评估，但不会收集存储和网络的性能数据。 这种情况的大小建议基于 CPU 和内存的性能数据以及磁盘和网络适配器的配置数据。

## <a name="plan-azure-migrate-projects"></a>计划 Azure Migrate 项目

基于以下限制计划发现和评估：

| **实体** | **计算机限制** |
| ---------- | ----------------- |
| Project    | 1,500              | 
| 发现  | 1,000              |
| 评估 | 400               |

- 如果要发现和评估的计算机少于 400 台，则需要使用单个项目和单次发现。 根据具体要求，可以在单次评估中评估所有计算机，也可将机拆分到多次评估。 
- 如果要发现的计算机介于 400 到 1,000 台之间，则需要使用单个项目和单次发现。 但需要通过多次评估来评估这些计算机，因为单次评估只能容纳至多 400 台计算机。
- 如果要发现的计算机介于 1,001 到 1,500 台之间，则需要使用包含两次发现的单个项目。
- 如果要发现的计算机超过 1,500 台，则需要创建多个项目，并根据需求执行多次发现。 例如：
    - 如果要发现的计算机为 3,000 台，可以设置两个包含两次发现的项目，或三个包含单次发现的项目。
    - 如果拥有 5,000 台计算机，可以设置 4 个项目：其中两个分别发现 1,500 台计算机，另外两个分别发现 500 台计算机。 或者，也可以设置含有单次发现的 5 个项目。 

## <a name="plan-multiple-discoveries"></a>计划多次发现

可以使用同一 Azure Migrate 收集器，对一个或多个项目进行多次发现。 请记住以下计划注意事项：
 
- 使用 Azure Migrate 收集器进行发现时，可将发现范围设置为 vCenter Server 文件夹、数据中心、群集或主机。
- 若要执行多个发现操作，请在 vCenter Server 中验证要发现的 VM 是否位于支持 1,000 台计算机限制的文件夹、数据中心、群集或主机中。
- 出于评估需要，建议让计算机在相同的项目和评估中保持相互依赖关系。 在 vCenter Server 中，出于评估需要，请确保依赖计算机位于同一文件夹、数据中心或群集中。


## <a name="create-a-project"></a>创建一个项目

根据具体需求创建 Azure Migrate 项目：

1. 在 Azure 门户中，选择“创建资源”。
2. 搜索 Azure Migrate，然后在搜索结果中选择服务“Azure Migrate (预览版)”。 然后选择“创建”。
3. 指定项目名称和项目的 Azure 订阅。
4. 创建新的资源组。
5. 指定要在其中创建项目的位置，然后选择“创建”。 请注意，仍可针对其他目标位置评估 VM。 将在为项目指定的位置存储从本地 VM 收集的元数据。

## <a name="set-up-the-collector-appliance"></a>设置收集器设备

Azure Migrate 会创建一个称作收集器设备的本地 VM。 此 VM 可发现本地 VMware VM，并将其相关元数据发送到 Azure Migrate 服务。 若要设置收集器设备，请下载 .OVA 文件，并将其导入到本地 vCenter Server 实例。

### <a name="download-the-collector-appliance"></a>下载收集器设备

如果有多个项目，只需将收集器设备下载到 vCenter 服务器一次。 下载和设置设备后，为每个项目运行该设备，并指定唯一项目 ID 和密钥。

1. 在 Azure Migrate 项目中，选择“开始” > “发现和评估” > “发现计算机”。
2. 在“发现计算机”中，选择“下载”以下载 .OVA 文件。
3. 在“复制项目凭据”中，请复制项目的 ID 和密钥。 在配置收集器时要使用这些信息。

   
### <a name="verify-the-collector-appliance"></a>验证收集器设备

在部署 .OVA 文件之前请检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 OVA 的哈希：

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   用法示例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 确保生成的哈希与以下设置匹配。
 
    适用于 OVA 版本 1.0.8.49

    **算法** | **哈希值**
    --- | ---
    MD5 | 8779eea842a1ac465942295c988ac0c7
    SHA1 | c136c52a0f785e1fd98865e16479dd103704887d
    SHA256 | 5143b1144836f01dd4eaf84ff94bc1d2c53f51ad04b1ca43ade0d14a527ac3f9

    适用于 OVA 版本 1.0.8.40：

    **算法** | **哈希值**
    --- | ---
    MD5 |afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>创建收集器 VM

将下载的文件导入 vCenter Server：

1. 在 vSphere 客户端控制台中，选择“文件” > “部署 OVF 模板”。

    ![部署 OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. 在“部署 OVF 模板向导”>“源”中，指定 OVA 文件的位置。
3. 在“名称”和“位置”中，为收集器 VM 指定一个友好名称，以及要托管 VM 的库存对象。
5. 在“主机/群集”中，指定要在其上运行收集器 VM 的主机或群集。
7. 在存储中，指定收集器 VM 的存储目标。
8. 在“磁盘格式”中，指定磁盘类型和大小。
9. 在“网络映射”中，指定收集器 VM 要连接到的网络。 网络需要与 Internet 建立连接才能向 Azure 发送元数据。 
10. 检查并确认设置，然后选择“完成”。

## <a name="identify-the-id-and-key-for-each-project"></a>标识每个项目的 ID 和密钥

如果拥有多个项目，请务必标识每个项目的 ID 和密钥。 运行收集器来发现 VM 时，需要使用密钥。

1. 在项目中，选择“入门” > “发现和评估” > “发现计算机”。
2. 在“复制项目凭据”中，请复制项目的 ID 和密钥。 
    ![复制项目凭据](./media/how-to-scale-assessment/copy-project-credentials.png)

## <a name="set-the-vcenter-statistics-level"></a>设置 vCenter 统计信息级别
下面是发现期间收集的性能计数器的列表。 这些计数器在 vCenter Server 中的各种级别默认可用。 

建议将统计信息级别设置为最高常用级别 (3)，以便正确收集所有计数器。 如果将 vCenter 设置的级别较低，则可能只完整收集几个计数器，而其他的计数器的收集数为 0。 这样，评估可能会显示不完整的数据。 

下表还列出了不收集特定计数器时将受到影响的评估结果。

|计数器                                  |级别    |设备级别  |评估影响                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |不可用                |建议的 VM 大小和成本                    |
|mem.usage.average                        | 1       |不可用                |建议的 VM 大小和成本                    |
|virtualDisk.read.average                 | 2       |2                 |磁盘大小、存储成本和 VM 大小         |
|virtualDisk.write.average                | 2       |2                 |磁盘大小、存储成本和 VM 大小         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |磁盘大小、存储成本和 VM 大小         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |磁盘大小、存储成本和 VM 大小         |
|net.received.average                     | 2       |3                 |VM 大小和网络成本                        |
|net.transmitted.average                  | 2       |3                 |VM 大小和网络成本                        |

> [!WARNING]
> 如果刚设置了更高的统计信息级别，将需要最多一天的时间来生成性能计数器。 因此，建议在一天后运行发现。

## <a name="run-the-collector-to-discover-vms"></a>运行收集器以发现 VM。

对于需要执行的每次发现，请运行收集器以发现所需范围内的 VM。 请依次运行发现。 不支持并发执行发现，每个发现必须具有不同的范围。

1. 在 vSphere 客户端控制台中，右键单击“VM”>“打开控制台”。
2. 提供设备的语言、时区和密码首选项。
3. 在桌面上，选择“运行收集器”快捷方式。
4. 在 Azure Migrate 收集器中，打开“设置先决条件”然后：

   a. 接受许可条款，并阅读第三方信息。

   收集器将会检查 VM 是否可访问 Internet。
   
   b. 如果 VM 通过代理访问 Internet，请选择“代理设置”，并指定代理地址和侦听端口。 如果代理需要身份验证，请指定凭据。

   收集器将检查 collectorservice 是否正在运行。 该服务默认安装在收集器 VM 上。

   c. 下载并安装 VMware PowerCLI。

5. 在“指定 vCenter Server 详细信息”中，执行以下操作：
    - 指定 vCenter Server 的名称 (FQDN) 或 IP 地址。
    - 在“用户名称”和“密码”中，指定收集器用来发现 vCenter Server 中 VM 的只读帐户凭据。
    - 在“选择范围”中，选择 VM 发现的范围。 收集器只能发现指定范围内的 VM。 可将范围设置为特定文件夹、数据中心或群集。 它不应包含超过 1000 台 VM。 

6. 在“指定迁移项目”中，指定项目的 ID 和密钥。 如果未复制这些信息，请从收集器 VM 中打开 Azure 门户。 在项目“概述”页中，选择“发现计算机”，然后复制结果。  
7. 在“查看收集进度”中，监视发现过程，并检查从 VM 中收集的元数据是否在范围内。 收集器提供一个近似的发现时间。


### <a name="verify-vms-in-the-portal"></a>在门户中验证 VM

发现所需的时间取决于所发现的 VM 数。 一般情况下，发现 100 台 VM 的进程会在收集器完成运行后约一小时内完成。 

1. 在 Migration Planner 项目中，选择“管理” > “计算机”。
2. 检查想要发现的 VM 是否出现在门户中。


## <a name="next-steps"></a>后续步骤

- 了解如何为评估[创建组](how-to-create-a-group.md)。
- [详细了解](concepts-assessment-calculation.md)如何计算评估。
