---
title: Azure 中对 Linux VM 的云 init 支持概述
description: 在 Azure 中的预配时间配置 VM 的云 init 功能概述。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465033"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>对 Azure 中的虚拟机的云 init 支持
本文介绍了在 Azure 中使用 [cloud-init](https://cloudinit.readthedocs.io) 在预配时间配置虚拟机 (VM) 或虚拟机规模集的现有支持。 Azure 预配资源后，这些云 init 配置将首先启动。  

VM 预配是 Azure 将传递 VM Create 参数值（如主机名、用户名、密码等）的过程，并在启动时将其提供给 VM。 "预配代理"将使用这些值、配置 VM 并在完成后报告。 

Azure 支持两个预配代理[云 init](https://cloudinit.readthedocs.io)和[Azure Linux 代理 （WALA）。](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)

## <a name="cloud-init-overview"></a>云 init 概述
[云init](https://cloudinit.readthedocs.io)是一种广泛使用的方法，用于自定义Linux VM，因为它第一次启动。 可使用 cloud-init 安装程序包和写入文件，或者配置用户和安全。 由于是在初始启动过程中调用 cloud-init，因此无需额外的步骤且无需代理来应用配置。  有关如何正确设置`#cloud-config`文件或其他输入的格式的详细信息，请参阅[云 init 文档网站](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)。  `#cloud-config` 文件是采用 base64 编码的文本文件。

云 init 还跨发行版工作。 例如，不要使用 apt-get 安装**** 或 yum 安装**** 来安装包。 可定义要安装的程序包的列表。 云 init 会自动使用本机包管理工具进行您选择的发行版。

我们正在积极地与我们认可的 Linux 发行版合作伙伴合作，以便在 Azure 市场中提供已启用 cloud-init 的映像。 这些映像可使 cloud-init 部署和配置无缝地应用于 VM 和虚拟机规模集。 最初，我们与认可的 Linux 分型合作伙伴和上游协作，以确保在 Azure 上的操作系统中使用云 init 函数，然后更新包并在分众包存储库中公开提供。 

在 Azure 上向支持的 Linux 发行版操作系统提供云 init 分为两个阶段，包括包支持，然后是映像支持：
* 支持或预览中的云 init 包在 Azure 文档上的"云 init 包支持"，因此可以在自定义映像中将这些包与操作系统一起使用。
* 如果映像已配置为使用云 init，则"映像云 init 就绪"文档。


### <a name="canonical"></a>Canonical
| 发布者/版本| 产品/服务 | SKU | 版本 | 图像云 init 就绪 | Azure 上的云 init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|规范 18.04 |UbuntuServer |18.04-LTS |最新 |是 | 是 |
|规范 16.04|UbuntuServer |16.04-LTS |最新 |是 | 是 |
|规范 14.04|UbuntuServer |14.04.5-LTS |最新 |是 | 是 |

### <a name="rhel"></a>RHEL
| 发布者/版本 | 产品/服务 | SKU | 版本 | 图像云 init 就绪 | Azure 上的云 init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |是 | 是 - 来自包版本的支持 *：18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | 是的（请注意，这是一个预览图像，一旦所有 RHEL 7.7 图像都支持云 init，这将在 2020 年年中删除，将发出通知） | 是 - 包版本支持： *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-RAW | 不适用| 否 - 2020 年 4 月底完成的图像更新| 是 - 包版本支持： *18.5-3.el7*|
|RedHat 7.7 |RHEL |7-LVM | 不适用| 否 - 图像更新，于 4 月底完成| 是 - 包版本支持： *18.5-3.el7*|
|RedHat 7.7 |RHEL |7.7 | 不适用| 否 - 图像更新，于 4 月底完成 | 是 - 包版本支持： *18.5-3.el7*|
|RedHat 7.7 |雷尔-比奥斯 | rhel-lvm77 | 不适用|否 - 图像更新，于 4 月底完成  | 是 - 包版本支持： *18.5-3.el7*|

### <a name="centos"></a>CentOS

| 发布者/版本 | 产品/服务 | SKU | 版本 | 图像云 init 就绪 | Azure 上的云 init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |是的（请注意，这是一个预览图像，一旦所有 CentOS 7.7 图像都支持云 init，这将在 2020 年年中删除，将发出通知） | 是 - 从包版本支持： *18.5-3.el7.centos*|

* 2020 年 3 月将在此处更新将启用云的 CentOS 7.7 图像 

### <a name="oracle"></a>Oracle

| 发布者/版本 | 产品/服务 | SKU | 版本 | 图像云 init 就绪 | Azure 上的云 init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|甲骨文 7.7 |Oracle-Linux |77-ci |7.7.01| 预览图像（请注意，这是预览图像，一旦所有 Oracle 7.7 图像都支持云 init，这将在 2020 年年中删除，将发出通知） | 否，在预览中，包是： *18.5-3.0.1.el7*

### <a name="debian--suse-sles"></a>debian & 苏塞 SLES
我们目前正在努力预览支持，预计在 2020 年 2 月和 3 月更新。

目前，Azure 堆栈将支持预配启用云的映像。


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init 和 Linux 代理 (WALA) 之间的区别是什么？
WALA 是特定于 Azure 的平台代理，用于预配和配置 VM 以及处理[Azure 扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。 

我们正在加强将 VM 配置为使用云 init 而不是 Linux 代理的任务，以便允许现有云 init 客户使用其当前的云 init 脚本，或者新客户利用丰富的云 init 配置功能。 如果您对用于配置 Linux 系统的云 init 脚本进行了现有投资，则**无需其他设置**即可启用云 init 处理它们。 

云 init 无法处理 Azure 扩展，因此映像中仍需要 WALA 来处理扩展，但需要禁用其预配代码，对于被云 init 转换为预配的支持的 Linux 发行版映像，它们将具有 WALA安装并正确设置。

创建 VM 时，如果在预配时未包括 Azure `--custom-data` CLI 交换机，则云 init 或 WALA 会采用预配 VM 和完成默认部署所需的最少 VM 预配参数。  如果使用`--custom-data`交换机引用云 init 配置，则自定义数据中包含的任何内容都将在 VM 启动时可供云 init 使用。

应用于 VM 的云 init 配置没有时间限制，也不会通过超时导致部署失败。WALA 的情况并非如此，如果将 WALA 默认值更改为处理自定义数据，则不能超过 40 分钟的总 VM 预配时间余量，如果是，VM Create 将失败。

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>部署已启用 cloud-init 的虚拟机
部署已启用 cloud-init 的虚拟机就和在部署期间引用已启用 cloud-init 的分发一样简单。  Linux 分发 Maintainer 需要选择启用 cloud-init，并将 cloud-init 集成到其基本 Azure 已发布映像中。 确认想要部署的映像已启用 cloud-init 之后，就可以使用 AzureCLI 部署映像。 

部署此映像的第一步是使用 [az group create](/cli/azure/group) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

下面的示例在*东部*位置创建名为*myResourceGroup*的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
下一步是在当前 shell 中创建名为 cloud-init.txt ** 的文件并粘贴以下配置。 对于此示例，请在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 输入 `sensible-editor cloud-init.txt` 以创建文件并查看可用编辑器的列表。 选择 #1 以使用 nano 编辑器****。 请确保已正确复制整个 cloud-init 文件，尤其是第一行：

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
按 `ctrl-X` 退出该文件，键入 `y` 以保存文件，并按 `enter` 确认退出时的文件名。

最后一步是使用 [az vm create](/cli/azure/vm) 命令创建 VM。 

以下示例创建一个名为 centos74** 的 VM，并且在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  使用 `--custom-data` 参数传递到 cloud-init 配置文件中。 如果未将 cloud-init.txt** 配置文件保存在现有工作目录中，请提供该文件的完整路径。 以下示例创建一个名为 centos74** 的 VM：

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
 
