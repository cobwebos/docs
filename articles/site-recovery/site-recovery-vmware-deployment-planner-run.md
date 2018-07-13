---
title: 适用于 VMware 到 Azure 部署的 Azure Site Recovery Deployment Planner | Microsoft Docs
description: 本文介绍针对 VMware 到 Azure 方案运行 Azure Site Recovery 部署规划器的模式。
services: site-recovery
documentationcenter: ''
author: nsoneji
manager: garavd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/06/2018
ms.author: nisoneji
ms.openlocfilehash: 8298f4a31db974f75e80aef7589bfd31fd7c7491
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921046"
---
# <a name="run-azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>运行适用于 VMware 到 Azure 部署的 Azure Site Recovery 部署规划器
本文为适用于 VMware 到 Azure 生产部署的 Azure Site Recovery Deployment Planner 用户指南。


## <a name="modes-of-running-deployment-planner"></a>运行部署规划器的模式
可使用以下四种模式之一运行该命令行工具 (ASRDeploymentPlanner.exe)：

1.  [分析](#profile-vmware-vms)
2.  [报表生成](#generate-report)
3.  [获取吞吐量](#get-throughput)

首先，请以分析模式运行该工具，收集 VM 数据变动量和 IOPS。 接下来，运行该工具以生成报表，确定网络带宽、存储要求和 DR 成本。

## <a name="profile-vmware-vms"></a>分析 VMware VM
在分析模式下，Deployment Planner 工具将连接到 vCenter 服务器/vSphere ESXi 主机，以便收集有关 VM 的性能数据。

* 分析不会影响生产 VM 的性能，因为不会与其进行直接连接。 所有性能数据是从 vCenter 服务器/vSphere ESXi 主机收集的。
* 为了确保分析对服务器造成的影响可忽略不计，该工具每隔 15 分钟对 vCenter 服务器/vSphere ESXi 主机查询一次。 该查询时间间隔不会降低分析的准确性，因为该工具会存储每一分钟的性能计数器数据。

### <a name="create-a-list-of-vms-to-profile"></a>创建要分析的 VM 的列表
首先，需要一个要分析的 VM 的列表。 可以使用以下过程中的 VMware vSphere PowerCLI 命令获取 vCenter 服务器/vSphere ESXi 主机上的 VM 的所有名称。 也可以在文件中列出要手动分析的 VM 的友好名称或 IP 地址。

1. 登录到在其中安装了 VMware vSphere PowerCLI 的 VM。
2. 打开 VMware vSphere PowerCLI 控制台。
3. 确保启用脚本的执行策略。 如果已禁用，请以管理员模式启动 VMware vSphere PowerCLI 控制台，并运行以下命令将它启用：

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4. 如果系统不将 Connect-VIServer 视为 cmdlet 的名称，则可能需要运行以下命令。

            Add-PSSnapin VMware.VimAutomation.Core

5. 要获取在 vCenter 服务器/vSphere ESXi 主机上的 VM 的所有名称并将列表存储在 .txt 文件中，请运行下面列出的两个命令。
将 &lsaquo;server name&rsaquo;、&lsaquo;user name&rsaquo;、&lsaquo;password&rsaquo; 和 &lsaquo;outputfile.txt&rsaquo; 替换为输入。

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>

6. 在记事本中打开输出文件，并将要分析的所有 VM 的名称复制到另一文件（例如 ProfileVMList.txt）中，每行一个 VM 名称。 此文件将用作命令行工具的 *-VMListFile* 参数的输入。

    ![Deployment Planner 中的 VM 名称列表
](media/site-recovery-vmware-deployment-planner-run/profile-vm-list-v2a.png)

### <a name="start-profiling"></a>开始分析
创建要分析的 VM 的列表后，可在分析模式下运行该工具。 下面是在分析模式下运行该工具所要提供的必需和可选参数列表。

```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| 参数名称 | 说明 |
|---|---|
| -Operation | StartProfiling |
| -Server | 要分析其 VM 的 vCenter 服务器/vSphere ESXi 主机的完全限定域名或 IP 地址。|
| -User | 用于连接到 vCenter 服务器/vSphere ESXi 主机的用户名。 该用户至少需要拥有只读访问权限。|
| -VMListFile | 一个文件，其中包含要分析的 VM 的列表。 文件路径可以是绝对或相对路径。 此文件应该每行包含一个 VM 名称/IP 地址。 此文件中指定的虚拟机名称应与 vCenter 服务器/vSphere ESXi 主机上的 VM 名称相同。<br>例如，VMList.txt 文件包含以下 VM：<ul><li>virtual_machine_A</li><li>10.150.29.110</li><li>virtual_machine_B</li><ul> |
|-NoOfMinutesToProfile|运行分析的分钟数。 最小值为 30 分钟。|
|-NoOfHoursToProfile|运行分析的小时数。|
| -NoOfDaysToProfile | 运行分析的天数。 建议运行分析 7 天以上，确保在指定时间段内观察环境中的工作负荷模式，并根据该模式提供准确的建议。 |
|-Virtualization|指定虚拟化类型（VMware 或 Hyper-V）。|
| -Directory | （可选）通用命名约定 (UNC) 或本地目录路径，用于存储在分析期间生成的分析数据。 如果未指定目录名称，将使用当前路径下名为“ProfiledData”的目录作为默认目录。 |
| -Password | （可选）用于连接到 vCenter 服务器/vSphere ESXi 主机的密码。 如果现在不指定密码，则在执行命令时，系统会提示指定密码。|
|-Port|（可选）用于连接到 vCenter/ESXi 主机的端口号。 默认端口为 443。|
|-Protocol| （可选）用于连接到 vCenter 的指定协议，即“http”或“https”。 默认协议为 https。|
| -StorageAccountName | （可选）存储帐户名称，用于确定在将数据从本地复制到 Azure 时可实现的吞吐量。 该工具会将测试数据上传到此存储帐户来计算吞吐量。 存储帐户必须是常规用途 v1 (GPv1) 类型。 |
| -StorageAccountKey | （可选）用于访问存储帐户的存储帐户密钥。 转到 Azure 门户 >“存储帐户”> <*存储帐户名称*> >“设置”>“访问密钥”> 密钥 1。 |
| -Environment | （可选）这是目标 Azure 存储帐户环境。 此项可能采用下述三个值之一：AzureCloud、AzureUSGovernment、AzureChinaCloud。 默认值为 AzureCloud。 当目标 Azure 区域为 Azure 美国政府版或 Azure 中国云时，请使用此参数。 |


建议在分析 VM 时，分析 7 天以上。 如果变动量模式在某个月发生变化，建议在看到最大变动量的一周内进行分析。 最好的方式是分析 31 天，以便获取更好的建议。 在分析过程中，ASRDeploymentPlanner.exe 将保持运行。 该工具将取以天为单位的分析时间输入。 若要快速测试此工具或获取概念证明，可以分析数小时或数分钟。 允许的最短分析时间为 30 分钟。

在分析期间，可以选择性地传递存储帐户名称和密钥，确定在从配置服务器或进程服务器复制到 Azure 时，Site Recovery 可实现的吞吐量。 如果在分析期间不传递存储帐户名称和密钥，该工具不会计算可实现的吞吐量。

可以针对各个 VM 集运行该工具的多个实例。 确保不要在任何分析集中重复使用 VM 名称。 例如，如果已分析 10 个 VM（VM1 到 VM10），过几天后又想要分析另外 5 个 VM（VM11 到 VM15），则可通过另一个命令行控制台针对第二组 VM（VM11 到 VM15）运行该工具。 请确保第二组 VM 不包含第一个分析实例中的任何 VM 名称，或请确保为第二次运行使用不同的输出目录。 如果使用该工具的两个实例分析相同的 VM 并使用相同的输出目录，生成的报告将不准确。

默认情况下，此工具配置为在分析后为最多 1000 个 VM 生成报告。 若要更改限制，可以更改 *ASRDeploymentPlanner.exe.config* 文件中的 MaxVMsSupported 项值。
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
使用默认设置时，若要分析特定数量的 VM（例如 1500 个 VM），请创建两个 VMList.txt 文件。 一个包含 1000 个 VM，另一个是 500 个 VM 的列表。 运行 ASR 部署规划器的这两个实例，一个使用 VMList1.txt，另一个使用 VMList2.txt。 可以使用同一目录路径来存储两个 VMList VM 的已分析数据。

我们已经看到，根据硬件配置（尤其是在其中运行报表生成工具的服务器的 RAM 大小），在内存不足的情况下，操作可能会失败。 如果硬件良好，可将 MaxVMsSupported 更改为更高的值。  

如果有多个 vCenter 服务器，需为每个 vCenter 服务器运行一个 ASRDeploymentPlanner 实例，以便进行分析。

VM 配置会在分析操作开始时捕获一次，存储在名为 VMDetailList.xml 的文件中。 生成报告时使用此信息。 从分析开始到结束都不捕获 VM 配置中发生的任何更改（例如，核心、磁盘或 NIC 数增加）。 如果分析的 VM 配置在分析过程中发生了更改，则可在公共预览版中通过下述解决方法在生成报告时获取最新的 VM 详细信息：

* 备份 VMdetailList.xml 文件，并将其从当前位置删除。
* 生成报告时传递 -User 和 -Password 参数。

该分析命令在分析目录中生成多个文件。 请勿删除任何文件，因为这样做会影响报告生成。

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>示例 1：分析 VM 30 天，确定从本地到 Azure 的吞吐量
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>示例 2：分析 VM 15 天

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>示例 3：分析 VM 60 分钟以快速测试工具
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60  -User vCenterUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>示例 4：分析 VM 2 小时以获取概念证明
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfHoursToProfile 2 -User vCenterUser1
```

>[!NOTE]
>
>* 如果运行该工具的服务器重启或崩溃，或者使用 Ctrl + C 关闭该工具，系统会保存分析数据。 但是，过去 15 分钟的分析数据可能会丢失。 在这种情况下，请在服务器重新启动后以分析模式重新运行该工具。
>* 如果传递了存储帐户名称和密钥，该工具会在执行最后一个分析步骤时测量吞吐量。 如果在分析完成之前关闭该工具，则不会计算吞吐量。 若要在生成报告之前确定吞吐量，可通过命令行控制台运行 GetThroughput 操作。 否则，生成的报告将不包含吞吐量信息。


## <a name="generate-report"></a>生成报告
该工具生成一个启用了宏的 Microsoft Excel 文件（XLSM 文件）作为报告输出，对所有部署建议进行了汇总。 该报告名为 DeploymentPlannerReport_<unique numeric identifier>.xlsm，置于指定目录中。

完成分析后，可在报告生成模式下运行该工具。 下表包含一系列必需的和可选的工具参数，适用于在报告生成模式下 运行。

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|参数名称 | 说明 |
|-|-|
| -Operation | GenerateReport |
| -Server |  vCenter/vSphere 服务器完全限定域名或 IP 地址（使用的名称或 IP 地址与分析时使用的相同），其中包含需要生成其报告的已分析 VM。 请注意，如果在分析时使用了 vCenter 服务器，则不能使用 vSphere 服务器来生成报告，反之亦然。|
| -VMListFile | 一个文件，其中包含一系列需为其生成报告的已分析 VM。 文件路径可以是绝对或相对路径。 此文件应该每行包含一个 VM 名称或 IP 地址。 此文件中指定的 VM 名称应与 vCenter 服务器/vSphere ESXi 主机上的 VM 名称相同，并与分析时使用的名称匹配。|
|-Virtualization|指定虚拟化类型（VMware 或 Hyper-V）。|
| -Directory | （可选）UNC 或本地目录路径，其中存储了分析数据（在分析期间生成的文件）。 需要使用此数据来生成报告。 如果未指定名称，将使用“ProfiledData”目录。 |
| -GoalToCompleteIR | （可选）小时数，需在此时间段内完成已分析 VM 的初始复制。 生成的报告将提供可在指定时间内完成初始复制的 VM 数。 默认值为 72 小时。 |
| -User | （可选）用于连接到 vCenter/vSphere 服务器的用户名。 此名称用于获取要在报告中使用的最新 VM 配置信息，例如磁盘数、核心数、NIC 数。 如果未提供此名称，将使用开始分析时收集的配置信息。 |
| -Password | （可选）用于连接到 vCenter 服务器/vSphere ESXi 主机的密码。 如果密码未作为参数指定，则稍后在执行命令时，系统会提示指定。 |
|-Port|（可选）用于连接到 vCenter/ESXi 主机的端口号。 默认端口为 443。|
|-Protocol|（可选）用于连接到 vCenter 的指定协议，即“http”或“https”。 默认协议为 https。|
| -DesiredRPO | （可选）以分钟为单位的所需恢复点目标。 默认值为 15 分钟。|
| -Bandwidth | 以 Mbps 为单位的带宽。 一个参数，用于计算指定的带宽可实现的 RPO。 |
| -StartDate | （可选）采用 MM-DD-YYYY:HH:MM 格式（24 小时制）的开始日期和时间。 “StartDate”必须与“EndDate”一起指定。 如果指定 StartDate，会根据从 StartDate 到 EndDate 收集的分析数据生成报告。 |
| -EndDate | （可选）采用 MM-DD-YYYY:HH:MM 格式（24 小时制）的结束日期和时间。 “EndDate”必须与“StartDate”一起指定。 如果指定 EndDate，会根据从 StartDate 到 EndDate 收集的分析数据生成报告。 |
| -GrowthFactor | （可选）增长系数，以百分比表示。 默认值为 30%。 |
| -UseManagedDisks | （可选）UseManagedDisks - 是/否。 默认值为“是”。 计算可放置到单个存储帐户中的虚拟机数量时要考虑到：对虚拟机进行的故障转移/测试性故障转移是在托管磁盘而不是非托管磁盘上完成的。 |
|-SubscriptionId |（可选）订阅 GUID。 可以根据订阅、与订阅相关联的套餐、特定的目标 Azure 区域和指定的货币，按照最新的价格使用此参数生成成本估算报表。|
|-TargetRegion|（可选）充当复制目标的 Azure 区域。 由于 Azure 的成本因区域而异，因此可使用此参数来生成特定目标 Azure 区域的报表。<br>默认值为 WestUS2 或上次使用的目标区域。<br>请参阅[支持的目标区域](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions)的列表。|
|-OfferId|（可选）与给定订阅关联的套餐。 默认值为 MS-AZR-0003P（即用即付）。|
|-Currency|（可选）在生成的报表中显示的成本所采用的货币。 默认为美元 ($) 或上次使用的货币。<br>请参阅[支持的货币](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies)的列表。|

默认情况下，此工具配置为在分析后为最多 1000 个 VM 生成报告。 若要更改限制，可以更改 *ASRDeploymentPlanner.exe.config* 文件中的 MaxVMsSupported 项值。
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>示例 1：当分析数据位于本地驱动器上时，使用默认值生成报告
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>示例 2：当分析数据位于远程服务器上时生成报告
应该对远程目录拥有读/写访问权限。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>示例 3：生成一个报告，列出指定的带宽，以及在指定时间内完成 IR 的目标
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>示例 4：使用 5% 的增长系数而不是默认值 30% 来生成报告
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualzation VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>示例 5：使用分析数据的子集生成报告
例如，有 30 天的分析数据，但只想生成 20 天的报告。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>示例 6：生成 5 分钟 RPO 报告
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>示例 7：使用印度卢比和特定的产品/服务 ID，生成一个针对“印度南部”Azure区域的报表
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware  -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>用于计算的百分位值
**在生成报告时，该工具将使用分析期间收集的性能指标的哪个默认百分位值？**

该工具默认使用分析所有 VM 期间收集的读/写 IOPS、写入 IOPS 和数据变动量的第 95 百分位值。 此指标可确保系统不使用第 100 百分位峰值（在发生临时事件时，可能会出现在 VM 中）来确定目标存储帐户和源带宽需求。 例如，临时事件可能是一天运行一次的备份作业、定期发生的数据库索引编制或分析报告生成活动，或者其他类似的短期时间点事件。

使用第 95 百分位值可以反映真实的工作负荷特征，使这些工作负荷在 Azure 中运行时提供最佳性能。 我们预计不需要更改此数字。 如果更改此值（例如更改为第 90 百分位），则可在默认文件夹中更新并保存配置文件“ASRDeploymentPlanner.exe.config”，针对现有的分析数据生成新报告。
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>增长系数考虑因素
**为何在对部署进行计划时应考虑增长系数？**

假设使用量在一段时间内可能会增多，则考虑工作负荷特征的增长就至关重要。 在保护就位以后，如果工作负荷特征发生更改，则除非先禁用保护，再重新启用保护，否则在切换到其他存储帐户后将无法获得保护。

例如，假设你目前的 VM 适合标准存储复制帐户。 在随后的三个月中，可能会发生多项变化：

* 在 VM 上运行的应用程序的用户数会增加。
* 该 VM 上的变动量随之会增加，因此需使用高级存储，使 Site Recovery 复制能够跟上节奏。
* 结果就是，需要先禁用对高级存储帐户的保护，再重新启用该保护。

强烈建议在部署计划期间以及在默认值为 30% 的时候对增长进行计划。 对自己的应用程序使用模式和增长预测了解得最充分，生成报告时可以相应地更改此数字。 另外，可以使用相同的分析数据根据各种增长系数生成多份报告，确定哪些目标存储和源带宽建议最适合自己。

生成的 Microsoft Excel 报告包含以下信息：

* [本地摘要](site-recovery-vmware-deployment-planner-analyze-report.md#on-premises-summary)
* [建议](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)
* [VM<->存储位置](site-recovery-vmware-deployment-planner-analyze-report.md#vm-storage-placement)
* [兼容的 VM](site-recovery-vmware-deployment-planner-analyze-report.md#compatible-vms)
* [不兼容的 VM](site-recovery-vmware-deployment-planner-analyze-report.md#incompatible-vms)
* [成本估算](site-recovery-vmware-deployment-planner-cost-estimation.md)

![Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

## <a name="get-throughput"></a>获取吞吐量

若要估算从本地复制到 Azure 期间 Site Recovery 可实现的吞吐量，请在 GetThroughput 模式下运行该工具。 该工具计算的吞吐量源自运行该工具的服务器。 理想情况下，该服务器的配置遵循配置服务器大小调整指南。 如果已在本地部署 Site Recovery 基础结构组件，请在配置服务器上运行该工具。

打开命令行控制台，转到 Site Recovery 部署规划工具文件夹。 结合以下参数运行 ASRDeploymentPlanner.exe。

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|参数名称 | 说明 |
|-|-|
| -Operation | GetThroughput |
|-Virtualization|指定虚拟化类型（VMware 或 Hyper-V）。|
| -Directory | （可选）UNC 或本地目录路径，其中存储了分析数据（在分析期间生成的文件）。 需要使用此数据来生成报告。 如果未指定目录名称，请使用“ProfiledData”目录。 |
| -StorageAccountName | 存储帐户名称，用于确定在将数据从本地复制到 Azure 时消耗的带宽。 该工具会将测试数据上传到此存储帐户来确定消耗的带宽。 存储帐户必须是常规用途 v1 (GPv1) 类型。|
| -StorageAccountKey | 用于访问存储帐户的存储帐户密钥。 转到 Azure 门户 >“存储帐户”> <存储帐户名称> >“设置”>“访问密钥”> 密钥 1（或经典存储帐户的主访问密钥）。 |
| -VMListFile | 一个文件，其中包含一系列可以通过分析来计算所消耗带宽的 VM。 文件路径可以是绝对或相对路径。 此文件应该每行包含一个 VM 名称/IP 地址。 此文件中指定的 VM 名称应与 vCenter 服务器/vSphere ESXi 主机上的 VM 名称相同。<br>例如，VMList.txt 文件包含以下 VM：<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Environment | （可选）这是目标 Azure 存储帐户环境。 此项可能采用下述三个值之一：AzureCloud、AzureUSGovernment、AzureChinaCloud。 默认值为 AzureCloud。 当目标 Azure 区域为 Azure 美国政府版或 Azure 中国云时，请使用此参数。 |

该工具将在指定的目录中创建多个 64 MB 的 asrvhdfile<#>.vhd 文件（其中“#”是文件编号）。 该工具会将这些文件上传到存储帐户来确定吞吐量。 测出吞吐量后，该工具会从存储帐户和本地服务器中删除所有这些文件。 如果该工具在计算吞吐量时因故被终止，它不会从存储或本地服务器中删除这些文件。 需要手动删除这些文件。

吞吐量是根据指定时间点测量的，也是在其他所有系数保持相同的前提下，Site Recovery 可实现的最大吞吐量。 例如，如果任何应用程序在相同的网络中开始消耗更多的带宽，则在复制期间实际吞吐量会有所变化。 如果从配置服务器运行 GetThroughput 命令，该工具无法识别任何受保护的 VM 和正在进行的复制。 如果是在受保护 VM 的数据变动量高时运行 GetThroughput 操作，则所测吞吐量的结果会有所不同。 建议在分析期间的不同时间点运行该工具，了解在不同时间能够达到的吞吐量水平。 在报告中，该工具会显示最后一个测得的吞吐量。

### <a name="example"></a>示例
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> 在与配置服务器具有相同存储和 CPU 特征的服务器上运行该工具。
>
> 对于复制，请设置建议的带宽以满足 100% 时间的 RPO。 在设置适当的带宽后，如果工具所报告的已实现吞吐量没有增长，请执行以下操作：
>
>  1. 通过检查来确定是否有任何网络服务质量 (QoS) 限制了 Site Recovery 吞吐量。
>
>  2. 通过检查来确定 Site Recovery 保管库是否位于从物理上来说最近的受支持 Microsoft Azure 区域，以尽量降低网络延迟。
>
>  3. 检查本地存储特征，确定能否改进硬件（例如，从 HDD 升级到 SSD）。
>
>  4. 更改进程服务器中的 Site Recovery 设置，[增大用于复制的网络带宽量](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth)。

## <a name="next-steps"></a>后续步骤
* [分析生成的报表](site-recovery-vmware-deployment-planner-analyze-report.md)。
