---
title: 高性能计算 - Azure 虚拟机 |微软文档
description: 了解 Azure 上的高性能计算。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707824"
---
# <a name="optimization-for-linux"></a>针对 Linux 进行优化

本文展示了优化操作系统映像的一些关键技术。 了解有关启用[InfiniBand](enable-infiniband.md)和优化操作系统映像的更多详细信息。

## <a name="update-lis"></a>更新 LIS

如果使用自定义映像进行部署（例如，较旧的操作系统（如 CentOS/RHEL 7.4 或 7.5），则更新 VM 上的 LIS。

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>回收内存

通过自动回收内存来提高效率，以避免远程内存访问。

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

要使这种情况在 VM 重新启动后持续：

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>禁用防火墙和 SELinux

禁用防火墙和 SELinux。

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>禁用 cpu 电源

禁用 cpu 电源。

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>后续步骤

* 了解有关启用[InfiniBand](enable-infiniband.md)和优化操作系统映像的更多详细信息。

* 在 Azure 上了解有关[HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)的更多详细信息。
