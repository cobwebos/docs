---
title: 使用 Azure Migrate 执行批量发现和评估 | Microsoft Docs
description: 介绍如何使用 Azure Migrate 服务访问大量本地计算机。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: raynew
ms.openlocfilehash: 5f02393e6c8d5e094443e418b3fe7439d73ff837
ms.sourcegitcommit: 465ae78cc22eeafb5dfafe4da4b8b2138daf5082
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2018
ms.locfileid: "44325016"
---
# <a name="discover-and-assess-a-large-vmware-environment"></a>发现和评估大型 VMware 环境

Azure Migrate 具有每个项目 1500 台计算机的限制，本文介绍了如何使用 [Azure Migrate](migrate-overview.md) 访问大量本地虚拟机 (VM)。   

## <a name="prerequisites"></a>先决条件

- **VMware**：计划迁移的 VM 必须由版本 5.5、6.0 或 6.5 的 vCenter Server 托管。 此外，需要一个运行 5.0 或更高版本的 ESXi 主机来部署收集器 VM。
- **vCenter 帐户**：需要只读帐户来访问 vCenter Server。 Azure Migrate 使用此帐户发现本地 VM。
- **权限**：在 vCenter Server 中，需要有通过导入 .OVA 格式的文件来创建 VM 的权限。
- **统计信息设置**：此要求仅适用于[一次性发现模型](https://docs.microsoft.com/azure/migrate/concepts-collector#discovery-methods)。 对于一次性发现模型，应在开始部署之前，将 vCenter Server 的统计信息设置指定为级别 3。 对于每天、每周和每月的收集间隔，统计级别将设置为 3。 如果三个收集间隔中的任何一个的级别低于 3，则可以正常评估，但不会收集存储和网络的性能数据。 然后，大小建议将基于 CPU 和内存的性能数据以及磁盘和网络适配器的配置数据。

### <a name="set-up-permissions"></a>设置权限

Azure Migrate 需要访问 VMware 服务器才能自动发现用于评估的 VM。 VMware 帐户需要以下权限：

- 用户类型：至少为只读用户
- 权限：数据中心对象 –> 传播到子对象，角色=只读
- 详细信息：用户在数据中心级别分配，可以访问数据中心的所有对象。
- 若要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。

如果要在租户环境中进行部署，以下是对此进行设置的一种方法：

1.  每个租户创建一个用户并使用 [RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)，分配对属于特定租户的所有 VM 的只读权限。 然后，使用这些凭据进行发现。 RBAC 确保相应的 vCenter 用户将仅具有特定于租户的 VM 的访问权限。
2. 将为不同的租户用户设置 RBAC，如以下示例中所述的用户 #1 和用户 #2：

    - 在“用户名称”和“密码”中，指定收集器用来在其中发现 VM 的只读帐户凭据
    - Datacenter1 - 向用户 #1 和用户 #2 授予只读权限。 不要将这些权限传播到所有子对象，因为你将对单个 VM 的子对象设置权限。

      - VM1（租户 #1）（授予用户 #1 只读权限）
      - VM2（租户 #1）（授予用户 #1 只读权限）
      - VM3（租户 #2）（授予用户 #2 只读权限）
      - VM4（租户 #2）（授予用户 #2 只读权限）

   - 如果使用用户 #1 凭据执行发现，则仅能发现 VM1 和 VM2。

## <a name="plan-your-migration-projects-and-discoveries"></a>规划迁移项目和发现

单个 Azure Migrate 收集器支持从多台 vCenter Server（逐台）执行发现，并且还支持将发现置于多个迁移项目中（逐个）。

（执行一次性发现时）收集器在“发后不理”模型下工作，完成发现后，可以使用同一收集器从不同的 vCenter Server 收集数据，或者将其发送到不同的迁移项目。 如果执行持续发现，则一个设备只能连接到一个项目，因此无法使用同一个收集器来触发第二个发现。

基于以下限制计划发现和评估：

| **实体** | **计算机限制** |
| ---------- | ----------------- |
| Project    | 1,500             |
| 发现  | 1,500             |
| 评估 | 1,500             |

请记住以下计划注意事项：

- 使用 Azure Migrate 收集器进行发现时，可将发现范围设置为 vCenter Server 文件夹、数据中心、群集或主机。
- 若要执行多个发现操作，请在 vCenter Server 中验证要发现的 VM 是否位于支持 1,500 台计算机限制的文件夹、数据中心、群集或主机中。
- 出于评估需要，建议让计算机在相同的项目和评估中保持相互依赖关系。 在 vCenter Server 中，出于评估需要，请确保依赖计算机位于同一文件夹、数据中心或群集中。

根据你的方案，你可以如下所述对发现进行拆分：

### <a name="multiple-vcenter-servers-with-less-than-1500-vms"></a>VM 数少于 1500 的多台 vCenter Server
如果环境中有多个 vCenter Server，并且虚拟机总数少于 1500，则可以根据场景使用以下方法：

**一次性发现：** 可以使用一个收集器和一个迁移项目来发现所有 vCenter Server 上的所有虚拟机。 由于一次性发现收集器一次发现一台 vCenter Server，因此你可以对所有 vCenter Server 逐台运行同一收集器，并将收集器指向同一迁移项目。 完成所有发现后，可以为计算机创建评估。

**持续发现：** 执行持续发现时，一个设备只能连接到一个项目。 因此，需要为每个 vCenter Server 部署一个设备，然后为每个设备创建一个项目并相应地触发发现。

### <a name="multiple-vcenter-servers-with-more-than-1500-vms"></a>VM 数多于 1500 的多台 vCenter Server

如果你有多个 vCenter Server，每个 vCenter Server 中的虚拟机数少于 1500，但所有 vCenter Server 中的 VM 总数多于 1500，则需要创建多个迁移项目（一个迁移项目只能容纳 1500 个 VM）。 可以通过为每台 vCenter Server 创建迁移项目并对发现进行拆分来实现此目的。

**一次性发现：** 可以使用一个收集器来发现每个 vCenter Server（逐个发现）。 如果希望同时启动发现，则还可以部署多台设备并且并行运行发现。

**持续发现：** 需要创建多个收集器设备（为每个 vCenter Server 各创建一个），将每个设备连接到项目并相应地触发发现。

### <a name="more-than-1500-machines-in-a-single-vcenter-server"></a>单台 vCenter Server 中的虚拟机数超过 1500

如果单台 vCenter Server 中的虚拟机数超过 1500，则需要将发现拆分为多个迁移项目。 若要拆分发现，可以利用设备中的“范围”字段并指定要发现的主机、群集、文件夹或数据中心。 例如，如果 vCenter Server 中有两个文件夹，其中一个文件夹 (Folder1) 包含 1000 个 VM，另一个文件夹 (Folder2) 包含 800 个 VM，则你可以使用范围字段在这些文件夹之间拆分发现。

**一次性发现：** 可以使用同一个收集器来触发两个发现。 在第一个发现中，可以将 Folder1 指定为范围并将其指向第一个迁移项目，在第一个发现完成后，可以使用同一收集器，将其范围更改为 Folder2，将迁移项目详细信息更改为第二个迁移项目并执行第二个发现。

**持续发现：** 在这种情况下，需要创建两个收集器设备，对于第一个收集器，需将范围指定为 Folder1 并将其连接到第一个迁移项目。 可以使用第二个收集器设备并行启动 Folder2的发现，并将其连接到第二个迁移项目。

### <a name="multi-tenant-environment"></a>多租户环境

如果你有在租户之间共享的环境并且不希望在一个租户的订阅中发现另一个租户的 VM，则可以使用收集器设备中的“范围”字段来设置发现范围。 如果各个租户共享主机，请创建一个仅对属于特定租户的 VM 具有只读访问权限的凭据，然后在收集器设备中使用此凭据并将范围指定为主机来执行发现。

## <a name="discover-on-premises-environment"></a>在本地环境中发现

在准备好计划后，可以启动对本地虚拟机的发现：

### <a name="create-a-project"></a>创建一个项目

根据具体需求创建 Azure Migrate 项目：

1. 在 Azure 门户中，选择“创建资源”。
2. 搜索 Azure Migrate，然后在搜索结果中选择服务“Azure Migrate”。 然后选择“创建”。
3. 指定项目名称和项目的 Azure 订阅。
4. 创建新的资源组。
5. 指定要在其中创建项目的位置，然后选择“创建”。 请注意，仍可针对其他目标位置评估 VM。 将在为项目指定的位置存储从本地 VM 收集的元数据。

### <a name="set-up-the-collector-appliance"></a>设置收集器设备

Azure Migrate 会创建一个称作收集器设备的本地 VM。 此 VM 可发现本地 VMware VM，并将其相关元数据发送到 Azure Migrate 服务。 若要设置收集器设备，请下载 .OVA 文件，并将其导入到本地 vCenter Server 实例。

#### <a name="download-the-collector-appliance"></a>下载收集器设备

如果有多个项目，只需将收集器设备下载到 vCenter 服务器一次。 下载和设置设备后，为每个项目运行该设备，并指定唯一项目 ID 和密钥。

1. 在 Azure Migrate 项目中，单击“开始” > “发现和评估” > “发现计算机”。
2. 在“发现计算机”中，有两个适用于设备的选项。请单击“下载”，以便根据首选项下载适当的设备。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 **一次性发现：** 此模型的应用设备与 vCenter Server 通信，以便收集有关 VM 的元数据。 至于 VM 的性能数据收集，它依赖于存储在 vCenter Server 中的历史性能数据，会收集上一个月的性能历史记录。 在此模型中，Azure Migrate 针对每个指标收集平均计数器（不同于峰值计数器）[了解详情] (https://docs.microsoft.com/azure/migrate/concepts-collector#what-data-is-collected)。 由于它是一次性发现，因此在发现完成后，将不再反映本地环境中的更改。 如果希望反映所做的更改，则需对同一项目的同一环境执行重新发现操作。

    b. **持续发现：** 此模型的设备持续分析本地环境，以便收集每个 VM 的实时利用率数据。 在此模型中，将会针对每个指标（CPU 利用率、内存利用率等）收集峰值计数器。 进行性能数据收集时，此模型不依赖于 vCenter Server 的统计信息设置。 可以随时停止设备提供的持续分析。

    > [!NOTE]
    > 持续发现功能为预览版。

3. 在“复制项目凭据”中，请复制项目的 ID 和密钥。 在配置收集器时要使用这些信息。


#### <a name="verify-the-collector-appliance"></a>验证收集器设备

在部署 .OVA 文件之前请检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。

2. 运行以下命令以生成 OVA 的哈希：

   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

   用法示例：```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. 确保生成的哈希与以下设置匹配。

#### <a name="one-time-discovery"></a>一次性发现

适用于 OVA 版本 1.0.9.14

**算法** | **哈希值**
--- | ---
MD5 | 6d8446c0eeba3de3ecc9bc3713f9c8bd
SHA1 | e9f5bdfdd1a746c11910ed917511b5d91b9f939f
SHA256 | 7f7636d0959379502dfbda19b8e3f47f3a4744ee9453fc9ce548e6682a66f13c

适用于 OVA 版本 1.0.9.12

**算法** | **哈希值**
--- | ---
MD5 | d0363e5d1b377a8eb08843cf034ac28a
SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

适用于 OVA 版本 1.0.9.8

**算法** | **哈希值**
--- | ---
MD5 | b5d9f0caf15ca357ac0563468c2e6251
SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

适用于 OVA 版本 1.0.9.7

**算法** | **哈希值**
--- | ---
MD5 | d5b6a03701203ff556fa78694d6d7c35
SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

#### <a name="continuous-discovery"></a>持续发现

适用于 OVA 版本 1.0.10.4

**算法** | **哈希值**
--- | ---
MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be

### <a name="create-the-collector-vm"></a>创建收集器 VM

将下载的文件导入 vCenter Server：

1. 在 vSphere 客户端控制台中，选择“文件” > “部署 OVF 模板”。

    ![部署 OVF](./media/how-to-scale-assessment/vcenter-wizard.png)

2. 在“部署 OVF 模板向导”>“源”中，指定 OVA 文件的位置。
3. 在“名称”和“位置”中，为收集器 VM 指定一个友好名称，以及要托管 VM 的库存对象。
4. 在“主机/群集”中，指定要在其上运行收集器 VM 的主机或群集。
5. 在存储中，指定收集器 VM 的存储目标。
6. 在“磁盘格式”中，指定磁盘类型和大小。
7. 在“网络映射”中，指定收集器 VM 要连接到的网络。 网络需要与 Internet 建立连接才能向 Azure 发送元数据。
8. 检查并确认设置，然后选择“完成”。

### <a name="identify-the-id-and-key-for-each-project"></a>标识每个项目的 ID 和密钥

如果拥有多个项目，请务必标识每个项目的 ID 和密钥。 运行收集器来发现 VM 时，需要使用密钥。

1. 在项目中，选择“入门” > “发现和评估” > “发现计算机”。
2. 在“复制项目凭据”中，请复制项目的 ID 和密钥。
    ![复制项目凭据](./media/how-to-scale-assessment/copy-project-credentials.png)

### <a name="set-the-vcenter-statistics-level"></a>设置 vCenter 统计信息级别

收集器设备发现与所选虚拟机相关的以下静态元数据。

1. VM 显示名称（在 vCenter 上）
2. VM 的清单路径（vCenter 中的主机/文件夹）
3. IP 地址
4. MAC 地址
5. 操作系统
5. 核心数、磁盘数、NIC 数
6. 内存大小、磁盘大小
7. VM、磁盘和网络的性能计数器，如下表中所列出的那样。

对于一次性发现，下表列出了确切收集的性能计数器，并列出了在未收集特定计数器的情况下受影响的评估结果。

对于持续发现，将实时（20 秒间隔）收集相同的计数器，因此，对 vCenter 统计信息级别没有依赖。 然后，设备将从 20 秒样本中选择峰值并将其发送到 Azure，以便汇总 20 秒样本，每隔 15 分钟创建单个数据点。

|计数器                                  |级别    |设备级别  |评估影响                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |NA                |建议的 VM 大小和成本                    |
|mem.usage.average                        | 1       |NA                |建议的 VM 大小和成本                    |
|virtualDisk.read.average                 | 2       |2                 |磁盘大小、存储成本和 VM 大小         |
|virtualDisk.write.average                | 2       |2                 |磁盘大小、存储成本和 VM 大小         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |磁盘大小、存储成本和 VM 大小         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |磁盘大小、存储成本和 VM 大小         |
|net.received.average                     | 2       |3                 |VM 大小和网络成本                        |
|net.transmitted.average                  | 2       |3                 |VM 大小和网络成本                        |

> [!WARNING]
> 对于一次性发现，如果刚设置了更高的统计信息级别，将需要最多一天的时间来生成性能计数器。 因此，建议在一天后运行发现。 对于持续发现模型，开始发现后需要至少等待一天让设备分析环境，然后创建评估。

### <a name="run-the-collector-to-discover-vms"></a>运行收集器以发现 VM。

对于需要执行的每次发现，请运行收集器以发现所需范围内的 VM。 请依次运行发现。 不支持并发执行发现，每个发现必须具有不同的范围。

1.  在 vSphere 客户端控制台中，右键单击“VM”>“打开控制台”。
2.  提供设备的语言、时区和密码首选项。
3.  在桌面上，选择“运行收集器”快捷方式。
4.  在 Azure Migrate 收集器中，打开“设置先决条件”然后：

    a. 接受许可条款，并阅读第三方信息。

    收集器将会检查 VM 是否可访问 Internet。

    b. 如果 VM 通过代理访问 Internet，请选择“代理设置”，并指定代理地址和侦听端口。 如果代理需要身份验证，请指定凭据。

    收集器将检查 collectorservice 是否正在运行。 该服务默认安装在收集器 VM 上。

    c. 下载并安装 VMware PowerCLI。

5.  在“指定 vCenter Server 详细信息”中，执行以下操作：
    - 指定 vCenter Server 的名称 (FQDN) 或 IP 地址。
    - 在“用户名称”和“密码”中，指定收集器用来发现 vCenter Server 中 VM 的只读帐户凭据。
    - 在“选择范围”中，选择 VM 发现的范围。 收集器只能发现指定范围内的 VM。 可将范围设置为特定文件夹、数据中心或群集。 它不应包含超过 1000 台 VM。

6.  在“指定迁移项目”中，指定项目的 ID 和密钥。 如果未复制这些信息，请从收集器 VM 中打开 Azure 门户。 在项目“概述”页中，选择“发现计算机”，然后复制结果。  
7.  在“查看收集进度”中，监视发现过程，并检查从 VM 中收集的元数据是否在范围内。 收集器提供一个近似的发现时间。


#### <a name="verify-vms-in-the-portal"></a>在门户中验证 VM

进行一次性发现时，发现所需的时间取决于所发现的 VM 数。 通常情况下，对于 100 个 VM，在收集器完成运行后，需要大约一小时才能收集完配置和性能数据。 可以在发现完成后立即创建评估（基于性能的评估和本地评估）。

对于持续发现（预览版），收集器会持续分析本地环境，并且始终会每隔一小时发送一次性能数据。 启动发现操作一小时后，即可在门户中查看计算机的情况。 强烈建议在为 VM 创建任何基于性能的评估之前，先等待至少一天。

1. 在迁移项目中，单击“管理” > “计算机”。
2. 检查想要发现的 VM 是否出现在门户中。


## <a name="next-steps"></a>后续步骤

- 了解如何为评估[创建组](how-to-create-a-group.md)。
- [详细了解](concepts-assessment-calculation.md)如何计算评估。
