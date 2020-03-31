---
title: Azure 文件存储性能故障排除指南
description: Azure 文件共享的已知性能问题和相关解决方法。
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 09e55abcd97317b87f8a272afa51c6b4ace572e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598079"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>排查 Azure 文件存储性能问题

本文列出了与 Azure 文件共享相关的一些常见问题。 其中提供了这些问题的潜在原因和解决方法。

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>高延迟、低吞吐量和一般性能问题

### <a name="cause-1-share-experiencing-throttling"></a>原因 1：共享体验限制

高级共享上的默认配额为 100 GiB，这可以提供 100 个基线 IOPS （一小时内可能会激增到 300）。 有关预配及其与 IOPS 之间的关系的详细信息，请参阅规划指南中的[预配的共享](storage-files-planning.md#understanding-provisioning-for-premium-file-shares)部分。

若要确认共享是否受到限制，可以利用门户中的“Azure 指标”。

1. 登录到 Azure[门户](https://portal.azure.com)。

1. 选择“所有服务”，然后搜索“指标”********。

1. 选择“指标”****。

1. 选择你的存储帐户作为资源。

1. 选择“文件”作为指标命名空间。****

1. 选择“事务”作为指标。****

1. 添加 **ResponseType** 的筛选器，并检查是否有任何请求的响应代码为 **SuccessWithThrottling**（适用于 SMB）或 **ClientThrottlingError**（适用于 REST）。

![高级文件共享的指标选项](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> 要在文件共享被限制时接收警报，请参阅[如何在文件共享被限制时创建警报](#how-to-create-an-alert-if-a-file-share-is-throttled)。

### <a name="solution"></a>解决方案

- 通过在共享中指定更高的配额来增大共享预配的容量。

### <a name="cause-2-metadatanamespace-heavy-workload"></a>原因 2：元数据/命名空间繁重的工作负载

如果大多数请求以元数据为中心（例如 createfile/openfile/closefile/queryinfo/querydirectory），则与读/写操作相比，延迟将会更严重。

若要确认大多数请求是否以元数据为中心，可以使用上述相同的步骤。 不要添加 **ResponseType** 的筛选器，而是添加 **API 名称**的筛选器。

![筛选指标中的 API 名称](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>解决方法

- 检查是否可以修改应用程序来减少元数据操作的数量。
- 在文件共享上添加 VHD，并从客户端通过 SMB 装载 VHD，以便对数据执行文件操作。 此方法适用于单个写入器和多个读取器方案，并允许元数据操作在本地进行，提供与本地直连存储类似的性能。

### <a name="cause-3-single-threaded-application"></a>原因 3：单线程应用程序

如果客户使用的应用程序是单线程的，这可能会导致 IOPS/吞吐量明显低于最大可能的值，具体取决于预配的共享大小。

### <a name="solution"></a>解决方案

- 通过增加线程数来提高应用程序的并行度。
- 切换到支持并行度的应用程序。 例如，对于复制操作，客户可以在 Windows 客户端中使用 AzCopy 或 RoboCopy，或者在 Linux 客户端中使用 **parallel** 命令。

## <a name="very-high-latency-for-requests"></a>请求的延迟很高

### <a name="cause"></a>原因

客户端 VM 所在的区域可能与文件共享所在的区域不同。

### <a name="solution"></a>解决方案

- 从与文件共享位于同一区域的 VM 运行应用程序。

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>客户端无法实现网络支持的最大吞吐量

此问题的潜在原因之一是缺少 SMB 多通道支持。 目前，Azure 文件共享仅支持单个通道，因此只会建立从客户端 VM 到服务器的一个连接。 此单一连接限定为客户端 VM 上的单一核心，因此，可从 VM 实现的最大吞吐量受限于单个核心。

### <a name="workaround"></a>解决方法

- 获取核心更大的 VM 可能有助于提高吞吐量。
- 从多个 VM 运行客户端应用程序会提高吞吐量。

- 尽可能地使用 REST API。

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>与 Windows 客户端相比，Linux 客户端上的吞吐量要低得多。

### <a name="cause"></a>原因

这是 Linux 上实施的 SMB 客户端的一个已知问题。

### <a name="workaround"></a>解决方法

- 跨多个 VM 分散负载。
- 在同一 VM 上，通过 **nosharesock** 选项使用多个装入点，并将负载分散到这些装入点。
- 在 Linux 上，尝试安装**带有无严格同步**选项，以避免强制 SMB 刷新每个**fsync**调用。 对于 Azure 文件，此选项不会干扰数据一致性，但可能会导致目录列表 **（ls-l**命令） 上的文件元数据陈旧。 直接查询文件的元数据（**stat** 命令）会返回最新的文件元数据。

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>涉及大量打开/关闭操作的元数据密集型工作负荷出现较高的延迟。

### <a name="cause"></a>原因

缺少目录租约支持。

### <a name="workaround"></a>解决方法

- 如果可能，请避免短时间内在同一目录中使用过多的打开/关闭句柄。
- 对于 Linux VM，请指定 **actimeo=\<sec>** 作为装载选项，以增大目录条目缓存超时。 默认情况下，该超时为 1 秒，使用更大的值（例如 3 或 5）可能有所帮助。
- 对于 Linux VM，请将内核升级到 4.20 或更高版本。

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL 上的 IOPS 较低

### <a name="cause"></a>原因

CentOS/RHEL 不支持大于 1 的 IO 深度。

### <a name="workaround"></a>解决方法

- 升级到 CentOS 8/RHEL 8。
- 改用 Ubuntu。

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>在 Linux 中将文件复制到 Azure 文件以及从中复制文件时速度缓慢

如果在向/从 Azure 文件复制文件时速度缓慢，请查看 Linux 故障排除指南中的[在 Linux 中向/从 Azure 文件复制文件时速度缓慢](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)部分。

## <a name="jitterysaw-tooth-pattern-for-iops"></a>IOPS 出现抖动/锯齿模式

### <a name="cause"></a>原因

客户端应用程序总是超过基线 IOPS。 目前，请求负载没有服务端平滑处理，因此，如果客户端超过基线 IOPS，则服务会对其进行限制。 该限制可能导致客户端遇到抖动/锯齿 IOPS 模式。 在这种情况下，客户端实现的平均 IOPS 可能低于基线 IOPS。

### <a name="workaround"></a>解决方法

- 减少客户端应用程序的请求负载，使共享不会受到限制。
- 提高共享配额，使共享不会受到限制。

## <a name="excessive-directoryopendirectoryclose-calls"></a>过多的 DirectoryOpen/DirectoryClose 调用

### <a name="cause"></a>原因

如果最频繁的 API 调用中包括 DirectoryOpen/DirectoryClose 调用，而你预计客户端不会发出这么多的调用，则问题可能与 Azure 客户端 VM 上安装的防病毒软件有关。

### <a name="workaround"></a>解决方法

- [Windows 四月平台更新](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)中提供了此问题的修复措施。

## <a name="file-creation-is-slower-than-expected"></a>文件创建速度慢于预期

### <a name="cause"></a>原因

依赖于创建大量文件的工作负荷在高级文件共享和标准文件共享中的性能没有明显差异。

### <a name="workaround"></a>解决方法

- 无。

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>在 Windows 8.1 或 Server 2012 R2 中的性能不佳

### <a name="cause"></a>原因

对于 IO 密集型工作负荷，访问 Azure 文件时的延迟要高于预期。

### <a name="workaround"></a>解决方法

- 安装可用的[修补程序](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)。

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>如果文件共享受到限制，如何创建警报

1. 在[Azure 门户](https://portal.azure.com)中，单击**监视器**。 

2. 单击 **"警报"，** 然后单击 **"新警报规则**"。

3. 单击 **"选择"** 以选择包含要提醒的文件共享的**存储帐户/文件**资源，然后单击"**完成**"。 例如，如果存储帐户名称是 contoso，请选择 contoso/文件资源。

4. 单击"**添加**"以添加条件。

5. 您将看到存储帐户支持的信号列表，选择**交易记录**指标。

6. 在 **"配置信号逻辑**"边栏选项卡上，转到**响应类型**维度，单击"**维度值**下拉"，然后选择 **"成功与旋转**（用于 SMB）"或 **"客户端旋转错误**"（用于 REST）。 

  > [!NOTE]
  > 如果未列出成功与旋转或客户端限制错误维度值，则表示资源未被限制。  要添加维度值，**+** 请单击 **"维度值**"旁边的下拉列表，键入 **"成功与旋转**"或 **"客户端旋转错误**"，单击"**确定**"，然后#6重复步骤。

7. 转到 **"文件共享**"维度，单击"**维度"值**下拉列表，然后选择要提醒的文件共享。 

  > [!NOTE]
  > 如果文件共享是标准文件共享，则维度值下拉将为空，因为每股指标不适用于标准文件共享。 如果存储帐户中的任何文件共享受到限制，并且警报不会标识哪个文件共享被限制，将触发标准文件共享的限制警报。 由于每股指标不适用于标准文件共享，因此建议每个存储帐户有一个文件共享。 

8. 定义用于评估指标警报规则的**警报参数**（阈值、运算符、聚合粒度和频率），然后单击 **"完成**"。

  > [!TIP]
  > 如果使用静态阈值，则指标图表可帮助确定文件共享当前被限制的合理阈值。 如果使用动态阈值，则指标图表将显示基于最近数据的计算阈值。

9. 通过选择现有操作组或创建新操作组，将**操作组**（电子邮件、SMS 等）添加到警报中。

10. 填写**警报详细信息**，如**警报规则名称**、**描述**和**严重性**。

11. 单击 **"创建警报规则**"以创建警报。

要了解有关在 Azure 监视器中配置警报的详细信息，请参阅[Microsoft Azure 中的警报概述]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。
