---
title: Azure 中 Linux Vm 的云初始化支持概述
description: 在 Azure 中预配时用于配置 VM 的 cloud-init 功能概述。
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 01/23/2019
ms.author: danis
ms.openlocfilehash: 1f0395956fa6977be5d1d6f4f4faf06b84c094d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79465033"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Azure 中虚拟机的 cloud-init 支持
本文介绍了在 Azure 中使用 [cloud-init](https://cloudinit.readthedocs.io) 在预配时间配置虚拟机 (VM) 或虚拟机规模集的现有支持。 Azure 预配资源后，这些 cloud-init 配置即会在第一次启动时运行。  

VM 预配是指 Azure 向下传递“VM 创建”参数值（例如主机名、用户名、密码等），并在 VM 启动时向其提供这些值的过程。 “预配代理”将使用这些值，配置 VM，并在完成后返回报告。 

Azure 支持两个预配代理：[cloud-init](https://cloudinit.readthedocs.io) 和 [Azure Linux 代理 (WALA)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)。

## <a name="cloud-init-overview"></a>cloud-init 概述
[cloud-init](https://cloudinit.readthedocs.io) 是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 来安装程序包和写入文件，或者配置用户和安全性。 由于是在初始启动过程中调用 cloud-init，因此无需额外的步骤且无需代理来应用配置。  有关如何正确设置 `#cloud-config` 文件或其他输入的格式的详细信息，请参阅 [cloud-init 文档站点](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)。  `#cloud-config` 文件是采用 base64 编码的文本文件。

cloud-init 还支持不同的分发版。 例如，不需使用  apt-get install 或  yum install 来安装包， 而是可定义要安装的程序包的列表。 cloud-init 将对所选发行版自动使用本机包管理工具。

我们正在积极地与我们认可的 Linux 发行版合作伙伴合作，以便在 Azure 市场中提供已启用 cloud-init 的映像。 这些映像将使 cloud-init 部署和配置无缝地应用于 VM 和虚拟机规模集。 最初，我们与经认可的 Linux 发行版合作伙伴和上游协作，以确保 Azure 上的操作系统具有云初始化功能，然后更新包并将其公开在发行版包存储库中。 

需要经历两个阶段才能使 cloud-init 可供 Azure 上认可的 Linux 分发版 OS 使用：包支持和映像支持：
* “Azure 上的 cloud-init 包支持”阐述了哪些 cloud-init 包即将受支持或以预览版提供，因此可以配合自定义映像中的 OS 使用这些包。
* “映像 cloud-init 就绪”阐述了相应映像是否已配置为使用 cloud-init。


### <a name="canonical"></a>Canonical
| 发布者/版本| 产品/服务 | SKU | 版本 | 映像 cloud-init 就绪 | Azure 上的 cloud-init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 18.04 |UbuntuServer |18.04-LTS |最新 |是 | 是 |
|Canonical 16.04|UbuntuServer |16.04-LTS |最新 |是 | 是 |
|Canonical 14.04|UbuntuServer |14.04.5-LTS |最新 |是 | 是 |

### <a name="rhel"></a>RHEL
| 发布者/版本 | 产品/服务 | SKU | 版本 | 映像 cloud-init 就绪 | Azure 上的 cloud-init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |是 | 是-包版本支持： *18.2-el7_6。 2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | 是（请注意，这是预览图像，一旦所有 RHEL 7.7 映像都支持云初始化，这将会被2020删除，但会向你提供通知） | 是-包版本支持： *18.5-3. el7*|
|RedHat 7.7 |RHEL |7-RAW | n/a| 2020年4月结束的无图像更新| 是-包版本支持： *18.5-3. el7*|
|RedHat 7.7 |RHEL |7-LVM | n/a| 不需要完成的任何图像更新| 是-包版本支持： *18.5-3. el7*|
|RedHat 7.7 |RHEL |7.7 | n/a| 不需要完成的任何图像更新 | 是-包版本支持： *18.5-3. el7*|
|RedHat 7.7 |rhel-byos | rhel-lvm77 | n/a|不需要完成的任何图像更新  | 是-包版本支持： *18.5-3. el7*|

### <a name="centos"></a>CentOS

| 发布者/版本 | 产品/服务 | SKU | 版本 | 映像云初始化就绪 | Azure 上的云 init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |是（请注意，这是一个预览图像，一旦所有 CentOS 7.7 映像都支持云初始化，这将会被2020删除，但会向你提供通知） | 是-包版本支持： *18.5-3. el7. centos*|

* 7.7 2020 年3月将更新启用了云初始化的 CentOS 映像 

### <a name="oracle"></a>Oracle

| 发布者/版本 | 产品/服务 | SKU | 版本 | 映像云初始化就绪 | Azure 上的云 init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7。7 |Oracle-Linux |77-ci |7.7.01| 预览图像（请注意，这是预览图像，在所有 Oracle 7.7 映像都支持云初始化后，这将会被2020删除，但会提供通知） | 不，在预览版中，package 为： *18.5-el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
我们目前正在使用预览版支持，预计在二月份和3月2020更新。

目前 Azure Stack 将支持预配启用了云初始化的映像。


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init 和 Linux 代理 (WALA) 之间的区别是什么？
WALA 是特定于 Azure 平台的代理，用于预配和配置 Vm，并处理[azure 扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。 

我们正在增强将 Vm 配置为使用云初始化而不是 Linux 代理的任务，以允许现有的云初始化客户使用其当前的云初始化脚本，或新客户利用丰富的云初始化配置功能。 如果你在云初始化脚本中有用于配置 Linux 系统的现有投资，则**无需其他设置**即可启用云初始化过程。 

cloud init 无法处理 Azure 扩展，因此，在处理扩展的映像中仍需要 WALA，但需要禁用其预配代码，对于已被云初始化转换为预配的已认可 Linux 发行版映像，这些映像将安装 WALA 并正确设置。

创建 VM 时，如果在预配时未包括 Azure CLI `--custom-data`交换机，云初始化或 WALA 将采用预配 vm 所需的最小 VM 预配参数，并使用默认值完成部署。  如果使用`--custom-data`交换机引用云初始化配置，则当 VM 启动时，自定义数据中包含的内容将可用于云初始化。

应用于 Vm 的云初始化配置不具有时间限制，并且不会因超时而导致部署失败。对于 WALA，这一点并不适用，如果你更改 WALA 默认值以处理自定义数据，则它不能超过40mins 的总 VM 预配时间限制，如果是，则 VM 创建将失败。

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>部署已启用 cloud-init 的虚拟机
部署已启用 cloud-init 的虚拟机就和在部署期间引用已启用 cloud-init 的分发一样简单。  Linux 分发 Maintainer 需要选择启用 cloud-init，并将 cloud-init 集成到其基本 Azure 已发布映像中。 确认想要部署的映像已启用 cloud-init 之后，就可以使用 AzureCLI 部署映像。 

部署此映像的第一步是使用 [az group create](/cli/azure/group) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”  位置创建名为“myResourceGroup”  的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
下一步是在当前 shell 中创建名为 cloud-init.txt ** 的文件并粘贴以下配置。 对于此示例，请在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 输入 `sensible-editor cloud-init.txt` 以创建文件并查看可用编辑器的列表。 选择 #1 以使用 nano 编辑器  。 请确保已正确复制整个 cloud-init 文件，尤其是第一行：

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
按 `ctrl-X` 退出该文件，键入 `y` 以保存文件，并按 `enter` 确认退出时的文件名。

最后一步是使用 [az vm create](/cli/azure/vm) 命令创建 VM。 

以下示例创建一个名为 centos74** 的 VM，并且在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  使用 `--custom-data` 参数传入 cloud-init 配置文件。 如果未将 cloud-init.txt  配置文件保存在现有工作目录中，请提供该文件的完整路径。 以下示例创建一个名为 centos74** 的 VM：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

创建 VM 后，Azure CLI 会显示部署的特定信息。 记下 `publicIpAddress`。 此地址用于访问 VM。  创建 VM、安装程序包和启动应用需要一些时间。 在 Azure CLI 返回提示之后，仍然存在继续运行的后台任务。 你可以使用 SSH 连接到 VM 并使用故障排除部分中所述的步骤来查看 cloud-init 日志。 

## <a name="troubleshooting-cloud-init"></a>对 cloud-init 进行故障排除
VM 预配完成后，会在 `--custom-data` 中定义的所有模块和脚本上运行 cloud-init，以便配置 VM。  若要对配置中存在的任何错误或遗漏进行故障排除，需要在位于 /var/log/cloud-init.log 的 cloud-init 日志中搜索模块名称（例如 `disk_setup` 或 `runcmd`）****。

> [!NOTE]
> 并不是每个模块故障都会导致严重的 cloud-init 整体配置故障。 例如使用 `runcmd` 模块，如果脚本发生故障，cloud-init 依然会报告预配成功，因为 runcmd 模块已执行。

有关 cloud-init 日志的更多详细信息，请参阅 [cloud-init 文档](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>后续步骤
有关配置更改的 cloud-init 示例，请参阅以下文档：
 
- [向 VM 添加其他 Linux 用户](cloudinit-add-user.md)
- [运行包管理器以在首次启动时更新现有包](cloudinit-update-vm.md)
- [更改 VM 本地主机名](cloudinit-update-vm-hostname.md) 
- [安装应用程序包、更新配置文件和注入密钥](tutorial-automate-vm-deployment.md)
 
