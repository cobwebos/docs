---
title: Azure 文件性能故障排除指南
description: Azure 文件共享和相关解决方法的已知性能问题。
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 09e55abcd97317b87f8a272afa51c6b4ace572e8
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598079"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>排查 Azure 文件性能问题

本文列出了一些与 Azure 文件共享相关的常见问题。 当遇到这些问题时，它将提供潜在的原因和解决方法。

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>高延迟、低吞吐量和一般性能问题

### <a name="cause-1-share-experiencing-throttling"></a>原因1：共享遇到限制

高级共享上的默认配额为 100 GiB，它提供100的基线 IOPS （可能会突然增长到300一小时）。 有关预配及其与 IOPS 的关系的详细信息，请参阅规划指南中的 "[预配共享](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)" 部分。

若要确认共享是否受到限制，可以在门户中利用 Azure 指标。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择 "**所有服务**"，然后搜索 "**指标**"。

1. 选择“指标”。

1. 选择存储帐户作为资源。

1. 选择 "**文件**" 作为 "指标命名空间"。

1. 选择 "**事务**" 作为指标。

1. 为**ResponseType**添加筛选器并检查是否有任何请求的响应代码为**SuccessWithThrottling** （适用于 SMB）或**ClientThrottlingError** （适用于 REST）。

