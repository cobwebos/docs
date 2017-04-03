本文概述了在 Azure 上运行 Linux 虚拟机 (VM) 的一套经过验证的做法，这些做法注重可扩展性、可用性、可管理性和安全性。 Azure 支持运行大量常用的 Linux 分发，包括 CentOS、Debian、Red Hat Enterprise、Ubuntu 和 FreeBSD。 有关详细信息，请参阅 [Azure 和 Linux][azure-linux]。

> [!NOTE]
> Azure 提供两个不同的部署模型：[Resource Manager][resource-manager-overview] 和经典模型。 本文使用 Resource Manager，Microsoft 建议将它用于新部署。
> 
> 

不建议针对任务关键型工作负荷使用单个 VM，因为这会产生单一故障点。 为了提高可用性，请在[可用性集][availability-set]中部署多个 VM。 有关详细信息，请参阅 [Running multiple VMs on Azure][multi-vm]（在 Azure 上运行多个 VM）。 

## <a name="architecture-diagram"></a>体系结构关系图
在 Azure 中预配 VM 涉及更多移动部件，而不只是 VM 本身。 需要考虑的因素有计算、网络和存储。

> 可从 [Microsoft 下载中心][visio-download]下载包含此体系结构图的 Visio 文档。 此示意图位于“Compute - single VM”（计算 - 单个 VM”页中。
> 
> 

![[0]][0]

* **资源组。** [*资源组*][resource-manager-overview]是保存相关资源的容器。 创建资源组以保存此 VM 的资源。
* **VM**。 可以基于已发布的映像列表或上载到 Azure Blob 存储的虚拟硬盘 (VHD) 文件预配 VM。
* **OS 磁盘。** OS 磁盘是存储在 [Azure 存储][azure-storage]中的一个 VHD。 这意味着它一直存在，即使主机出现故障，也是如此。 OS 磁盘是 `/dev/sda1`。
* **临时磁盘。** 使用临时磁盘创建 VM。 此磁盘存储在主机的物理驱动器上。 它*不会*保存在 Azure 存储中，重新启动和发生其他 VM 生命周期事件期间，它可能会被删除。 只使用此磁盘存储临时数据，如页面文件或交换文件。 临时磁盘是 `/dev/sdb1`，并在 `/mnt/resource` 或 `/mnt` 中装入。
* **数据磁盘。** [数据磁盘][data-disk]是用于保存应用程序数据的持久性 VHD。 数据磁盘像 OS 磁盘一样，存储在 Azure 存储中。
* **虚拟网络 (VNet) 和子网。** Azure 中的每个 VM 都部署在 VNet 中，后者进一步划分为多个子网。
* **公共 IP 地址。** 公共 IP 地址需要与 VM 通信 &mdash; 例如，通过 SSH。
* **网络接口 (NIC)**。 NIC 使 VM 能够与虚拟网络进行通信。
* **网络安全组 (NSG)**。 [NSG][nsg] 用于允许/拒绝到子网的网络流量。 可以将 NSG 与单个 NIC 或与子网相关联。 如果将 NSG 与一个子网相关联，则 NSG 规则适用于该子网中的所有 VM。
* **诊断。** 诊断日志记录对于 VM 管理和故障排除至关重要。

## <a name="recommendations"></a>建议

以下建议适用于大多数方案。 除非有优先于这些建议的特定要求，否则请遵循这些建议。 

### <a name="vm-recommendations"></a>VM 建议

Azure 可提供多种虚拟机大小，但建议使用 DS 和 GS 系列，因为相关计算机大小支持[高级存储][premium-storage]。 除非运行专用工作负荷（例如高性能计算），否则请选择其中的一种计算机大小。 有关详细信息，请参阅[虚拟机大小][virtual-machine-sizes]。

如果要将现有工作负荷转移到 Azure，开始时请先使用与本地服务器最匹配的 VM 大小。 然后测量与 CPU、内存和每秒磁盘输入/输出操作次数 (IOPS) 有关的实际工作负荷的性能，并根据需要调整大小。 如果 VM 需要多个 NIC，请注意 NIC 的最大数量取决于 [VM 大小][vm-size-tables]。

在预配 VM 和其他资源时，必须指定区域。 通常应选择离内部用户或客户最近的区域。 但是，并非所有 VM 大小都可在所有区域中使用。 有关详细信息，请参阅[服务（按区域）][services-by-region]。 若要列出给定区域中的可用 VM 大小，请运行以下 Azure 命令行接口 (CLI) 命令：

```
azure vm sizes --location <location>
```

若要了解如何选择发布的 VM 映像，请参阅[利用 Azure CLI 选择 Linux VM 映像][select-vm-image]。

### <a name="disk-and-storage-recommendations"></a>磁盘和存储建议

