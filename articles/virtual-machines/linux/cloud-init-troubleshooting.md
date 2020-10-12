---
title: 使用 cloud-init 时进行故障排除
description: 使用 cloud-init 预配 Azure VM 时进行故障排除。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6412036e3f16e2efb3bbf6669f6a31e9dc6e3584
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89434633"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>对使用 cloud-init 的 VM 预配进行故障排除

如果你已在创建通用化自定义映像（使用 cloud-init 进行预配），但发现 VM 未正确创建，则需对自定义映像进行故障排除。

预配问题的一些示例：
- VM 停滞在“正在创建”状态达 40 分钟，并且 VM 创建操作被标记为失败
- `CustomData` 未得到处理
- 临时磁盘装载失败
- 未创建用户，或存在用户访问问题
- 未正确设置网络
- 交换文件或分区故障

本文逐步讲解如何对 cloud-init 进行故障排除。 如需更深入的详细信息，请参阅[深入探讨 cloud-init](./cloud-init-deep-dive.md)。

## <a name="step-1-test-the-deployment-without-customdata"></a>步骤 1：在不使用 `customData` 的情况下测试部署

在创建 VM 时，Cloud-init 可以接受传递给它的 `customData`。 首先，你应确保这不会导致任何部署问题。 尽量在不传入任何配置的情况下预配 VM。 如果你发现 VM 无法预配，请继续执行下面的步骤；如果发现未应用你传递的配置，请转到[步骤 4]()。 

## <a name="step-2-review-image-requirements"></a>步骤 2：查看映像要求
VM 预配失败的主要原因是 OS 映像不满足在 Azure 上运行的先决条件。 尝试在 Azure 中预配映像之前，请确保已正确准备好映像。 


以下文章演示了准备 Azure 中支持的各种 Linux 发行版的步骤：

- [基于 CentOS 的分发版](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES 和 openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [其他：非认可的分发版](create-upload-generic.md)

对于[受支持的 Azure cloud-init 映像](./using-cloud-init.md)，Linux 发行版已准备好所有必需的包和配置，方便用户在 Azure 中正确预配映像。 如果发现 VM 未能从自己的特选映像创建，请尝试使用可选的已配置为进行云初始化的受支持的 Azure Marketplace 映像 `customData` 。 如果 `customData` 使用 Azure Marketplace 映像可以正常工作，则可能是特选映像出现问题。

## <a name="step-3-collect--review-vm-logs"></a>步骤 3：收集和查看 VM 日志

当 VM 无法预配时，Azure 会显示“正在创建”状态 20 分钟，然后重启 VM，再等待 20 分钟，最后才会将 VM 部署标记为失败，并使用 `OSProvisioningTimedOut` 错误来标记它。

你需要在 VM 处于运行状态的情况下使用其中的日志来了解预配为何失败。  若要了解 VM 预配为何失败，请不要停止 VM。 让 VM 保持运行状态。 为了收集日志，你需要使发生故障的 VM 保持运行状态。 若要收集日志，请使用以下方法之一：

- [串行控制台](../troubleshooting/serial-console-grub-single-user-mode.md)

- 在创建 VM 之前[启用启动诊断](./tutorial-monitor.md#enable-boot-diagnostics)，然后在启动过程中[查看](./tutorial-monitor.md#view-boot-diagnostics)它们。

- [运行 AZ VM Repair](../troubleshooting/repair-linux-vm-using-azure-virtual-machine-repair-commands.md) 来附加和装载 OS 磁盘，这将允许你收集以下日志：
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
若要开始进行初始故障排除，请从 cloud-init 日志着手，了解发生故障的位置，然后使用其他日志深入了解情况并获取更多见解。 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* Serial/boot logs

在所有日志中，开始搜索“Failed”、“WARNING”、“WARN”、“err”、“error”、“ERROR”。 建议将配置设置为忽略区分大小写的搜索。 

> [!TIP]
> 如果对自定义映像进行故障排除，应考虑在映像操作过程中添加用户。 如果预配无法设置管理员用户，你仍然可以登录到 OS。

## <a name="analyzing-the-logs"></a>分析日志

下面更详细地介绍了要在每个 cloud-init 日志中查找什么内容。

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

默认情况下，所有优先级为“调试”或更高级别的 cloud-init 事件都会写入到 `/var/log/cloud-init.log` 中。 这样就可以提供在 cloud-init 初始化期间发生的每个事件的详细日志。 

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


找到错误或警告后，请在 cloud-init 日志中倒退着阅读，了解在出现错误或警告之前 cloud-init 正在尝试执行的操作。 在许多情况下，在出现错误之前，cloud-init 运行了 OS 命令或执行了预配操作。你可以通过这些命令或操作了解日志中出现错误的原因。 以下示例表明，cloud-init 刚好在出现错误之前尝试了装入设备的操作。

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

如果有权访问 [串行控制台](../troubleshooting/serial-console-grub-single-user-mode.md)，可以尝试重新运行 cloud init 正在尝试运行的命令。

还可以在 /etc/cloud/cloud.cfg.d/05_logging.cfg 中重新配置 `/var/log/cloud-init.log` 的日志记录。 有关 cloud-init 日志记录的更多详细信息，请参阅 [cloud-init 文档](https://cloudinit.readthedocs.io/en/latest/topics/logging.html)。 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

可以在 [cloud-init 的各个阶段](cloud-init-deep-dive.md)获取 `stdout` 和 `stderr` 中的信息。 这通常涉及 cloud-init 的每个阶段的路由表信息、网络信息、ssh 主机密钥验证信息、`stdout` 和 `stderr`，以及每个阶段的时间戳。 如果需要，可以通过 `/etc/cloud/cloud.cfg.d/05_logging.cfg` 重新配置 `stderr` 和 `stdout` 日志记录。

### <a name="serialboot-logs"></a>Serial/boot logs 

Cloud-init 有多个依赖项，这些依赖项记录在 Azure 上的映像所需的先决条件（例如网络、存储、装载 ISO 以及装载和格式化临时磁盘的功能）中。 这其中的任何一项都可能会引发错误，导致 cloud-init 失败。 例如，如果 VM 无法获得 DHCP 租约，则 cloud-init 会失败。

如果仍无法厘清 cloud-init 未能预配的原因，则需了解 cloud-init 的具体阶段和模块的运行时间。 如需更多详细信息，请参阅[更深入地了解 cloud-init](cloud-init-deep-dive.md)。


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>步骤 4：调查未应用配置的原因
并非 cloud-init 中的每次失败都会导致严重的预配故障。 例如，如果在 cloud-init 配置中使用了 `runcmd` 模块，则运行命令时出现非零退出代码会导致 VM 预配失败。 这是因为它在核心预配功能（在 cloud-init 的前 3 个阶段中执行）后运行。 若要排查为何未应用配置，请手动查看步骤 3 中的日志和 cloud-init 模块。 例如：

- `runcmd` - 脚本在运行时是否未出错？ 请从终端手动运行配置，以确保它们按预期运行。
- 安装包 - VM 是否有权访问包存储库？
- 还应检查提供给 VM 的 `customData` 数据配置，该配置位于 `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` 中。


## <a name="next-steps"></a>后续步骤

如果仍然无法厘清 cloud-init 未运行配置的原因，则需更细致地了解每个 cloud-init 阶段发生的情况，以及运行模块的时间。 有关详细信息，请参阅[更深入地了解 cloud-init 配置](./cloud-init-deep-dive.md)。 