![高级文件共享的度量值选项](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> 若要在文件共享受到限制时接收警报，请参阅[如何在文件共享受到限制](#how-to-create-an-alert-if-a-file-share-is-throttled)的情况下创建警报。

### <a name="solution"></a>解决方案

- 通过在共享上指定较高的配额，增加共享预配的容量。

### <a name="cause-2-metadatanamespace-heavy-workload"></a>原因2：元数据/命名空间繁重的工作负荷

如果大多数请求都是以元数据为中心的（如 createfile/openfile/对应 closefile/queryinfo/querydirectory），则与读/写操作相比，延迟时间将更差。

若要确认大多数请求是否为以元数据为中心的，你可以使用与上述步骤相同的步骤。 除了为**ResponseType**添加筛选器外，请为**API 名称**添加筛选器。

![筛选度量值中的 API 名称](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>解决方法

- 检查是否可以修改应用程序以减少元数据操作的数量。
- 在文件共享上添加 VHD，并从客户端装载 VHD，以便对数据执行文件操作。 此方法适用于单个编写器和多个读取器方案，并允许元数据操作是本地的，提供与本地直连存储类似的性能。

### <a name="cause-3-single-threaded-application"></a>原因3：单线程应用程序

如果客户使用的应用程序是单线程的，则可能会根据预配的共享大小，使 IOPS/吞吐量明显低于最大值。

### <a name="solution"></a>解决方案

- 增加线程数以提高应用程序并行度。
- 切换到可能并行的应用程序。 例如，对于复制操作，客户可使用 Windows 客户端中的 AzCopy 或 RoboCopy，或 Linux 客户端上的**并行**命令。

## <a name="very-high-latency-for-requests"></a>请求的延迟非常长

### <a name="cause"></a>原因

客户端 VM 可能位于与文件共享不同的区域。

### <a name="solution"></a>解决方案

- 从与文件共享位于同一区域的 VM 运行该应用程序。

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>客户端无法实现网络支持的最大吞吐量

这种情况的一个可能原因是缺少 SMB 多通道支持。 目前，Azure 文件共享仅支持单个通道，因此从客户端 VM 到服务器的连接只有一个。 此单一连接限定为客户端 VM 上的单个内核，因此可从 VM 实现的最大吞吐量由单个核心绑定。

### <a name="workaround"></a>解决方法

- 获取更大核心的 VM 可能有助于提高吞吐量。
- 从多个 Vm 运行客户端应用程序会增加吞吐量。

- 尽可能使用 REST Api。

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>与 Windows 客户端相比，Linux 客户端上的吞吐量大大降低。

### <a name="cause"></a>原因

这是在 Linux 上实现 SMB 客户端的已知问题。

### <a name="workaround"></a>解决方法

- 跨多个 Vm 分散负载。
- 在同一 VM 上，使用具有**nosharesock**选项的多个装入点，并将负载分散到这些装入点。
- 在 Linux 上，尝试装载**nostrictsync**选项，以避免在每个**fsync**调用上强制进行 SMB 刷新。 对于 Azure 文件，此选项不会影响数据一致性，但可能会导致目录列表（**ls-l**命令）上的文件元数据过时。 直接查询文件的元数据（**stat**命令）将返回最新的文件元数据。

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>涉及大量打开/关闭操作的元数据繁重工作负荷的高延迟。

### <a name="cause"></a>原因

缺少目录租约支持。

### <a name="workaround"></a>解决方法

- 如果可能，请避免在较短的时间内在同一目录中出现过多的打开/关闭句柄。
- 对于 Linux Vm，请通过将**actimeo =\<> sec**指定为装载选项来增加目录条目缓存超时。 默认情况下，它是一秒，因此更大的值（如三或5）可能会有所帮助。
- 对于 Linux Vm，请将内核升级到4.20 或更高版本。

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL 上的低 IOPS

### <a name="cause"></a>原因

CentOS/RHEL 上不支持大于1的 IO 深度。

### <a name="workaround"></a>解决方法

- 升级到 CentOS 8/RHEL 8。
- 更改为 Ubuntu。

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>在 Linux 中将文件复制到 Azure 文件以及从中复制文件时速度缓慢

如果在复制到 Azure 文件和从 Azure 文件复制速度缓慢，请查看 Linux 故障排除指南中的 "将[Azure 文件复制到 linux 或从 Azure 文件复制到 Azure 文件的速度缓慢](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)" 部分。

## <a name="jitterysaw-tooth-pattern-for-iops"></a>IOPS 抖动/锯模式

### <a name="cause"></a>原因

客户端应用程序始终超过基线 IOPS。 目前，请求负载没有服务端，因此，如果客户端超过基线 IOPS，则服务会对其进行限制。 此限制可能会导致客户端遇到抖动/牙的 IOPS 模式。 在这种情况下，客户端实现的平均 IOPS 可能低于基线 IOPS。

### <a name="workaround"></a>解决方法

- 减少客户端应用程序的请求负载，以便不会限制共享。
- 增加共享配额，以便不会限制共享。

## <a name="excessive-directoryopendirectoryclose-calls"></a>过度 DirectoryOpen/DirectoryClose 调用

### <a name="cause"></a>原因

如果 DirectoryOpen/DirectoryClose 调用数在最高 API 调用之间，并且你不希望客户端发出多次调用，则可能是在 Azure 客户端 VM 上安装了防病毒软件的问题。

### <a name="workaround"></a>解决方法

- 适用于[Windows 的四月平台更新](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)中提供了此问题的修补程序。

## <a name="file-creation-is-slower-than-expected"></a>文件创建速度比预期要慢

### <a name="cause"></a>原因

依赖于创建大量文件的工作负荷不会在高级文件共享和标准文件共享的性能之间出现明显差异。

### <a name="workaround"></a>解决方法

- 无。

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 或服务器 2012 R2 性能降低

### <a name="cause"></a>原因

对于 IO 密集型工作负荷，访问 Azure 文件的延迟更高。

### <a name="workaround"></a>解决方法

- 安装可用的[修补程序](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)。

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>如果文件共享受到限制，如何创建警报

1. 在[Azure 门户](https://portal.azure.com)中，单击 "**监视器**"。 

2. 单击 "**警报**"，然后单击 " **+ 新建警报规则**"。

3. 单击 "**选择**" 以选择包含要进行警报的文件共享的**存储帐户/文件**资源，然后单击 "**完成**"。 例如，如果存储帐户名称为 "contoso"，则选择 "contoso/文件" 资源。

4. 单击 "**添加**" 以添加条件。

5. 你会看到存储帐户支持的信号列表，请选择 "**事务**" 指标。

6. 在 "**配置信号逻辑**" 边栏选项卡上，单击 "**响应类型**" 维度，单击 "**维度值**" 下拉箭头，然后选择 " **SuccessWithThrottling** " （适用于 SMB）或 " **ClientThrottlingError** （适用于 REST）"。 

  > [!NOTE]
  > 如果 "SuccessWithThrottling" 或 "ClientThrottlingError" 维度值未列出，则表示资源尚未被限制。  若要添加维度值，请单击 "**维度值**" 下拉箭头旁边的 " **+** "，键入 " **SuccessWithThrottling** " 或 " **ClientThrottlingError**"，单击 **"确定"** ，然后重复步骤 #6。

7. 中转到 "**文件共享**" 维度，单击 "**维度值**" 下拉箭头，然后选择要对其发出警报的文件共享。 

  > [!NOTE]
  > 如果文件共享是标准文件共享，则 "维度值" 下拉将为空，因为 "每个共享" 度量值不可用于标准文件共享。 如果存储帐户中的任何文件共享受到限制，并且警报不会确定限制了哪个文件共享，则会触发标准文件共享的限制警报。 由于每个共享度量值不可用于标准文件共享，因此建议每个存储帐户都有一个文件共享。 

8. 定义用于评估指标警报规则的**警报参数**（阈值、运算符、聚合粒度和频率），然后单击 "**完成**"。

  > [!TIP]
  > 如果使用的是静态阈值，则在当前限制文件共享的情况下，度量值图表可帮助确定合理的阈值。 如果使用的是动态阈值，则度量值图表将根据最近的数据显示计算的阈值。

9. 选择现有操作组或创建新的操作组，将**操作组**（电子邮件、短信等）添加到警报。

10. 填写警报**详细信息**，如**警报规则名称**、**描述**和**严重性**。

11. 单击 "**创建警报规则**" 以创建警报。

若要了解有关在 Azure Monitor 中配置警报的详细信息，请参阅[Microsoft Azure 中的警报概述]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。