为获得最佳磁盘 I/O 性能，建议使用[高级存储][premium-storage]，它在固态硬盘 (SSD) 上存储数据。 成本取决于预配磁盘的大小。 IOPS 和吞吐量（即数据传输速率）也取决于磁盘大小，因此在预配磁盘时，请全面考虑三个因素（容量、IOPS 和吞吐量）。 

为每个 VM 创建单独的 Azure 存储帐户，用于存放虚拟硬盘 (VHD)，从而避免存储帐户达到 IOPS 限制。 

添加一个或多个数据磁盘。 刚创建的 VHD 尚未格式化， 请登录 VM 格式化该磁盘。 在 Linux shell 中，数据磁盘显示为 `/dev/sdc`、`/dev/sdd` 等。 你可以运行 `lsblk` 以列出块设备，包括磁盘。 若要使用数据磁盘，请创建一个分区和文件系统，然后装载磁盘。 例如：

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

如果你有大量数据磁盘，请注意存储帐户的总 I/O 限制。 有关详细信息，请参阅[虚拟机磁盘限制][vm-disk-limits]。

在添加数据磁盘时，将为磁盘分配逻辑单元号 (LUN) ID。 或者，可以指定 LUN ID &mdash; 例如，若要更换磁盘并保留相同的 LUN ID，或者应用程序要查找特定 LUN ID。 但请记住，每个磁盘的 LUN ID 必须唯一。

你可能想要更改 I/O 计划程序，以便针对 SSD 的性能进行优化，因为使用高级存储帐户的 VM 的磁盘是 SSD。 常见的建议是对 SSD 使用 NOOP 计划程序，但应使用 [iostat] 等工具来监视特定工作负荷的磁盘 I/O 性能。

为获得最佳性能，请创建单独的存储帐户来存储诊断日志。 标准的本地冗余存储 (LRS) 帐户足以存储诊断日志。

### <a name="network-recommendations"></a>网络建议

公共 IP 地址可以是动态的或静态的。 默认是动态的。

* 如果需要不会更改的固定 IP 地址（例如，如果需要在 DNS 中创建 A 记录，或者需要将 IP 地址添加到安全列表），请保留[静态 IP 地址][static-ip]。
* 你还可以为 IP 地址创建完全限定域名 (FQDN)。 然后，可在 DNS 中注册指向 FQDN 的 [CNAME 记录][cname-record]。 有关详细信息，请参阅[在 Azure 门户中创建完全限定的域名][fqdn]。

所有 NSG 都包含一组[默认规则][nsg-default-rules]，其中包括阻止所有入站 Internet 流量的规则。 无法删除默认规则，但其他规则可以覆盖它们。 若要启用 Internet 流量，请创建允许特定端口的入站流量的规则 &mdash; 例如，将端口 80 用于 HTTP。  

若要启用 SSH，请向 NSG 添加允许 TCP 端口 22 的入站流量的规则。

## <a name="scalability-considerations"></a>可伸缩性注意事项

若要扩展或缩减，请参阅[更改 VM 大小][vm-resize]。 

若要水平扩大，请将两个或更多 VM 放入负载均衡器后面的可用性集中。 有关详细信息，请参阅[在 Azure 上运行多个 VM][multi-vm]。

## <a name="availability-considerations"></a>可用性注意事项

为了提高可用性，请在可用性集中部署多个 VM。 这样还可提供更高的[服务级别协议][vm-sla] (SLA)。 

VM 可能会受到[计划内维护][planned-maintenance]或[计划外维护][manage-vm-availability]的影响。 可以使用 [VM 重新启动日志][reboot-logs]来确定 VM 重新启动是否是由计划内维护导致的。

VHD 存储在 [Azure 存储][azure-storage]中，Azure 存储会复制，实现持久性和可用性。

若要防止在正常操作期间意外数据丢失（例如，由于用户错误），则还应使用 [Blob 快照][blob-snapshot]或其他工具实现时间点备份。

## <a name="manageability-considerations"></a>可管理性注意事项

**资源组。** 将共享相同生命周期的紧密耦合资源放入同一[资源组][resource-manager-overview]中。 资源组可让你以组的形式部署和监视资源，并按资源组汇总计费成本。 你还可以删除作为集的资源，这对于测试部署非常有用。 为资源指定有意义的名称。 这样，可更轻松地找到特定资源并了解其角色。 请参阅 [Azure 资源的建议命名约定][naming conventions]。

**SSH**。 在创建 Linux VM 之前，生成 2048 位 RSA 公共/专用密钥对。 创建 VM 时，使用公钥文件。 有关详细信息，请参阅[如何在 Azure 中将 SSH 与 Linux 和 Mac 配合使用][ssh-linux]。

