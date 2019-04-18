---
title: 发现和评估要使用 Azure Migrate 迁移到 Azure 的本地 VMware VM | Microsoft Docs
description: 介绍如何发现和评估要使用 Azure Migrate 迁移到 Azure 的本地 VMware VM。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9eab8a29db40118f2a15064c52419ecebcd4aecb
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490306"
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>发现和评估要迁移到 Azure 的本地 VMware VM

[Azure Migrate](migrate-overview.md) 服务会评估要迁移到 Azure 的本地工作负荷。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建一个可供 Azure Migrate 用来发现本地 VM 的帐户
> * 创建 Azure Migrate 项目。
> * 设置一个本地收集器虚拟机 (VM)，用于发现要评估的本地 VMware VM。
> * 将 VM 分组并创建评估。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>先决条件

- **VMware**：计划迁移的 VM 必须由运行版本 5.5、6.0、6.5 或 6.7 的 vCenter Server 托管。 此外，需要一个运行 5.5 或更高版本的 ESXi 主机来部署收集器 VM。
- **vCenter Server 帐户**：需要只读帐户来访问 vCenter Server。 Azure Migrate 使用此帐户发现本地 VM。
- **权限**：在 vCenter Server 上，需要有通过导入 .OVA 格式的文件来创建 VM 的权限。

## <a name="create-an-account-for-vm-discovery"></a>创建用于 VM 发现的帐户

Azure Migrate 需要访问 VMware 服务器才能自动发现用于评估的 VM。 创建具有以下属性的 VMware 帐户。 请在 Azure Migrate 安装过程中指定此帐户。

- 用户类型：至少一个只读用户
- 权限：数据中心对象 –> 传播到子对象、角色=只读
- 详细信息：在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。
- 若要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。


## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-project"></a>创建一个项目

1. 在 Azure 门户中，单击“创建资源”。
2. 搜索 Azure Migrate，然后在搜索结果中选择服务“Azure Migrate”。 然后单击“创建”。
3. 指定项目名称和项目的 Azure 订阅。
4. 创建新的资源组。
5. 指定需要在其中创建项目的地理位置，然后单击“创建”。 只能在以下地域创建 Azure Migrate 项目。 但是，仍可以计划任意目标 Azure 位置的迁移。 为项目指定的地理位置仅用于存储从本地 VM 中收集的元数据。

**地理位置** | **存储位置**
--- | ---
Azure Government  | 美国政府弗吉尼亚州
亚洲 | 东南亚
欧洲 | 欧洲北部或欧洲西部
美国 | 美国东部或美国中西部

![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)


## <a name="download-the-collector-appliance"></a>下载收集器设备

Azure Migrate 会创建一个称作收集器设备的本地 VM。 此 VM 可发现本地 VMware VM，并将其相关元数据发送到 Azure Migrate 服务。 若要设置收集器设备，请下载一个 .OVA 文件，并将其导入到本地 vCenter 服务器以创建 VM。

1. 在 Azure Migrate 项目中，单击“开始” > “发现和评估” > “发现计算机”。
2. 在“发现计算机”中，单击“下载”以下载设备。

    Azure Migrate 设备与 vCenter Server 进行通信，持续分析本地环境，以收集每个 VM 的实时利用率数据。 它会针对每个指标（CPU 利用率、内存利用率等）收集峰值计数器。 进行性能数据收集时，此模型不依赖于 vCenter Server 的统计信息设置。 可以随时停止设备提供的持续分析。

    > [!NOTE]
    > 一次性发现设备现在已弃用，因为此方法依赖于 vCenter Server 针对性能数据点可用性的统计信息设置并且收集平均性能计数器，这导致用于迁移到 Azure 的 VM 大小不足。

    **快速评估：** 使用持续发现设备，发现完成后（需花费几个小时，具体取决于 VM 数量），可立即创建评估。 由于性能数据收集在你启动发现时开始，如果你希望进行快速评估，则应当将评估中的大小调整条件选择为“按本地”。 对于基于性能的评估，建议在启动发现后等待至少一天，以便获得可靠的大小建议。

    该设备仅连续收集性能数据，它不会检测本地环境中的任何配置更改（即 VM 添加、删除、磁盘添加等）。 如果本地环境中存在配置更改，可以执行以下操作以在门户中反映更改：

    - 添加项（VM、磁盘、核心等）：若要在 Azure 门户中反映这些更改，可以从设备停止发现，然后重启发现。 这可确保在 Azure Migrate 项目中更新更改。

    - 删除 VM：由于设备的设计方式，即使停止并启动发现，也不会反映出 VM 已删除这一更改。 这是因为后续发现的数据会追加到较旧的发现后，而不是进行覆盖。 在这种情况下，可以通过从组中删除 VM 并重新计算评估来直接忽略门户中的 VM。


