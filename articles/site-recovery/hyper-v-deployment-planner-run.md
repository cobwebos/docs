---
title: "适用于 Hyper-V 到 Azure 部署的 Azure Site Recovery 部署规划器 | Microsoft Docs"
description: "本文介绍在从 Hyper-V 移到 Azure 时运行 Azure Site Recovery 部署规划器的模式。"
services: site-recovery
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: nisoneji
ms.openlocfilehash: ae539f136578c6461ef7f680d553fbd76b10ae98
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="run-azure-site-recovery-deployment-planner-for-hyper-v-to-azure"></a>运行适用于 Hyper-V 到 Azure 部署的 Azure Site Recovery 部署规划器

## <a name="modes-of-running-the-deployment-planner"></a>运行部署规划器的模式
可使用以下四种模式之一运行该命令行工具 (ASRDeploymentPlanner.exe)： 
-   [获取虚拟机 (VM) 列表](#get-vm-list-for-profiling-hyper-v-vms)
-   [配置文件](#profile-hyper-v-vms)
-   [生成报表](#generate-report)
-   [获取吞吐量](#get-throughput)

首先，运行该工具，从单个或多个 Hyper-V 主机中获取 VM 的列表。 然后，以分析模式运行该工具，收集 VM 数据变动量和 IOPS。 接下来，运行该工具生成报告，确定网络带宽和存储要求。

## <a name="get-the-vm-list-for-profiling-hyper-v-vms"></a>获取 VM 列表，以便对 Hyper-V VM 进行分析
首先，需要一个要分析的 VM 的列表。 使用部署规划器工具的 GetVMList 模式，通过单个命令生成多个 Hyper-V 主机上存在的 VM 的列表。 生成完整列表以后，即可从输出文件中删除不希望分析的 VM。 然后使用输出文件进行所有其他的操作：分析、生成报表、获取吞吐量。

可以生成 VM 列表，方法是将工具指向 Hyper-V 群集和/或单独的 Hyper-V 主机。

### <a name="command-line-parameters"></a>命令行参数
下表包含一系列必需的和可选的工具参数，适用于在 GetVMList 模式下运行。 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```
| 参数名称 | 说明 |
|---|---|
| -Operation | GetVMList |
| -User | 连接到 Hyper-V 主机或 Hyper-V 群集所需的用户名。 用户需要有管理访问权限。|
|-ServerListFile | 包含服务器列表的文件，其中有要分析的 VM。 文件路径可以是绝对或相对路径。 此文件的每一行中应包含以下内容之一：<ul><li>Hyper-V 主机名或 IP 地址</li><li>Hyper-V 群集名称或 IP 地址</li></ul><br>**示例：**ServerList.txt 包含以下服务器：<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|（可选）通用命名约定 (UNC) 或本地目录路径，用于存储在此操作期间生成的数据。 如果未指定名称，则会使用当前路径下名为 ProfiledData 的目录作为默认目录。|
|-OutputFile| （可选）一个文件，其中保存了从 Hyper-V 服务器中提取的 VM 的列表。 如果未提到名称，则详细信息会存储在 VMList.txt 中。  在删除不需分析的 VM 后，即可使用此文件开始分析。|
|-Password|（可选）连接到 Hyper-V 主机所需的密码。 如果未将密码指定为参数，则在运行命令时，系统会提示你输入它。|

### <a name="getvmlist-discovery"></a>GetVMList 发现
**Hyper-V 群集**：如果在服务器列表文件中给出了 Hyper-V 群集名称，此工具会找出该群集的所有 Hyper-V 节点，并获取存在于每个 Hyper-V 主机上的 VM。

**Hyper-V 主机**：如果给出了 Hyper-V 主机名，此工具会首先检查该名称是否属于群集。 如果是，此工具会提取属于群集的节点。 然后从每个 Hyper-V 主机获取 VM。 

也可选择在文件中列出要手动分析的 VM 的友好名称或 IP 地址。

在记事本中打开输出文件，并将要分析的所有 VM 的名称复制到另一文件（例如 ProfileVMList.txt）中。 每行使用一个 VM 名称。 在执行所有其他操作（分析、生成报表和获取吞吐量）时，此文件用作工具的 -VMListFile 参数的输入。

### <a name="examples"></a>示例

#### <a name="store-the-list-of-vms-in-a-file"></a>在文件中存储 VM 列表
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile “E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

#### <a name="store-the-list-of-vms-at-the-default-location--directory-path"></a>在默认位置（-Directory 路径）存储 VM 列表
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>分析 Hyper-V VM
在分析模式下，部署规划器工具将连接到每个 Hyper-V 主机，以便收集有关 VM 的性能数据。 

分析不会影响生产 VM 的性能，因为不会与其进行直接连接。 从 Hyper-V 主机收集所有性能数据。

此工具每 15 秒查询 Hyper-V 主机一次，以确保分析准确性。 它存储每分钟的性能计数器数据的平均值。

此工具无缝处理从群集中的一个节点到另一个节点的 VM 迁移，以及主机中的存储迁移。

### <a name="getting-the-vm-list-to-profile"></a>获取要分析的 VM 列表
若要创建要分析的 VM 的列表，请参阅 [GetVMList](#get-vm-list-for-profiling-hyper-v-vms) 操作。

创建要分析的 VM 的列表后，可在分析模式下运行该工具。 

### <a name="command-line-parameters"></a>命令行参数
下表列出了必需的和可选的工具参数，适用于在分析模式下运行。 此工具常用于从 VMware 移至 Azure 以及从 Hyper-V 移至 Azure 这两种方案。 以下参数适用于 Hyper-V。 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```
| 参数名称 | 说明 |
|---|---|
| -Operation | StartProfiling |
| -User | 连接到 Hyper-V 主机或 Hyper-V 群集所需的用户名。 用户需要有管理访问权限。|
| -VMListFile | 包含要分析的 VM 列表的文件。 文件路径可以是绝对或相对路径。 对于 Hyper-V，此文件是 GetVMList 操作的输出文件。 如果手动进行准备，此文件应包含一个服务器名称或 IP 地址，后跟 VM 名称（每一行都由 \ 分隔）。 该文件中指定的 VM 名称应与 Hyper-V 主机上的 VM 名称相同。<br><br>**示例：**VMList.txt 包含以下 VM：<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|运行分析的分钟数。 最小值为 30 分钟。|
|-NoOfHoursToProfile|运行分析的小时数。|
|-NoOfDaysToProfile |运行分析的天数。 建议在运行分析时，运行 7 天以上。 该持续时间可确保观察环境中指定时段内的工作负荷模式，并使用这些结果来提供准确的建议。|
|-Virtualization|虚拟化类型（VMware 或 Hyper-V）。|
|-Directory|（可选）用于存储在分析期间生成的分析数据的 UNC 或本地目录路径。 如果未指定名称，则会使用当前路径下名为 ProfiledData 的目录作为默认目录。|
|-Password|（可选）连接到 Hyper-V 主机所需的密码。 如果未将密码指定为参数，则在运行命令时，系统会提示你输入它。|
|-StorageAccountName|（可选）存储帐户名称，用于确定在将数据从本地复制到 Azure 时可实现的吞吐量。 该工具会将测试数据上传到此存储帐户来计算吞吐量。 存储帐户必须是常规用途 v1 或常规用途 v2。|
|-StorageAccountKey|（可选）用于访问存储帐户的密钥。 转到 Azure 门户 >“存储帐户” > *存储帐户名称* > “设置” > “访问密钥” > **Key1**（或经典存储帐户的主访问密钥）。|
|-Environment|（可选）Azure 存储帐户的目标环境。 它可能采用下述三个值之一：AzureCloud、AzureUSGovernment、AzureChinaCloud。 默认值为 AzureCloud。 当目标区域为 Azure 美国政府或 Azure 中国时，请使用此参数。|

建议在分析 VM 时，分析 7 天以上。 如果变动量模式在某个月发生变化，建议在看到最大变动量的一周内进行分析。 最好的方式是分析 31 天，以便获取更好的建议。 

在分析过程中，ASRDeploymentPlanner.exe 将保持运行。 该工具将取以天为单位的分析时间输入。 若要快速测试此工具或获取概念证明，可以分析数小时或数分钟。 允许的最短分析时间为 30 分钟。 

在分析期间，可以选择性地传递存储帐户名称和密钥，确定在从 Hyper-V 服务器复制到 Azure 时，Azure Site Recovery 可实现的吞吐量。 如果在分析期间不传递存储帐户名称和密钥，该工具不会计算可实现的吞吐量。

可以针对各个 VM 集运行该工具的多个实例。 确保不要在任何分析集中重复使用 VM 名称。 例如，假设已分析了 10 个 VM（VM1 到 VM10）。 几天后，需要再分析 5 个 VM（VM11 到 VM15）。 可以通过另一命令行控制台运行该工具，以便完成第二组 VM（VM11 到 VM15）的分析。 

请确保第二组 VM 没有第一个分析实例中的任何 VM 名称，或确保为第二次运行使用不同的输出目录。 如果使用该工具的两个实例分析相同的 VM 并使用相同的输出目录，生成的报告将不准确。 

默认情况下，此工具配置为在分析后为最多 1,000 个 VM 生成报表。 若要更改限制，可以更改 ASRDeploymentPlanner.exe.config 文件中的 MaxVMsSupported 项值。
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```
使用默认设置时，若要分析 1500 个 VM（此处为举例），请创建两个 VMList.txt 文件。 一个有 1,000 个 VM，另一个有 500 个 VM。 运行 Azure Site Recovery 部署规划器的这两个实例：一个使用 VMList1.txt，另一个使用 VMList2.txt。 可以使用同一目录路径来存储两个 VMList VM 的已分析数据。 

根据硬件配置（尤其是在其中运行报表生成工具的服务器的 RAM 大小），在内存不足的情况下，操作可能会失败。 如果硬件良好，可将 MaxVMsSupported 更改为更高的值。  

VM 配置会在分析操作开始时捕获一次，存储在名为 VMDetailList.xml 的文件中。 生成报告时使用此信息。 从分析开始到结束都不捕获 VM 配置中发生的任何更改（例如，核心、磁盘或 NIC 数增加）。 如果分析的 VM 配置在分析过程中发生了更改，则可通过下述解决方法在生成报表时获取最新的 VM 详细信息：

* 备份 VMdetailList.xml 文件，并将其从当前位置删除。
* 生成报告时传递 -User 和 -Password 参数。

该分析命令在分析目录中生成多个文件。 请勿删除任何文件，因为这样做会影响报告生成。

### <a name="examples"></a>示例

#### <a name="profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>分析 VM 30 天，确定从本地到 Azure 的吞吐量
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="profile-vms-for-15-days"></a>分析 VM 15 天
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>分析 VM 60 分钟以快速测试工具
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="profile-vms-for-2-hours-for-a-proof-of-concept"></a>分析 VM 2 小时以获取概念证明
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

### <a name="considerations-for-profiling"></a>有关分析的注意事项

如果运行该工具的服务器重启或崩溃，或者使用 Ctrl+C 关闭该工具，系统会保存分析数据。 但是，过去 15 分钟的分析数据可能会丢失。 在这样的情况下，请在服务器重启后以分析模式重新运行该工具。

如果传递了存储帐户名称和密钥，该工具会在执行最后一个分析步骤时测量吞吐量。 如果在分析完成之前关闭该工具，则不会计算吞吐量。 若要在生成报告之前确定吞吐量，可通过命令行控制台运行 GetThroughput 操作。 否则，生成的报告将不包含吞吐量信息。

Azure Site Recovery 不支持使用 iSCSI 和传递磁盘的 VM。 但是，此工具无法检测和分析附加到 VM 的 iSCSI 和传递磁盘。

## <a name="generate-a-report"></a>生成报告
该工具生成一个启用了宏的 Microsoft Excel 文件（XLSM 文件）作为报表输出， 并对所有部署建议进行了汇总。 该报表名为 DeploymentPlannerReport_*唯一数字标识符*.xlsm，置于指定目录中。

完成分析后，可在报告生成模式下运行该工具。 

### <a name="command-line-parameters"></a>命令行参数
下表包含一系列必需的和可选的工具参数，适用于在报告生成模式下 运行。 此工具常用于从 VMware 移至 Azure 以及从 Hyper-V 移至 Azure。 以下参数适用于 Hyper-V。
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```
| 参数名称 | 说明 |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | 一个文件，其中包含一系列需为其生成报表的已分析 VM。 文件路径可以是绝对或相对路径。 对于 Hyper-V，此文件是 GetVMList 操作的输出文件。 如果手动进行准备，此文件应包含一个服务器名称或 IP 地址，后跟 VM 名称（每一行都由 \ 分隔）。 该文件中指定的 VM 名称应与 Hyper-V 主机上的 VM 名称相同。<br><br>**示例：**VMList.txt 包含以下 VM：<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|虚拟化类型（VMware 或 Hyper-V）。|
|-Directory|（可选）UNC 或本地目录路径，其中存储了分析数据（在分析期间生成的文件）。 需要使用此数据来生成报告。 如果未指定名称，则会使用当前路径下名为 ProfiledData 的目录作为默认目录。|
| -User | （可选）连接到 Hyper-V 主机或 Hyper-V 群集所需的用户名。 用户需要有管理访问权限。 用户和密码用于获取要在报表中使用的最新 VM 配置信息（例如磁盘数、核心数、NIC 数）。 如果未提供此值，则使用分析过程中收集的配置信息。|
|-Password|（可选）连接到 Hyper-V 主机所需的密码。 如果未将密码指定为参数，则在运行命令时，系统会提示你输入它。|
| -DesiredRPO | （可选）以分钟为单位的所需恢复点目标 (RPO)。 默认值为 15 分钟。|
| -Bandwidth | （可选）带宽，以兆位/秒为单位。 使用此参数可以计算指定的带宽可实现的 RPO。 |
| -StartDate | （可选）采用 MM-DD-YYYY:HH:MM（24 小时）格式的开始日期和时间。 StartDate 必须与 EndDate 一起指定。 如果指定 StartDate，会根据从 StartDate 到 EndDate 收集的分析数据生成报告。 |
| -EndDate | （可选）采用 MM-DD-YYYY:HH:MM（24 小时）格式的结束日期和时间。 EndDate 必须与 StartDate 一起指定。 如果指定 EndDate，会根据从 StartDate 到 EndDate 收集的分析数据生成报告。 |
| -GrowthFactor | （可选）增长系数，以百分比表示。 默认值为 30%。 |
| -UseManagedDisks | （可选）UseManagedDisks：是/否。 默认值为“是”。 计算可放置到单个存储帐户中的虚拟机数量时，需考虑到对虚拟机进行的故障转移/测试性故障转移是否是在托管磁盘而不是非托管磁盘上完成的。 |
|-SubscriptionId |（可选）订阅 GUID。 可以根据订阅、与订阅相关联的产品/服务、目标 Azure 区域和指定的货币，按照最新的价格使用此参数生成成本估算报表。|
|-TargetRegion|（可选）充当复制目标的 Azure 区域。 由于 Azure 的成本因区域而异，因此可使用此参数来生成特定目标 Azure 区域的报表。 默认值为 WestUS2 或上次使用的目标区域。 请参阅[支持的目标区域](hyper-v-deployment-planner-cost-estimation.md#supported-target-regions)的列表。|
|-OfferId|（可选）与订阅关联的产品/服务。 默认值为 MS-AZR-0003P（即用即付）。|
|-Currency|（可选）在生成的报表中显示的成本所采用的货币。 默认为美元 ($) 或上次使用的货币。 请参阅[支持的货币](hyper-v-deployment-planner-cost-estimation.md#supported-currencies)的列表。|

默认情况下，此工具配置为在分析后为最多 1,000 个 VM 生成报表。 若要更改限制，可以更改 ASRDeploymentPlanner.exe.config 文件中的 MaxVMsSupported 项值。
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>示例
#### <a name="generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>当分析数据位于本地驱动器上时，使用默认值生成报表
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”
```

#### <a name="generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>当分析数据位于远程服务器上时生成报表
应该对远程目录拥有读/写访问权限。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “\\PS1-W2K12R2\Hyper-V_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="generate-a-report-with-a-specific-bandwidth-that-you-will-provision-for-the-replication"></a>使用将要为复制预配的特定带宽生成一个报表
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -Bandwidth 100
```

#### <a name="generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>使用 5% 的增长系数而不是默认值 30% 来生成报表 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="generate-a-report-with-a-subset-of-profiled-data"></a>使用分析数据的子集生成报表
例如，有 30 天的分析数据，但只想生成 20 天的报告。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="generate-a-report-for-a-5-minute-rpo"></a>生成 5 分钟 RPO 报表
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="generate-a-report-for-the-south-india-azure-region-with-indian-rupee-and-a-specific-offer-id"></a>使用印度卢比和特定的产品/服务 ID，生成一个针对“印度南部”Azure 区域的报表
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


### <a name="percentile-value-used-for-the-calculation"></a>用于计算的百分位值
此工具在生成报表时，会针对读/写 IOPS、写 IOPS 和数据变动量默认采用 95% 这个值。 这些值在分析所有 VM 期间收集。 此指标可确保系统不使用百分位峰值 100（在发生临时事件时，可能会出现在 VM 中）来确定目标存储帐户和源带宽需求。 例如，临时事件可能是一天运行一次的备份作业、定期发生的数据库索引编制或分析报表生成活动，或者另一短期时间点事件。

使用百分位值 95 可以反映真实的工作负荷特征，使这些工作负荷在 Azure 中运行时提供最佳性能。 我们预计不需要更改此数字。 如果更改此值（例如更改为百分位值 90），则可在默认文件夹中更新并保存配置文件 ASRDeploymentPlanner.exe.config，针对现有的分析数据生成新报表。
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

### <a name="considerations-for-growth-factor"></a>增长系数注意事项
假设使用量在一段时间内可能会增多，则考虑工作负荷特征的增长就至关重要。 在保护就位以后，如果工作负荷特征发生更改，则除非先禁用保护，再重新启用保护，否则在切换到其他存储帐户后将无法获得保护。

例如，假设你目前的 VM 适合标准存储复制帐户。 在随后的三个月中，可能会发生以下变化：

1. 在 VM 上运行的应用程序的用户数会增加。
2. 该 VM 上的变动量增加，因此需使用高级存储，使 Azure Site Recovery 复制能够跟上节奏。
3. 需要先禁用对高级存储帐户的保护，再重新启用该保护。

强烈建议在部署计划期间对增长进行计划。 虽然默认值为 30%，但你了解应用程序使用模式和增长预期， 因此可以在生成报表时可以相应地更改此数字。 另外，还可以对同一批已分析数据使用各种增长系数，从而生成多个报表， 然后即可确定最适合自己的目标存储和源带宽建议。 

生成的 Microsoft Excel 报告包含以下信息：

* [本地摘要](hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [建议](hyper-v-deployment-planner-analyze-report.md#recommendations)
* [VM-粗糙年位置](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
* [兼容的 VM](hyper-v-deployment-planner-analyze-report.md#compatible-vms)
* [不兼容的 VM](hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [本地存储要求](hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [IR 批处理](hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [成本估算](hyper-v-deployment-planner-cost-estimation.md)

![部署规划器报表](media/hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>获取吞吐量
若要估算从本地复制到 Azure 期间 Azure Site Recovery 可实现的吞吐量，请在 GetThroughput 模式下运行该工具。 该工具计算的吞吐量源自运行该工具的服务器。 理想情况下，此服务器是其 VM 需要受到保护的 Hyper-V 服务器。 

### <a name="command-line-parameters"></a>命令行参数 
打开命令行控制台，转到 Azure Site Recovery 部署规划工具的文件夹。 结合以下参数运行 ASRDeploymentPlanner.exe。
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```
 参数名称 | 说明 |
|---|---|
| -Operation | GetThroughput |
|-Virtualization|虚拟化类型（VMware 或 Hyper-V）。|
|-Directory|（可选）UNC 或本地目录路径，其中存储了分析数据（在分析期间生成的文件）。 需要使用此数据来生成报告。 如果未指定名称，则会使用当前路径下名为 ProfiledData 的目录作为默认目录。|
| -StorageAccountName | 存储帐户名称，用于确定在将数据从本地复制到 Azure 时消耗的带宽。 该工具会将测试数据上传到此存储帐户来确定消耗的带宽。 存储帐户必须是常规用途 v1 或常规用途 v2。|
| -StorageAccountKey | 用于访问存储帐户的存储帐户密钥。 转到 Azure 门户 >“存储帐户” > *存储帐户名称* > “设置” > “访问密钥” > **Key1**。|
| -VMListFile | 一个文件，其中包含一系列可以通过分析来计算所消耗带宽的 VM。 文件路径可以是绝对或相对路径。 对于 Hyper-V，此文件是 GetVMList 操作的输出文件。 如果手动进行准备，此文件应包含一个服务器名称或 IP 地址，后跟 VM 名称（每一行都由 \ 分隔）。 该文件中指定的 VM 名称应与 Hyper-V 主机上的 VM 名称相同。<br><br>**示例：**VMList.txt 包含以下 VM：<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Environment|（可选）Azure 存储帐户的目标环境。 它可能采用下述三个值之一：AzureCloud、AzureUSGovernment、AzureChinaCloud。 默认值为 AzureCloud。 当目标 Azure 区域为 Azure 美国政府或 Azure 中国时，请使用此参数。|

### <a name="example"></a>示例
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory E:\Hyp-erV_ProfiledData -VMListFile E:\Hyper-V_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

### <a name="throughput-considerations"></a>吞吐量注意事项

该工具将在指定的目录中创建多个 64 MB 的 asrvhdfile*number*.vhd 文件（其中 *number* 是文件编号）。 该工具会将这些文件上传到存储帐户来确定吞吐量。 测出吞吐量后，该工具会从存储帐户和本地服务器中删除所有这些文件。 如果该工具在计算吞吐量时因故被终止，它不会从存储帐户或本地服务器中删除这些文件。 需手动删除这些文件。

吞吐量在指定的时间点测量。 它是 Azure Site Recovery 在复制过程中能够实现的最大吞吐量，前提是所有其他系数保持不变。 例如，如果任何应用程序在相同的网络中开始消耗更多的带宽，则在复制期间实际吞吐量会有所变化。 如果是在受保护 VM 的数据变动量高时运行 GetThroughput 操作，则所测吞吐量的结果会有所不同。 

若要了解在不同时间能够达到的吞吐量水平，建议在分析期间的不同时间点运行该工具。 在报告中，该工具会显示最后一个测得的吞吐量。

> [!NOTE]
> 在与 Hyper-V 服务器具有相同存储和 CPU 特征的服务器上运行该工具。

对于复制，请设置建议的带宽以满足 100% 时间的 RPO。 在设置适当的带宽后，如果工具所报告的已实现吞吐量没有增长，请执行以下操作：

1. 通过检查来确定是否是网络服务质量 (QoS) 问题限制了 Azure Site Recovery 吞吐量。
2. 通过检查来确定 Azure Site Recovery 保管库是否位于从物理上来说最近的受支持 Microsoft Azure 区域，以尽量降低网络延迟。
3. 检查本地存储特征，确定能否改进硬件（例如，从 HDD 升级到 SSD）。

    
## <a name="next-steps"></a>后续步骤
* [分析生成的报表](hyper-v-deployment-planner-analyze-report.md)
