---
title: Linux 预配概述
description: 概述如何将 Linux VM 映像引入到 Azure 或新建映像以在 Azure 中使用。
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6530d05b8e1aa565e64256054e81b785572edfb0
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307049"
---
# <a name="azure-linux-vm-provisioning"></a>Azure Linux VM 预配
使用通用映像（共享映像库或托管映像）创建 VM 时，通过控制平面可以创建 VM 并将参数和设置传递到 VM。 此过程称为 VM 预配。 在预配过程中，该平台会在 VM 启动时将所需的 VM Create 参数值（主机名、用户名、密码、SSH 密钥 和 customData）提供给 VM。 

映像内烘焙的预配代理与该平台建立连接（连接多个独立的预配接口），设置属性并向该平台发送成功完成的信号。 

预配代理可以是 [Azure Linux 代理](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)或 [cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)。 这是创建通用映像的[先决条件](create-upload-generic.md)。

预配代理支持所有认可的 [Azure Linux 发行版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。很多情况下，这些认可的发行版映像附带于 cloud-init 和 Linux 代理中。 这使你可以选择让 cloud-init 处理预配，让 Linux 支持 [Azure 扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/features-windows)处理。 对扩展的预配支持意味着 VM 将有资格支持其他 Azure 服务，例如 VM 密码重置、Azure Monitor、Azure 备份和 Azure 磁盘加密。

预配完成后，每次启动时 cloud-init 都会运行。 cloud-init 会监视 VM 的更改，例如网络变化、临时磁盘格的装载和格式化以及 Linux 代理的启动。 Linux 代理持续在服务器上运行，从 Azure 平台中查找“目标状态”（新配置），以便无论何时安装扩展，Linux 代理都能够处理扩展。

虽然目前有两个预配代理可供选择，但应选择 cloud-init 作为预配代理，选择安装 Linux 代理来提供扩展支持。 通过这种方式，你可以利用平台优化和禁用/删除 Linux 代理。有关如何在不使用该代理的情况下创建映像以及如何删除代理，请参阅此[文档](disable-provisioning.md)。

如果你的 Linux 内核不支持运行这两种代理，但你需要设置主机名、customData、用户名、密码和 SSH 密钥等 VM Create 属性，可通过本文档了解如何[创建通用映像但不使用代理](no-agent.md)以及如何满足平台要求。


## <a name="provisioning-agent-responsibilities"></a>预配代理的责任

**映像预配**
  
- 创建用户帐户
- 配置 SSH 身份验证类型
- 部署 SSH 公钥和密钥对
- 设置主机名
- 将主机名发布到平台 DNS
- 将 SSH 主机密钥指纹报告给平台
- 资源磁盘管理
- 格式化并安装资源磁盘
- 使用和处理 `customData`
 
**网络**
  
- 管理路由以提高与平台 DHCP 服务器的兼容性
- 确保网络接口名称的稳定性

**内核**
  
- 配置虚拟 NUMA（对版本低于 <`2.6.37` 的内核禁用）
- 将 Hyper-V 熵用于 `/dev/random`
- 为根设备配置 SCSI 超时（可能通过远程方式）

**诊断**
  
- 控制台重定向到串行端口

## <a name="communication"></a>通信
从平台到代理的信息流通过两个通道进行：

- 用于 IaaS 部署的附加了启动时间的 DVD。 此 DVD 包含一个与 OVF 兼容的配置文件，该文件包括除 SSH 密钥对之外的所有配置信息。
- 用于获取部署和拓扑配置的一个公开 REST API 的 TCP 终结点。


## <a name="azure-provisioning-agent-requirements"></a>Azure 预配代理要求
Linux 代理和 cloud-init 的正常运行依赖一些系统程序包：
- Python 2.6+
- OpenSSL 1.0+
- OpenSSH 5.3+
- 文件系统实用工具：`sfdisk`、`fdisk`、`mkfs` 和 `parted`
- 密码工具：chpasswd、sudo
- 文本处理工具：sed、grep
- 网络工具：ip-route
- 装载 UDF 文件系统的内核支持。

## <a name="next-steps"></a>后续步骤

如果需要，可以[禁用预配并删除 Linux 代理](disable-provisioning.md)。
