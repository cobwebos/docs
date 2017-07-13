Azure 虚拟机 (VM) 有时可能会在没有明显原因（没有证据表明你已启动重启操作）的情况下重启。 本文列出了可导致 VM 重启的操作和事件，并针对如何避免意外重启问题或减少此类问题影响提供见解。

## 配置 VM 以实现高可用性
<a id="configure-the-vms-for-high-availability" class="xliff"></a>
若要防止 Azure 上运行的应用程序出现 VM 重启和停机问题，最佳方式是配置 VM 以实现高可用性。

若要为应用程序提供此级别的冗余，建议两个或更多 VM 组合到一个可用性集中。 这种配置可确保发生计划内或计划外维护事件时，至少有一个 VM 可用，并满足 99.95% 的 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/) 要求。

有关可用性集的详细信息，请参阅以下文章：

- [管理 VM 的可用性](../articles/virtual-machines/windows/manage-availability.md)
- [配置 VM 的可用性](../articles/virtual-machines/windows/classic/configure-availability.md)

## 资源运行状况信息
<a id="resource-health-information" class="xliff"></a> 
Azure 资源运行状况是一项服务，用于公开各个 Azure 资源的运行状况，并为排查问题提供可行的指南。 在不可直接访问服务器或基础结构元素的云环境中，资源运行状况的目标是减少你排除故障花费的时间。 具体说来，目的是减少确定问题根源在于应用程序还是在于 Azure 平台内事件所花的时间。 有关详细信息，请参阅[了解和使用资源运行状况](../articles/resource-health/resource-health-overview.md)。

## 可能导致 VM 重启的操作和事件
<a id="actions-and-events-that-can-cause-the-vm-to-reboot" class="xliff"></a>

### 计划内维护
<a id="planned-maintenance" class="xliff"></a>
Microsoft Azure 在全球范围内定期执行更新，提高 VM 所基于主机基础结构的可靠性、性能及安全性。 许多更新（包括内存保留更新）在执行时不会对 VM 或云服务产生任何影响。

但是，有些更新确实需要重启。 在这种情况下，VM 会在修补基础结构期间关闭，随后再重启。

若要了解什么是 Azure 计划内维护，及其如何影响 Linux VM 的可用性，请参阅下面列出的文章。 这些文章介绍了 Azure 计划内维护过程的背景，以及如何安排计划内维护以进一步减少影响。

- [Azure VM 的计划内维护](../articles/virtual-machines/windows/planned-maintenance.md)
- [如何在 Azure VM 上安排计划内维护](../articles/virtual-machines/windows/planned-maintenance-schedule.md)

### 内存保留更新
<a id="memory-preserving-updates" class="xliff"></a>   
对于 Microsoft Azure 中的这类更新，用户体验不到其对运行中的 VM 的任何影响。 其中一些更新主要面向组件或服务，更新时不会干扰正在运行的实例。 还有一些是主机操作系统上的平台基础结构更新，应用时无需重启 VM。

这些内存保留更新通过启用就地实时迁移技术实现。 进行更新时，VM 处于“暂停”状态。 该状态可保留 RAM 中的内存，基础主机操作系统则接收必要的更新和补丁。 VM 在暂停后 30 秒内恢复正常。 VM 恢复后，其时钟将自动同步。

由于暂停时间短，因此通过这种机制部署更新可以大大减少对 VM 的影响。 但是，并非所有更新都可通过这种方式部署。 

多实例更新（针对可用性集中的 VM）一次应用一个更新域。

