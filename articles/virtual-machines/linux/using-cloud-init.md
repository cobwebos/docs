---
title: Azure 中 Linux Vm 的云初始化支持概述
description: 在 Azure 中预配时配置 VM 的云初始化功能概述。
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
ms.openlocfilehash: e3a09a0d8412af711bfb6c539dc9d2829b1f0898
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964577"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Azure 中虚拟机的云初始化支持
本文介绍了在 Azure 中设置时，[云初始化](https://cloudinit.readthedocs.io)用于配置虚拟机（VM）或虚拟机规模集的支持。 一旦 Azure 预配了资源，这些云初始化配置就会在首次启动时运行。  

VM 预配是指 Azure 会向下传递 VM 创建参数值（例如主机名、用户名、密码等）的过程，并在启动时将其提供给 VM。 "预配代理" 将使用这些值，配置 VM，并在完成后返回报告。 

Azure 支持两种预配代理：[云初始化](https://cloudinit.readthedocs.io)和[Azure LINUX 代理（WALA）](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)。

## <a name="cloud-init-overview"></a>云初始化概述
[云初始化](https://cloudinit.readthedocs.io)是一种广泛使用的方法，用于在第一次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 安装程序包和写入文件，或者配置用户和安全。 由于是在初始启动过程中调用 cloud-init，因此无需额外的步骤且无需代理来应用配置。  有关如何正确设置 `#cloud-config` 文件或其他输入的格式的详细信息，请参阅[云初始化文档网站](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)。  `#cloud-config` 文件是采用 base64 编码的文本文件。

云初始化也可跨分布工作。 例如，不要使用 apt-get 安装或 yum 安装来安装包。 可定义要安装的程序包的列表。 cloud init 自动为你选择的发行版使用本机包管理工具。

我们正在积极地与我们认可的 Linux 发行版合作伙伴合作，以便在 Azure 市场中提供已启用 cloud-init 的映像。 这些映像可使 cloud-init 部署和配置无缝地应用于 VM 和虚拟机规模集。 最初，我们与认可的 Linux 发行版合作伙伴和上游协作，以确保 Azure 上的操作系统具有云初始化功能，然后更新包，并使其在发行版包存储库中公开提供。 

提供两个阶段，使云初始化可用于 Azure 上认可的 Linux 发行版操作系统、包支持和映像支持：
* "Azure 上的云 init 包支持" 记录了支持或以预览版提供的云 init 包，因此你可以在自定义映像中将这些包与 OS 一起使用。
* 如果映像已配置为使用云初始化，则为 "映像云初始化就绪" 文档。


### <a name="canonical"></a>Canonical
| 发布者/版本| 产品 | SKU | 版本 | 映像云初始化就绪 | Azure 上的云 init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|规范18.04 |UbuntuServer |18.04-LTS |最新 |是 | 是 |
|规范16.04|UbuntuServer |16.04-LTS |最新 |是 | 是 |
|规范14.04|UbuntuServer |14.04.5-LTS |最新 |是 | 是 |

### <a name="rhel"></a>RHEL
| 发布者/版本 | 产品 | SKU | 版本 | 映像云初始化就绪 | Azure 上的云 init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |是 | 是-包版本支持： *18.2-el7_6。 2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | 是（请注意，这是预览图像，一旦所有 RHEL 7.7 映像都支持云初始化，这将会被2020删除，但会向你提供通知） | 是-包版本支持： *18.5-3. el7*|
|RedHat 7.7 |RHEL |7-RAW | 不适用| 无-在2020年2月开始更新映像| 是-包版本支持： *18.5-3. el7*|
|RedHat 7.7 |RHEL |7-LVM | 不适用| 无-在2020年2月开始更新映像| 是-包版本支持： *18.5-3. el7*|
|RedHat 7.7 |RHEL |7.7 | 不适用| 无-在2020年2月开始更新映像 | 是-包版本支持： *18.5-3. el7*|
|RedHat 7.7 |rhel-byos | rhel-lvm77 | 不适用|无-在2020年2月开始更新映像  | 是-包版本支持： *18.5-3. el7*|

### <a name="centos"></a>CentOS

| 发布者/版本 | 产品 | SKU | 版本 | 映像云初始化就绪 | Azure 上的云 init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7。7 |CentOS |7-CI |7.7.20190920 |是（请注意，这是一个预览图像，一旦所有 CentOS 7.7 映像都支持云初始化，这将会被2020删除，但会向你提供通知） | 是-包版本支持： *18.5-3. el7. centos*|

* 将在2020年2月更新启用云初始化的 CentOS 7.7 映像 

### <a name="oracle"></a>Oracle

| 发布者/版本 | 产品 | SKU | 版本 | 映像云初始化就绪 | Azure 上的云 init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7。7 |Oracle-Linux |77-ci |7.7.01| 预览图像（请注意，这是预览图像，在所有 Oracle 7.7 映像都支持云初始化后，这将会被2020删除，但会提供通知） | 不，在预览版中，package 为： *18.5-el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
我们目前正在使用预览版支持，预计在二月份和3月2020更新。

目前 Azure Stack 将支持预配启用了云初始化的映像。


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init 和 Linux 代理 (WALA) 之间的区别是什么？
WALA 是特定于 Azure 平台的代理，用于预配和配置 Vm，并处理[azure 扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。 

我们正在增强将 Vm 配置为使用云初始化而不是 Linux 代理的任务，以允许现有的云初始化客户使用其当前的云初始化脚本，或新客户利用丰富的云初始化配置功能。 如果你在云初始化脚本中有用于配置 Linux 系统的现有投资，则**无需其他设置**即可启用云初始化过程。 

cloud init 无法处理 Azure 扩展，因此，在处理扩展的映像中仍需要 WALA，但需要禁用其预配代码，对于被云初始化转换为预配的已认可 Linux 发行版映像，这些映像将具有 WALA已正确安装和安装。

创建 VM 时，如果在预配时未包括 Azure CLI `--custom-data` 交换机，cloud init 或 WALA 将采用预配 VM 所需的最小 VM 预配参数，并使用默认值完成部署。  如果使用 `--custom-data` 交换机引用云初始化配置，则当 VM 启动时，自定义数据中包含的内容将可用于 cloud init。

应用于 Vm 的云初始化配置不具有时间限制，并且不会因超时而导致部署失败。对于 WALA，这一点并不适用，如果你更改 WALA 默认值以处理自定义数据，则它不能超过40mins 的总 VM 预配时间限制，如果是，则 VM 创建将失败。

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>部署已启用 cloud-init 的虚拟机
部署已启用 cloud-init 的虚拟机就和在部署期间引用已启用 cloud-init 的分发一样简单。  Linux 分发 Maintainer 需要选择启用 cloud-init，并将 cloud-init 集成到其基本 Azure 已发布映像中。 确认想要部署的映像已启用 cloud-init 之后，就可以使用 AzureCLI 部署映像。 

部署此映像的第一步是使用 [az group create](/cli/azure/group) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
下一步是在当前 shell 中创建名为 cloud-init.txt的文件并粘贴以下配置。 对于此示例，请在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 输入 `sensible-editor cloud-init.txt` 以创建文件并查看可用编辑器的列表。 选择 #1 以使用 nano 编辑器。 请确保已正确复制整个 cloud-init 文件，尤其是第一行：

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
 
