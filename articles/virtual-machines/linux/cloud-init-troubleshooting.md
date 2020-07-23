---
title: 使用 cloud init 进行故障排除
description: 排查使用 cloud init 预配 Azure VM 的问题。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 81e138e7149327c7b792df58180419b93417d263
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86510967"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>VM 预配和云初始化疑难解答

如果已创建通用化自定义映像，使用 cloud init 进行预配，但发现该 VM 未正确创建，则需要对自定义映像进行故障排除。

有关设置的问题的一些示例：
- VM 在 "正在创建" 上停滞了40分钟，并且 VM 创建标记为失败
- `CustomData`不处理
- 临时磁盘装载失败
- 用户未创建，或存在用户访问问题
- 未正确设置网络
- 交换文件或分区故障

本文逐步说明如何排查云初始化问题。 有关更深入的详细信息，请参阅[云初始化深入探讨](./cloud-init-deep-dive.md)。

## <a name="step-1-test-the-deployment-without-customdata"></a>步骤1：测试部署，无需`customData`

`customData`创建 VM 时，可以接受将其传递给它的云初始化。 首先，应确保这不会导致任何部署问题。 尝试预配 VM，无需传入任何配置。 如果发现 VM 无法预配，请继续执行下面的步骤，如果发现未应用正在传递的配置，请转到[步骤 4]()。 

## <a name="step-2-review-image-requirements"></a>步骤2：查看映像要求
VM 设置失败的主要原因是操作系统映像不满足在 Azure 上运行的先决条件。 尝试在 Azure 中预配映像之前，请确保已正确准备好映像。 


以下文章说明了准备 Azure 中支持的各种 linux 分发的步骤：

- [基于 CentOS 的分发版](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES 和 openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [其他：非认可的分发版](create-upload-generic.md)

对于[支持的 azure 云初始化映像](./using-cloud-init.md)，Linux 分发版已经具备了所有必需的包和配置，以便在 Azure 中正确预配映像。 如果发现 VM 未能从自己的特选映像创建，请尝试使用可选的已配置为进行云初始化的受支持的 Azure Marketplace 映像 `customData` 。 如果 `customData` 使用 Azure Marketplace 映像可以正常工作，则可能是特选映像出现问题。

## <a name="step-3-collect--review-vm-logs"></a>步骤3：收集 & 查看 VM 日志

如果 VM 无法预配，Azure 将显示 "正在创建" 状态，20分钟后重新启动 VM，并等待20分钟，最后将 VM 部署标记为 "失败"，最后将其标记为 "错误" `OSProvisioningTimedOut` 。

VM 运行时，你将需要 VM 中的日志，以了解预配失败的原因。  若要了解 VM 设置失败的原因，请不要停止 VM。 让 VM 保持运行状态。 若要收集日志，需要使发生故障的 VM 处于运行状态。 若要收集日志，请使用以下方法之一：

- [串行控制台](./serial-console-grub-single-user-mode.md)

- 在创建 VM 之前[启用启动诊断](./tutorial-monitor.md#enable-boot-diagnostics)，然后在启动过程中[查看](./tutorial-monitor.md#view-boot-diagnostics)。

- [运行 AZ VM Repair](../troubleshooting/repair-linux-vm-using-azure-virtual-machine-repair-commands.md)附加并装载 OS 磁盘，这将允许你收集以下日志：
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
若要开始进行初始故障排除，请从云初始化日志开始，并了解发生故障的位置，然后使用其他日志深入探讨，并提供更多见解。 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* 串行/启动日志

在所有日志中，开始搜索 "失败"、"警告"、"警告"、"错误"、"错误" 和 "错误"。 建议将配置设置为忽略区分大小写的搜索。 

> [!TIP]
> 如果对自定义映像进行故障排除，应考虑在映像过程中添加用户。 如果预配无法设置管理员用户，你仍可以登录到操作系统。

## <a name="analyzing-the-logs"></a>分析日志

下面更详细地介绍了每个云初始化日志中要查找的内容。

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

默认情况下，将写入优先级为 "调试" 或更高的所有云初始化事件 `/var/log/cloud-init.log` 。 这提供了在云初始化初始化期间发生的每个事件的详细日志。 

例如：

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


找到错误或警告后，请在 cloud init 日志中向后阅读，以了解在出现错误或警告之前云初始化正在尝试执行的操作。 在许多情况下，云初始化将运行操作系统命令或在出现错误之前执行预配操作，这可以提供有关日志中出现错误的原因。 下面的示例演示云初始化尝试在出现错误之前装入设备。

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

如果有权访问[串行控制台](./serial-console-grub-single-user-mode.md)，可以尝试重新运行 cloud init 正在尝试运行的命令。

`/var/log/cloud-init.log`还可以在/etc/cloud/cloud.cfg.d/05_logging 中重新配置日志记录。 有关云初始化日志记录的详细信息，请参阅[云初始化文档](https://cloudinit.readthedocs.io/en/latest/topics/logging.html)。 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

你可以从中获取信息，也可以在 `stdout` `stderr` [云初始化的阶段](cloud-init-deep-dive.md)获取信息。 这通常涉及路由表信息、网络信息、ssh 主机密钥验证信息， `stdout` 以及 `stderr` 每个阶段的每个阶段，以及每个阶段的时间戳。 如果需要， `stderr` `stdout` 可以从中重新配置日志记录 `/etc/cloud/cloud.cfg.d/05_logging.cfg` 。

### <a name="serialboot-logs"></a>串行/启动日志 

云初始化具有多个依赖项，这些依赖项在 Azure 中的映像所需的必备组件（如网络、存储、安装 ISO 的能力）上记录，并装载临时磁盘并设置其格式。 其中的任何可能会引发错误，并导致云初始化失败。 例如，如果 VM 无法获得 DHCP 租约，则云初始化将失败。

如果仍无法隔离云初始化未能预配的原因，则需要了解云初始化阶段和模块运行的时间。 有关更多详细信息，请参阅深入了解[云 init](cloud-init-deep-dive.md) 。


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>步骤4：调查未应用配置的原因
并非云初始化中的每个失败都会导致严重的预配失败。 例如，如果在 `runcmd` 云初始化配置中使用模块，则在运行该命令的非零退出代码将导致 VM 预配失败。 这是因为它在云初始化的前3个阶段中发生的核心预配功能后运行。 若要解决配置未应用的原因，请手动查看步骤3中的日志和云初始化模块。 例如：

- `runcmd`-脚本是否运行不出错？ 手动从终端运行配置，以确保它们按预期运行。
- 正在安装包-VM 是否有权访问包存储库？
- 还应检查 `customData` 提供给 VM 的数据配置，这位于中 `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` 。


## <a name="next-steps"></a>后续步骤

如果你仍然无法隔离云初始化未运行配置的原因，你需要更密切地了解每个云初始化阶段中发生的情况，以及模块运行的时间。 有关详细信息，请参阅深入了解[云初始化配置](./cloud-init-deep-dive.md)。 