> [!NOTE]
> 具有旧内核版本的 Linux 计算机在此更新方法期间受内核错误影响。 若要避免此问题，请更新到内核版本 3.10.0-327.10.1 或更高版本。 有关详细信息，请参阅[主机节点升级后基于 3.10 内核的 Azure Linux VM 出现错误](https://support.microsoft.com/help/3212236)。     
    
### 用户发起的重启或关闭操作
<a id="user-initiated-reboot-or-shutdown-actions" class="xliff"></a>
 
如果从 Azure 门户、Azure PowerShell、 命令行接口或重置 API 执行重启，则可在 [Azure 活动日志](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md)中找到该事件。

如果在 VM 的操作系统中执行该操作，则可在系统日志中找到该事件。

通常导致 VM 重启的其他方案包括多个配置更改操作。 通常会看到一条指示执行特定操作将导致 VM 重启的警告消息。 示例包括任意 VM 大小调整操作、更改管理帐户密码和设置静态 IP 地址。

### Azure 安全中心和 Windows 更新
<a id="azure-security-center-and-windows-update" class="xliff"></a>
Azure 安全中心每天对 Windows 和 Linux VM 进行监控，以找出缺少的操作系统更新。 安全中心从 Windows Update 或 Windows Server Update Services (WSUS) 检索可用的安全更新和关键更新的列表，具体取决于 Windows VM 上配置的服务。 安全中心还可检查 Linux 系统的最新更新。 如果 VM 缺少系统更新，安全中心会建议你应用系统更新。 通过 Azure 门户中的安全中心控制这些系统更新的应用情况。 应用某些更新后，可能需要重启 VM。 有关详细信息，请参阅[在 Azure 安全中心应用系统更新](../articles/security-center/security-center-apply-system-updates.md)。

与本地服务器一样，Azure 不会向 Microsoft Azure VM 推送 Windows 更新提供的更新，因为这些虚拟机应由用户进行管理。 但是，我们依然建议启用 Windows 自动更新设置。 自动安装 Windows 更新提供的更新也会导致应用更新后发生重启。 有关详细信息，请参阅 [Windows 更新常见问题解答](https://support.microsoft.com/help/12373/windows-update-faq)。

### 影响 VM 可用性的其他情况
<a id="other-situations-affecting-the-availability-of-your-vm" class="xliff"></a>
在其他情况下，Azure 可能主动暂停使用 VM。 你会在执行此操作前收到电子邮件通知，因此有机会解决该基础问题。 举例来说，影响 VM 可用性的问题包括：违反安全规范、付款方式过期。

### 主机服务器错误
<a id="host-server-faults" class="xliff"></a> 
在 Azure 数据中心内运行的物理服务器上托管 VM。 除了其他几个 Azure 组件外，物理服务器也运行名为“主机代理”的代理。 如果物理服务器上的这些 Azure 软件组件无响应，则监视系统会触发主机服务器重启，尝试恢复。 VM 通常在五分钟内再次可用，并继续像以前一样存在于同一主机上。

服务器错误通常由硬盘或固态硬盘等硬件故障引起。 Azure 持续监视这些事件，确定基础 bug，并在实现和测试缓解举措后推出更新。

由于某些主机服务器错误可能特定于该服务器，因此可通过手动将 VM 重新部署到其他主机服务器来改善 VM 重复重启的情况。 在 VM 详细信息页上使用“重新部署”选项，或在 Azure 门户中停止并重启 VM，可触发此操作。

### 自动恢复
<a id="auto-recovery" class="xliff"></a>
如果出于某种原因，主机服务器不能重启，Azure 平台会启动自动恢复操作，使发生故障的主机服务器脱离轮换，以便展开进一步调查。 

该主机上的所有 VM 均自动重新定位到其他运行正常的主机服务器。 此过程通常在 15 分钟内完成。 若要详细了解自动恢复过程，请参阅 [VM 自动恢复](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines)。

### 计划外维护
<a id="unplanned-maintenance" class="xliff"></a>
在少数情况下，Azure 运营团队可能需要执行维护活动，确保 Azure 平台总体上正常运行。 此行为可能会影响 VM 可用性，并且通常会导致相同的自动恢复操作，如前所述。  

计划外维护包括以下情况：

- 紧急节点碎片整理
- 紧急网络交换机更新

### VM 故障
<a id="vm-crashes" class="xliff"></a>
VM 可能因自身问题重启。 在 VM 上运行的工作负荷或角色可能触发来宾操作系统内的 Bug 检查。 为确定故障原因，请查看系统和应用程序日志（适用于 Windows VM）和串行日志（适用于 Linux VM）。

### 与存储相关的强制关机
<a id="storage-related-forced-shutdowns" class="xliff"></a>
对于在 Azure 存储基础结构上托管的操作系统和数据存储，Azure 中的 VM 依赖于虚拟磁盘。 只要可用性或者 VM 和关联的虚拟磁盘之间的连接受影响的时间超过 120 秒，Azure 平台就会强制关闭 VM，以免数据损坏。 存储连接还原后，VM 自动重启。 

关机持续时间可短至 5 分钟，也可能非常久。 下面是与存储相关的强制关机具体情况之一： 

超过 IO 限制

如果 I/O 请求因每秒输入/输出操作数 (IOPS) 超出磁盘 I/O 限制而持续受到限制，则可能暂时关闭 VM。 （标准磁盘存储的限制为 500 IOPS。）为缓解此问题，请在来宾 VM 中使用磁盘剥离或配置存储空间，具体情况取决于工作负荷。 有关详细信息，请参阅[配置 Azure VM 以获得最佳存储性能](http://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)。

通过 Azure 高级存储提供高达 80,000 IOPS 的 IOPS 限制。 有关详细信息，请参阅[高性能高级存储](../articles/storage/storage-premium-storage.md)。

### 其他事件
<a id="other-incidents" class="xliff"></a>
在极少数情况下，普遍的问题可能影响 Azure 数据中心内的多台服务器。 如果出现这种问题，Azure 团队会向受影响订阅者发送电子邮件通知。 可查看 [Azure 服务运行状况仪表板](https://azure.microsoft.com/status/)和 Azure 门户，了解正在进行的服务中断和过去事件的状态。
