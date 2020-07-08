---
title: 了解云初始化
description: 深入了解如何使用云初始化来预配 Azure VM。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: f5028abadbe5600058c83a144d0095aee1278fe6
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042063"
---
# <a name="diving-deeper-into-cloud-init"></a>深入了解云初始化
若要详细了解[云初始化](https://cloudinit.readthedocs.io/en/latest/index.html)或对其进行故障排除，需要了解其工作原理。 本文档重点介绍了重要的部分，并介绍了 Azure 具体内容。

当在通用化映像中包含 cloud init 并且从该映像创建 VM 时，它将处理配置并在初始启动过程中通过5个阶段运行。 这些阶段非常重要，因为它会向你显示云初始化将应用配置的位置。 


## <a name="understand-cloud-init-configuration"></a>了解云初始化配置
将 VM 配置为在平台上运行，这意味着云初始化需要应用多个配置，作为映像使用者，你将与之交互的主要配置为 `User data` （customData）（支持多种格式），[此处](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats)对此进行了介绍。 你还可以添加和运行脚本（/var/lib/cloud/scripts）以进行其他配置，下面更详细地讨论了这一点。

部分配置已融入在云初始化附带的 Azure Marketplace 映像中，例如：

1. **云数据源**-cloud-init 包含可与云平台进行交互的代码，这些代码称为 "数据源"。 从[azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure)中的云初始化映像创建 VM 时，云初始化会加载 azure 数据源，该数据源将与 azure 元数据终结点交互，以获取特定于 VM 的配置。
2. **运行时配置**（/run/cloud-init）
3. **映像配置**（/etc/cloud），如 `/etc/cloud/cloud.cfg` ， `/etc/cloud/cloud.cfg.d/*.cfg` 。 例如，在 Azure 中使用此功能的情况下，使用 cloud init 的 Linux OS 映像可以使用 Azure datasource 指令，这会告诉云初始化应该使用的数据源，这将节省云初始化时间：

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>云初始化启动阶段（处理配置）

在通过云初始化进行预配时，有5个启动阶段，用于处理配置，并显示在日志中。

1. [生成器阶段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator)： cloud init systemd 生成器将启动，确定是否应将其包含在启动目标中，如果是，它将启用云初始化。 

2. [云初始化本地阶段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local)：在此处，cloud init 将查找本地 "Azure" 数据源，该数据源将启用与 azure 的连接，并应用网络配置（包括回退）。

3. [云初始化初始化阶段（网络）](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network)：网络应该联机，并且应生成 NIC 和路由表信息。 在此阶段， `cloud_init_modules` 将运行/etc/cloud/cloud.cfg 中列出的模块。 将装入 Azure 中的 VM，对临时磁盘进行格式化，同时设置主机名和其他任务。

   下面是其中的一些 `cloud_init_modules` ：
   
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
   
   在此阶段之后，cloud init 会向 Azure 平台发出信号，指示 VM 已成功预配。 某些模块可能已失败，并非所有模块故障都将导致预配失败。

4. [云初始化配置阶段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config)：在此阶段， `cloud_config_modules` 将运行在/etc/cloud/cloud.cfg 中定义和列出的模块。


5. [云初始化最终阶段](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final)：在此最后阶段， `cloud_final_modules` 将运行/etc/cloud/cloud.cfg 中列出的模块。 此处需要在启动过程运行时延迟运行的模块，例如包安装和运行脚本等。 

   -   在此阶段中，可以通过将脚本放在下面的目录中来运行脚本 `/var/lib/cloud/scripts` ：
   - `per-boot`-该目录中的脚本，每次重新启动时运行
   - `per-instance`-当首次启动新实例时，此目录中的脚本运行
   - `per-once`-此目录内的脚本仅运行一次

## <a name="next-steps"></a>后续步骤

[排查云初始化问题](cloud-init-troubleshooting.md)。
