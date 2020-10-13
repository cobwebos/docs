---
title: 了解 cloud-init
description: 深入了解如何使用 cloud-init 来预配 Azure VM。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: f5028abadbe5600058c83a144d0095aee1278fe6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86042063"
---
# <a name="diving-deeper-into-cloud-init"></a>深入了解 cloud-init
若要详细了解 [cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) 或在更深级别对其进行故障排除，你需要了解其工作原理。 本文档重点介绍了重要的部分，并介绍了 Azure 具体内容。

当通用映像中包含 cloud-init 并且基于该映像创建了 VM 时，它会在初始启动过程中处理配置并完成 5 个阶段。 这些阶段很重要，因为它会显示 cloud-init 将在何时应用配置。 


## <a name="understand-cloud-init-configuration"></a>了解 Cloud-Init 配置
将 VM 配置为在平台上运行意味着 cloud-init 需要应用多个配置。作为映像使用者，你将与之交互的主要配置是 `User data` (customData)，它支持多种格式，这些内容记录在[此处](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats)。 你还可以添加并运行脚本 (/var/lib/cloud/scripts) 来进行其他配置。下面更详细地讨论了这一点。

某些配置已经内置到附带了 cloud-init 的 Azure 市场映像中，例如：

1. **云数据源** - cloud-init 包含可与云平台（称为“数据源”）进行交互的代码。 从 [azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure)中的云初始化映像创建 VM 时，云初始化会加载 azure 数据源，该数据源将与 azure 元数据终结点交互，以获取特定于 VM 的配置。
2. **运行时配置** (/run/cloud-init)
3. **映像配置** (/etc/cloud)，如 `/etc/cloud/cloud.cfg`、`/etc/cloud/cloud.cfg.d/*.cfg`。 例如，在 Azure 中使用此功能的情况下，具有 cloud-init 的 Linux OS 映像通常可以发出一个 Azure 数据源指令，告诉 cloud-init 应该使用哪个数据源，这将节省 cloud-init 时间：

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>cloud-init 启动阶段（处理配置）

在通过 cloud-init 进行预配时，会经历 5 个用于处理配置的启动阶段，这些阶段会显示在日志中。

1. [生成器阶段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator)：cloud-init systemd 生成器在启动后会确定是否应该将 cloud-init 包括在启动目标中。如果应该，它会启用 cloud-init。 

2. [Cloud-init 本地阶段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local)：在此阶段中，cloud-init 将查找本地“Azure”数据源，该数据源将使 cloud-init 能够与 Azure 进行连接，并应用网络配置（包括回退）。

3. [Cloud-init 初始化阶段（网络）](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network)：应该联网并生成 NIC 和路由表信息。 此阶段将运行 /etc/cloud/cloud.cfg 中的 `cloud_init_modules` 列出的模块。 需装入 Azure 中的 VM，对临时磁盘进行格式化，设置主机名并执行其他任务。

   下面是一些 `cloud_init_modules`：
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   在此阶段之后，cloud-init 会向 Azure 平台发出信号，指示 VM 已成功预配。 某些模块可能已失败，并非所有模块故障都会导致预配失败。

4. [Cloud-init 配置阶段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config)：此阶段会运行在 /etc/cloud/cloud.cfg 中定义并列出的 `cloud_config_modules` 中的模块。


5. [Cloud-init 最终阶段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final)：此阶段会运行 /etc/cloud/cloud.cfg 中列出的 `cloud_final_modules` 中的模块。 这些是在引导过程运行期间后期需要运行的模块，例如包安装和运行脚本等。 

   -   在此阶段，可以通过将脚本放在 `/var/lib/cloud/scripts` 下的目录中来运行脚本：
   - `per-boot` - 每次重启时都会运行此目录中的脚本
   - `per-instance` - 首次启动新实例时会运行此目录中的脚本
   - `per-once` - 此目录中的脚本仅运行一次

## <a name="next-steps"></a>后续步骤

[对 cloud-init 进行故障排除](cloud-init-troubleshooting.md)。