**VM 诊断。** 启用监视和诊断，包括基本运行状况指标、诊断基础结构日志和[启动诊断][boot-diagnostics]。 如果你的 VM 陷入不可启动状态，启动诊断有助于诊断启动故障。 有关详细信息，请参阅[启用监视和诊断][enable-monitoring]。  

以下 CLI 命令可启用诊断：

```
azure vm enable-diag <resource-group> <vm-name>
```

**停止 VM。** Azure 对“已停止”和“已解除分配”状态做了区分。 当 VM 状态为已停止时（而不是当 VM 已解除分配时）将向你收费。

使用以下 CLI 命令可解除分配 VM：

```
azure vm deallocate <resource-group> <vm-name>
```

在 Azure 门户中，“停止”按钮可解除分配 VM。 但是，如果在已登录时通过 OS 关闭，VM 将会停止，但*不*会解除分配，因此仍会产生费用。

**删除 VM。** 如果你删除 VM，则不会删除 VHD。 这意味着你可以安全地删除 VM，而不会丢失数据。 但是，仍将向你收取存储费用。 若要删除 VHD，请从 [Blob 存储][blob-storage]中删除相应的文件。

若要防止意外删除，请使用[资源锁][resource-lock]锁定整个资源组或锁定单个资源（如 VM）。 

## <a name="security-considerations"></a>安全注意事项

使用 [OSPatching] VM 扩展自动执行 OS 更新。 预配 VM 时，安装此扩展。 你可以指定安装修补程序的频率以及修补后是否要重新启动。

使用[基于角色的访问控制][rbac] (RBAC) 来控制对部署的 Azure 资源的访问。 RBAC 允许你将授权角色分配给开发运营团队的成员。 例如，“读者”角色可以查看 Azure 资源，但不能创建、管理或删除这些资源。 某些角色特定于特定的 Azure 资源类型。 例如，“虚拟机参与者”角色可以执行重启或解除分配 VM、重置管理员密码、创建 VM 等操作。 可能对此参考体系结构有用的其他[内置 RBAC 角色][rbac-roles]包括[开发测试实验室用户][rbac-devtest]和[网络参与者][rbac-network]。 

可将用户分配给多个角色，并且可以创建自定义角色以实现更细化的权限。

d> [!NOTE]
> RBAC 不限制已登录到 VM 的用户可以执行的操作。 这些权限由来宾 OS 上的帐户类型决定。   
> 
> 

使用[审核日志][audit-logs]可查看预配操作和其他 VM 事件。

如果需要加密 OS 磁盘和数据磁盘，请考虑使用 [Azure 磁盘加密][disk-encryption]。 

## <a name="solution-deployment"></a>解决方案部署
[GitHub][github-folder] 中提供了此参考体系结构的部署。 它包括一个 VNet、NSG 和单个 VM。 若要部署该体系结构，请执行以下步骤： 

1. 右键单击下面的按钮，然后选择“在新标签页中打开链接”或“在新窗口中打开链接”。
   [![部署到 Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. 在 Azure 门户中打开该链接后，必须输入某些设置的值： 
   
   * 参数文件中已定义**资源组**名称，因此请选择“新建”，然后在文本框中输入 `ra-single-vm-rg`。
   * 从“位置”下拉框中选择区域。
   * 不要编辑“模板根 URI”或“参数根 URI”文本框。
。   在 OS 类型*下拉框中，选择“Linux”。
   * 查看条款和条件，然后单击“我同意上述条款和条件”复选框。
   * 单击“购买”按钮。
3. 等待部署完成。
4. 参数文件包括硬编码管理员用户名和密码，强烈建议你立即更改它们。 在 Azure 门户中单击名为 `ra-single-vm0 ` 的 VM。 然后，单击“支持 + 故障排除”部分中的“重置密码”。 在“模式”下拉框中选择“重置密码”，然后选择新**用户名**和**密码**。 单击“更新”按钮保存新用户名和密码。

## <a name="next-steps"></a>后续步骤
为了提高可用性，请在负载均衡器后面部署两个或更多个 VM。 有关详细信息，请参阅 [Running multiple VMs on Azure][multi-vm]（在 Azure 上运行多个 VM）。

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]:../articles/virtual-machines/windows/create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]:../articles/virtual-machines/linux/overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/storage/storage-about-disks-and-vhds-linux.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]:../articles/virtual-machines/linux/portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]:../articles/virtual-machines/linux/manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]:../articles/virtual-machines/linux/planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-labs-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]:../articles/virtual-machines/linux/cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]:../articles/virtual-machines/linux/mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]:../articles/virtual-machines/linux/sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]:../articles/virtual-machines/linux/change-vm-size.md
[vm-size-tables]:../articles/virtual-machines/windows/sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Azure 中的单一 Linux VM 体系结构"