3. 在“复制项目凭据”中，复制项目 ID 和密钥。 在配置收集器时要使用这些信息。

    ![下载 .ova 文件](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>验证收集器设备

在部署 .OVA 文件之前请检查其安全性。

1. 在下载文件的计算机上，打开管理员命令窗口。
2. 运行以下命令以生成 OVA 的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例： ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 生成的哈希应匹配这些设置。

#### <a name="continuous-discovery"></a>持续发现

  对于 OVA 版本 1.0.10.11

  **算法** | **哈希值**
    --- | ---
    MD5 | 5f6b199d8272428ccfa23543b0b5f600
    SHA1 | daa530de6e8674a66a728885a7feb3b0a2e8ccb0
    SHA256 | 85da50a21a7a6ca684418a87ccc1dd4f8aab30152c438a17b216ec401ebb3a21

  对于 OVA 版本 1.0.10.9

  **算法** | **哈希值**
  --- | ---
  MD5 | 169f6449cc1955f1514059a4c30d138b
  SHA1 | f8d0a1d40c46bbbf78cd0caa594d979f1b587c8f
  SHA256 | d68fe7d94be3127eb35dd80fc5ebc60434c8571dcd0e114b87587f24d6b4ee4d

  适用于 OVA 版本 1.0.10.4

  **算法** | **哈希值**
  --- | ---
  MD5 | 2ca5b1b93ee0675ca794dd3fd216e13d
  SHA1 | 8c46a52b18d36e91daeae62f412f5cb2a8198ee5
  SHA256 | 3b3dec0f995b3dd3c6ba218d436be003a687710abab9fcd17d4bdc90a11276be


#### <a name="one-time-discovery-deprecated-now"></a>一次性发现（现已弃用）

此模型现在已弃用，将提供对现有设备的支持。

  适用于 OVA 版本 1.0.9.15

  **算法** | **哈希值**
  --- | ---
  MD5 | e9ef16b0c837638c506b5fc0ef75ebfa
  SHA1 | 37b4b1e92b3c6ac2782ff5258450df6686c89864
  SHA256 | 8a86fc17f69b69968eb20a5c4c288c194cdcffb4ee6568d85ae5ba96835559ba

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

## <a name="create-the-collector-vm"></a>创建收集器 VM

将下载的文件导入 vCenter Server。

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
4. 在收集器 UI 的顶部栏中单击“检查更新”，并确认收集器正在最新版本上运行。 如果不是，可以选择从链接下载最新的升级包并更新收集器。
5. 在 Azure Migrate 收集器中，打开“设置必备组件”。
   - 选择打算迁移到的 Azure 云（Azure 全局或 Azure 政府）。
   - 接受许可条款，并阅读第三方信息。
   - 收集器将会检查 VM 是否可访问 Internet。
   - 如果 VM 通过代理访问 Internet，请单击“代理设置”，并指定代理地址和侦听端口。 如果代理需要身份验证，请指定凭据。 [详细了解](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-prerequisites) Internet 连接要求和收集器访问的 [URL 列表](https://docs.microsoft.com/azure/migrate/concepts-collector)。

     > [!NOTE]
     > 需以 http:\//ProxyIPAddress 或 http:\//ProxyFQDN 的形式输入代理地址。 仅支持 HTTP 代理。 如果有拦截代理，则如果没有导入代理证书，Internet 连接一开始可能会失败；[详细了解](https://docs.microsoft.com/azure/migrate/concepts-collector)如何在收集器 VM 上将代理证书作为受信任的证书导入，以便修复此问题。

   - 收集器将检查 collectorservice 是否正在运行。 该服务默认安装在收集器 VM 上。
   - 下载并安装 VMware PowerCLI。

6. 在“指定 vCenter Server 详细信息”中，执行以下操作：
    - 指定 vCenter 服务器的名称 (FQDN) 或 IP 地址。
    - 在“用户名称”和“密码”中，指定收集器用来发现 vCenter 服务器上的 VM 的只读帐户凭据。
    - 在“收集范围”中，选择 VM 发现的范围。 收集器只能发现指定范围内的 VM。 可将范围设置为特定文件夹、数据中心或群集。 它不应包含超过 1500 台 VM。 [详细了解](how-to-scale-assessment.md)如何发现更大的环境。

       > [!NOTE]
       > “集合范围”仅列出主机和群集的文件夹。 不能直接将 VM 的文件夹作为集合范围选择。 不过，可以使用能够访问单个 VM 的 vCenter 帐户来发现。 [详细了解](https://docs.microsoft.com/azure/migrate/how-to-scale-assessment#set-up-permissions)如何将范围局限于 VM 的文件夹。

7. 在“指定迁移项目”中，指定从门户复制的 Azure Migrate 项目 ID 和密钥。 如果未复制这些信息，请从收集器 VM 中打开 Azure 门户。 在项目“概述”页中，单击“发现计算机”，然后复制结果。  
8. 在“查看收集进度”中，监视发现状态。 [详细了解](https://docs.microsoft.com/azure/migrate/concepts-collector) Azure Migrate 收集器收集哪些数据。

> [!NOTE]
> 收集器仅支持使用“英语(美国)”作为操作系统语言和收集器界面语言。
> 如果在要评估的计算机上更改设置，则在运行评估之前，触发器会再次发现所做的更改。 在收集器中，使用“再次启动收集”选项来执行此操作。 收集完成以后，请在门户中选择用于评估的“重新计算”选项，以便获取更新的评估结果。


### <a name="verify-vms-in-the-portal"></a>在门户中验证 VM

收集器设备会持续分析本地环境，并且始终会每隔一小时发送一次性能数据。 启动发现操作一小时后，即可在门户中查看计算机的情况。

1. 在迁移项目中，单击“管理” > “计算机”。
2. 检查想要发现的 VM 是否出现在门户中。


## <a name="create-and-view-an-assessment"></a>创建和查看评估

在门户中发现 VM 后，可将其进行分组并创建评估。 在门户中发现 VM 后，可以立即创建本地评估。 在创建基于性能的评估之前，建议等待至少一天，以获得可靠的大小建议。

1. 在项目的“概述”页中，单击“+ 创建评估”。
2. 单击“全部查看”查看评估属性。
3. 创建组并指定组名称。
4. 选择要添加到该组的计算机。
5. 单击“创建评估”，以创建该组和评估。
6. 创建评估后，在“概述” > “仪表板”中查看该评估。
7. 单击“导出评估”，将评估下载为 Excel 文件。

> [!NOTE]
> 在启动发现之后，强烈建议等待至少一天，然后再创建评估。 若要使用最新的性能数据更新现有的评估，可以对评估使用“重新计算”命令来更新它。

### <a name="assessment-details"></a>评估详细信息

评估包括以下信息：本地 VM 是否兼容 Azure、适合在 Azure 中运行 VM 的 VM 大小，以及估算的每月 Azure 成本。

![评估报告](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Azure 迁移就绪性

评估中的 Azure 就绪性视图显示每个 VM 的就绪状态。 可以根据 VM 的属性将每个 VM 标记为：
- 已做好 Azure 迁移准备
- 已做好特定条件下的 Azure 迁移准备
- 尚未做好 Azure 迁移准备
- 就绪性未知

对于准备就绪的 VM，Azure Migrate 会推荐一个适合 Azure 的 VM 大小。 Azure Migrate 提供的大小建议取决于在评估属性中指定的大小调整条件。 如果大小调整条件是基于性能的大小调整，则在提供大小建议时，会考虑 VM（CPU 和内存）及磁盘（IOPS 和吞吐量）的性能历史记录。 如果大小调整条件是“按本地”，则 Azure Migrate 不会考虑 VM 和磁盘的性能数据。 Azure 中 VM 大小的建议通过查看本地 VM 的大小来完成，并且根据评估属性中指定的存储类型（默认为高级磁盘）来完成磁盘大小调整。 [详细了解](concepts-assessment-calculation.md)如何在 Azure Migrate 中进行大小调整。

如果 VM 尚未做好 Azure 迁移或特定条件下的 Azure 迁移的准备，Azure Migrate 会对就绪性问题进行说明，并提供修正步骤。

对于 Azure Migrate 因数据不可用而无法确定其 Azure 就绪性的 VM，系统会将其标记为“就绪性未知”。

除了 Azure 就绪性和大小调整，Azure Migrate 还会建议适用于 VM 迁移的工具。 这要求在本地环境进行更深入的发现。 [详细了解](how-to-get-migration-tool.md)如何在本地计算机上安装代理，以便进行更深入的发现。 如果未在本地计算机上安装代理，建议使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 进行直接迁移。 如果在本地计算机上安装了代理，Azure Migrate 会查看在计算机中运行的进程，确定该计算机是否为数据库计算机。 如果计算机是数据库计算机，则建议使用 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)作为迁移工具，否则使用 Azure Site Recovery。

  ![评估就绪](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>每月估算费用

此视图显示在 Azure 中运行 VM 的总计算和存储成本以及每台计算机的详细信息。 估算成本时，会考虑 Azure Migrate 针对计算机及其磁盘和评估属性提供的大小建议。

> [!NOTE]
> Azure Migrate 提供的成本估算适用于将本地 VM 作为 Azure 基础结构即服务 (IaaS) VM 运行。 Azure Migrate 不考虑任何平台即服务 (PaaS) 或服务型软件 (SaaS) 成本。

将会对于组中的所有 VM 合计计算和存储的每月估算费用。

![评估 VM 费用](./media/tutorial-assessment-vmware/assessment-vm-cost.png)

#### <a name="confidence-rating"></a>置信度分级

在 Azure Migrate 中进行的每个基于性能的评估都会与置信度分级相关联。置信度分级分为 1 星到 5 星（1 星表示置信度最低，5 星表示置信度最高）。 为评估分配置信度时，会考虑到进行评估计算时所需数据点的可用性。 对评估的置信度分级可以用来评估 Azure Migrate 提供的大小建议的可靠性。 置信度分级不适用于“按原样”本地评估。

对于基于性能的大小调整，Azure Migrate 需要 VM 的 CPU 和内存的利用率数据。 此外，对于每个附加到 VM 的磁盘，它需要磁盘 IOPS 和吞吐量数据。 同样，对于每个附加到 VM 的网络适配器，Azure Migrate 需要网络流入/流出量才能执行基于性能的大小调整。 如果上述利用率数据在 vCenter Server 中均不可用，则 Azure Migrate 提供的大小建议可能不可靠。 根据可用数据点的百分比，提供评估的置信度分级，如下所示：

   **数据点的可用性** | **置信度分级**
   --- | ---
   0%-20% | 1 星
   21%-40% | 2 星
   41%-60% | 3 星
   61%-80% | 4 星
   81%-100% | 5 星

由于以下某个原因，评估时并非所有数据点都可用：

- 在创建评估的过程中，你没有对环境进行分析。 例如，如果创建性能持续时间设置为 1 天的评估，则在对所有数据点启用发现之后，需要等待至少一天才能收集。

- 一些 VM 在进行评估计算期间关闭。 如果某些 VM 停机了一段时间，则无法收集该时段的性能数据。

- 在进行评估计算期间创建了一些 VM。 例如，如果要针对最后一个月的性能历史记录创建评估，但仅仅在一周前，在环境中创建了一些 VM， 则在这种情况下，新建 VM 的性能历史记录并非在整个期间都有。

> [!NOTE]
> 如果任何评估的置信度评级低于五星，请等待至少一天以便设备对环境进行分析，然后重新计算评估。 如果前述操作无法完成，则基于性能的大小调整可能不可靠，建议通过更改评估属性切换到“按本地大小调整”。

## <a name="next-steps"></a>后续步骤

- [了解](how-to-modify-assessment.md)如何根据要求自定义评估。
- 了解如何使用[计算机依赖关系映射](how-to-create-group-machine-dependencies.md)创建高可信度的评估组
- [详细了解](concepts-assessment-calculation.md)如何计算评估。
- [了解](how-to-scale-assessment.md)如何发现和评估大型 VMware 环境。
- [详细了解](resources-faq.md)有关 Azure Migrate 的常见问题解答
