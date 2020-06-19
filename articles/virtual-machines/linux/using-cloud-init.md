---
title: Azure 中 Linux VM 的 cloud-init 支持概述
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
ms.date: 05/19/2019
ms.author: danis
ms.openlocfilehash: 9e42229b08d7817b64c66c4ab23877c837339475
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83827312"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Azure 中虚拟机的 cloud-init 支持
本文介绍在 Azure 中预配时用于配置虚拟机 (VM) 或虚拟机规模集的 [cloud-init](https://cloudinit.readthedocs.io) 的现有支持。 Azure 预配资源后，这些 cloud-init 配置即会在首次启动时运行。  

VM 预配是指 Azure 向下传递“VM 创建”参数值（例如主机名、用户名、密码等），并在 VM 启动时向其提供这些值的过程。 “预配代理”将使用这些值，配置 VM，并在完成后返回报告。 

Azure 支持两个预配代理：[cloud-init](https://cloudinit.readthedocs.io) 和 [Azure Linux 代理 (WALA)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)。

## <a name="cloud-init-overview"></a>cloud-init 概述
[cloud-init](https://cloudinit.readthedocs.io) 是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 安装程序包和写入文件，或者配置用户和安全。 由于是在初始启动过程中调用 cloud-init，因此无需额外的步骤且无需代理来应用配置。  有关如何正确设置 `#cloud-config` 文件或其他输入的格式的详细信息，请参阅 [cloud-init 文档站点](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)。  `#cloud-config` 文件是采用 base64 编码的文本文件。

cloud-init 还支持不同的发行版。 例如，不要使用 apt-get 安装或 yum 安装来安装包。 可定义要安装的程序包的列表。 cloud-init 将对你选择的发行版自动使用本机包管理工具。

我们正在积极地与我们认可的 Linux 发行版合作伙伴合作，以便在 Azure 市场中提供已启用 cloud-init 的映像。 这些映像可使 cloud-init 部署和配置无缝地应用于 VM 和虚拟机规模集。 最初，我们与认可的 Linux 发行版合作伙伴和上游协作，确保 cloud-init 可以配合 Azure 上的 OS 正常运行，然后更新了包，并在发行版包的存储库中公开提供了这些包。 

若要使 cloud-init 可供 Azure 上认可的 Linux 发行版 OS 使用，需要经历两个阶段 - 包支持，然后是映像支持：
* “Azure 上的 cloud-init 包支持”阐述了哪些 cloud-init 包即将受支持或以预览版提供，因此你可以将这些包与自定义映像中的 OS 配合使用。
* “映像 cloud-init 准备就绪”阐述了映像是否已配置为使用 cloud-init。


### <a name="canonical"></a>Canonical
| 发布者/版本| 产品/服务 | SKU | 版本 | 映像 cloud-init 准备就绪 | Azure 上的 cloud-init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 20.04 |UbuntuServer |18.04-LTS |最新 |是 | 是 |
|Canonical 18.04 |UbuntuServer |18.04-LTS |最新 |是 | 是 |
|Canonical 16.04|UbuntuServer |16.04-LTS |最新 |是 | 是 |
|Canonical 14.04|UbuntuServer |14.04.5-LTS |最新 |是 | 是 |

### <a name="rhel"></a>RHEL
| 发布者/版本 | 产品/服务 | SKU | 版本 | 映像 cloud-init 准备就绪 | Azure 上的 cloud-init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |是 | 是 - 通过包版本支持：*18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | 是（请注意，这是一个预览版映像，一旦所有 RHEL 7.7 映像都支持 cloud-init，就会在 2020 年 9 月 1 日删除此映像） | 是 - 通过包版本支持：*18.5-6.el7*|
|RedHat 7.7 (Gen1)|RHEL |7.7 | 7.7.2020051912 | 否 - 映像更新正在进行，要在 5 月底完成 | 是 - 通过包版本支持：*18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 77-gen2 | 7.7.2020051913 | 否 - 映像更新正在进行，要在 5 月底完成 | 是 - 通过包版本支持：*18.5-6.el7*|
|RedHat 7.7 (Gen1)|RHEL |7-LVM | 7.7.2020051921 | 否 - 映像更新正在进行，要在 5 月底完成 | 是 - 通过包版本支持：*18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 7lvm-gen2 | 7.7.2020051922  | 否 - 映像更新正在进行，要在 5 月底完成 | 是 - 通过包版本支持：*18.5-6.el7*|
|RedHat 7.7 (Gen1) |rhel-byos | rhel-lvm77 | 7.7.20200416 | 否 - 映像更新正在进行，要在 5 月底完成  | 是 - 通过包版本支持：*18.5-6.el7*|
|RedHat 8.1 (Gen1) |RHEL |8.1-ci |8.1.2020042511 | 是（请注意，这是一个预览版映像，一旦所有 RHEL 8.1 映像都支持 cloud-init，就会在 2020 年 8 月 1 日删除此映像） | 否，完整支持的 ETA 为 2020 年 6 月|
|RedHat 8.1 (Gen2) |RHEL |81-ci-gen2 |8.1.2020042524 | 是（请注意，这是一个预览版映像，一旦所有 RHEL 8.1 映像都支持 cloud-init，就会在 2020 年 8 月 1 日删除此映像） | 否，完整支持的 ETA 为 2020 年 6 月 |

RedHat:RHEL 7.8 和 8.2（Gen1 和 Gen2）映像是使用 cloud-init 预配的。

### <a name="centos"></a>CentOS

| 发布者/版本 | 产品/服务 | SKU | 版本 | 映像 cloud-init 准备就绪 | Azure 上的 cloud-init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |是（请注意，这是一个预览版映像，一旦所有 CentOS 7.7 映像都支持 cloud-init，就会在 2020 年 9 月 1 日删除此映像） | 是 - 通过包版本支持：*18.5-3.el7.centos*|

* 本文将在 2020 年 6 月更新启用 cloud-init 的 CentOS 7.7 映像 
* CentOS 7.8 映像是使用 cloud-init 预配的。


### <a name="oracle"></a>Oracle

| 发布者/版本 | 产品/服务 | SKU | 版本 | 映像 cloud-init 准备就绪 | Azure 上的 cloud-init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7.7 |Oracle-Linux |77-ci |7.7.01| 预览版映像（请注意，这是一个预览版映像，一旦所有 Oracle 7.7 映像都支持 cloud-init，就会在 2020 年中期删除此映像，届时会发出通知） | 否，在预览版中，包为：*18.5-3.0.1.el7*

### <a name="suse-sles"></a>SuSE SLES
| 发布者/版本 | 产品/服务 | SKU | 版本 | 映像 cloud-init 准备就绪 | Azure 上的 cloud-init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|SUSE SLES 15 SP1 |suse |sles-15-sp1-basic |cloud-init-preview| 有关详细信息，请参阅 [SUSE cloud-init 博客](https://suse.com/c/clout-init-coming-to-suse-images-in-azure/) | 否，以预览版提供。 |
|SUSE SLES 15 SP1 |suse |sles-15-sp1-basic |gen2-cloud-init-preview| 有关详细信息，请参阅 [SUSE cloud-init 博客](https://suse.com/c/clout-init-coming-to-suse-images-in-azure/) | 否，以预览版提供。 |


### <a name="debian"></a>Debian
我们目前正在努力提供预览版支持，有望在 2020 年 6 月提供最新信息。

目前 Azure Stack 将支持预配启用了 cloud-init 的映像。


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init 和 Linux 代理 (WALA) 之间的区别是什么？
WALA 是一种特定于 Azure 平台的代理，用于预配和配置 VM 并处理 [Azure 扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。 

我们正在增强将 VM 配置为使用 cloud-init 而不是 Linux 代理的任务，使现有的 cloud-init 客户能够使用其当前 cloud-init 脚本，或者使新客户能够利用丰富的 cloud-init 配置功能。 如果已使用过 cloud-init 脚本来配置 Linux 系统，那么，**不需要进行额外的设置**就能让 cloud-init 处理这些任务。 

cloud-init 无法处理 Azure 扩展，因此，仍需在映像中包含 WALA 才能处理扩展，不过，需要禁用其预配代码。要转换为由 cloud-init 预配的已认可 Linux 发行版映像将会正确安装并设置 WALA。

创建 VM 时，如果在预配时未包括 Azure CLI `--custom-data` 开关，cloud-init 或 WALA 将采用所需的最小 VM 预配参数来预配 VM 并使用默认值完成部署。  如果使用 `--custom-data` 开关引用 cloud-init 配置，在 VM 启动时，自定义数据中包含的任何内容均可用于 cloud-init。

应用于 VM 的 cloud-init 配置没有时限，也不会因为超时导致部署失败。对于 WALA，这一点并不适用。如果更改 WALA 默认值来处理自定义数据，则用于处理自定义数据的时间不能超过 VM 预配总时间限制（40 分钟），否则 VM 创建操作将会失败。

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>部署已启用 cloud-init 的虚拟机
部署已启用 cloud-init 的虚拟机就和在部署期间引用已启用 cloud-init 的分发一样简单。  Linux 分发 Maintainer 需要选择启用 cloud-init，并将 cloud-init 集成到其基本 Azure 已发布映像中。 确认想要部署的映像已启用 cloud-init 之后，就可以使用 AzureCLI 部署映像。 

部署此映像的第一步是使用 [az group create](/cli/azure/group) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
下一步是在当前 shell 中创建名为 cloud-init.txt 的文件并粘贴以下配置。 对于此示例，请在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 输入 `sensible-editor cloud-init.txt` 以创建文件并查看可用编辑器的列表。 选择 #1 以使用 nano 编辑器。 请确保已正确复制整个 cloud-init 文件，尤其是第一行：

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
按 `ctrl-X` 退出该文件，键入 `y` 以保存文件，并按 `enter` 确认退出时的文件名。

最后一步是使用 [az vm create](/cli/azure/vm) 命令创建 VM。 

以下示例创建一个名为 centos74 的 VM，并且在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  使用 `--custom-data` 参数传递到 cloud-init 配置文件中。 如果未将 cloud-init.txt 配置文件保存在现有工作目录中，请提供该文件的完整路径。 以下示例创建一个名为 centos74 的 VM：

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

创建 VM 后，Azure CLI 会显示部署的特定信息。 记下 `publicIpAddress`。 此地址用于访问 VM。  创建 VM、安装程序包和启动应用需要一些时间。 在 Azure CLI 向你返回提示之后，仍然存在继续运行的后台任务。 你可以使用 SSH 连接到 VM 并使用故障排除部分中所述的步骤来查看 cloud-init 日志。 

## <a name="troubleshooting-cloud-init"></a>对 cloud-init 进行故障排除
VM 预配完成后，会在 `--custom-data` 中定义的所有模块和脚本上运行 cloud-init，以便配置 VM。  若要对配置中存在的任何错误或遗漏进行故障排除，需要在位于 /var/log/cloud-init.log 的 cloud-init 日志中搜索模块名称（例如 `disk_setup` 或 `runcmd`）。

> [!NOTE]
> 并不是每个模块故障都会导致严重的 cloud-init 整体配置故障。 例如使用 `runcmd` 模块，如果脚本发生故障，cloud-init 依然会报告预配成功，因为 runcmd 模块已执行。

有关 cloud-init 日志的更多详细信息，请参阅 [cloud-init 文档](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>后续步骤
有关配置更改的 cloud-init 示例，请参阅以下文档：
 
- [向 VM 添加其他 Linux 用户](cloudinit-add-user.md)
- [运行包管理器以在首次启动时更新现有包](cloudinit-update-vm.md)
- [更改 VM 本地主机名](cloudinit-update-vm-hostname.md) 
- [安装应用程序包、更新配置文件和注入密钥](tutorial-automate-vm-deployment.md)
 
