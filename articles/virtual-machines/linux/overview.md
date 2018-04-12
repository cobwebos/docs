---
title: Azure 中 Linux VM 的概述 | Microsoft Docs
description: 介绍 Linux 虚拟机上的 Azure 计算、存储和网络服务。
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: jeconnoc
editor: ''
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: d2e4a014a0f7bb7f94885528abc930d2b243318b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="azure-and-linux"></a>Azure 和 Linux
Microsoft Azure 正在不断集结各种集成的公有云服务，包括分析、虚拟机、数据库、移动、网络、存储和 Web&mdash;是托管解决方案的理想之选。  Microsoft Azure 提供可缩放的计算平台，允许即用即付，而无需投资购买本地硬件。  Azure 允许根据客户端所需的任何规模，随时扩展和缩减解决方案。

如果熟悉 Amazon AWS 各项功能的话，可以查看 Azure 与 AWS 的[定义映射文档](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)。

## <a name="regions"></a>区域
Microsoft Azure 资源分布在世界各地的多个地理区域。  一个“区域”代表位于单个地理区域的多个数据中心。 Azure 目前（截至 2017 年 11 月）已在全球 36 个区域正式推出，另外已宣布即将在另外 6 个区域推出。 可在以下页面上找到现有区域和新宣布推出区域的更新列表：

* [Azure 区域](https://azure.microsoft.com/regions/)

## <a name="availability"></a>可用性
Azure 宣布了行业领先的单实例虚拟机服务级别协议：可用性达到 99.9%（前提是为所有磁盘使用高级存储部署 VM）。  为了使部署符合标准 99.95% 的 VM 服务级别协议，仍需要在可用性集中部署两个或更多个运行工作负荷的 VM。 可用性集可确保 VM 分布在 Azure 数据中心内的多个容错域，并使用不同的维护时段部署到主机。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 说明了 Azure 作为整体的保证可用性。

## <a name="managed-disks"></a>托管磁盘

托管磁盘为用户在后台处理 Azure 存储帐户的创建和管理，确保用户无需担心存储帐户的可伸缩性限制。 只需指定磁盘大小和性能层（标准或高级），Azure 就会自动创建和管理磁盘。 在添加磁盘或者扩展和缩减 VM 时，无需考虑所用的存储。 如果要创建新的 VM，请[使用 Azure CLI 2.0](quick-create-cli.md) 或 Azure 门户，通过托管 OS 和数据磁盘创建 VM。 如果 VM 具有非托管磁盘，则可以[将 VM 转换为由托管磁盘支持](convert-unmanaged-to-managed-disks.md)。

用户还可以按 Azure 区域在一个存储帐户中管理自定义映像，并使用这些映像在同一订阅中创建数百台 VM。 有关托管磁盘的详细信息，请参阅[托管磁盘概述](../linux/managed-disks-overview.md)。

## <a name="azure-virtual-machines--instances"></a>Azure 虚拟机和实例
Microsoft Azure 支持运行由多家合作伙伴提供和维护的众多热门 Linux 分发版。  可以在 Azure Marketplace 中找到 Red Hat Enterprise、CentOS、SUSE Linux Enterprise、Debian、Ubuntu、CoreOS、RancherOS 和 FreeBSD 等分发版。 Microsoft 积极与各大 Linux 社区合作以便为 [Azure 认可的 Linux 分发版](endorsed-distros.md)列表添加更多成员。

如果首选的 Linux 分发版目前不在库中，可以通过[在 Azure 中创建和上传 Linux VHD](create-upload-generic.md) 来“自带 Linux”VM。

借助 Azure 虚拟机，用户可以采用灵活的方式部署各种计算解决方案。 几乎可以在任何操作系统（Windows、Linux 或从我们不断增长的合作伙伴列表中的任一合作伙伴自定义创建的操作系统）上部署几乎任何工作负荷和任何语言。 没有找到所需的映像？  别担心，也可以使用本地的自有映像。

## <a name="vm-sizes"></a>VM 大小
VM 的[大小](sizes.md)由所要运行的工作负荷决定。 然后，所选大小又会影响多个因素，例如处理能力、内存和存储容量。 Azure 提供各种大小来支持多种类型的用途。

Azure 根据 VM 的大小和操作系统按[小时价格](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)计费。 对于不足一小时的部分，Azure 仅根据使用的分钟数计费。 存储将另行定价和收费。

## <a name="automation"></a>自动化
若要实现适当的 DevOps 区域性，所有基础结构都必须是代码。  当所有基础结构都存在于代码中时，便可以轻松重新创建它（Phoenix 服务器）。  Azure 可与所有主要自动化工具（如 Ansible、Chef、SaltStack 和 Puppet）配合使用。  Azure 也有自己的自动化工具：

* [Azure 模板](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](using-vmaccess-extension.md)

Azure 正在支持它的大多数 Linux 发行版中推出 [cloud-init](http://cloud-init.io/) 支持。  目前，默认情况下 Canonical Ubuntu VM 在启用 cloud-init 的情况下进行部署。  Red Hat RHEL、CentOS 和 Fedora 支持 cloud-init，但 RedHat 维护的 Azure 映像目前未安装 cloud-init。  若要在 RedHat 系列 OS 上使用 cloud-init，必须创建安装了 cloud-init 的自定义映像。

* [在 Azure Linux VM 上使用 cloud-init](using-cloud-init.md)

## <a name="quotas"></a>配额
每个 Azure 订阅都有默认的配额限制，此限制会在为项目部署大量 VM 时造成影响。 每个订阅的当前限制是每区域 20 个 VM。  若要快速轻松地提高配额限制，可以开具支持票证来请求提高限制。  有关配额限制的更多详细信息，请参阅：

* [Azure 订阅服务限制](../../azure-subscription-service-limits.md)

## <a name="partners"></a>合作伙伴
Microsoft 与合作伙伴紧密合作，以确保可用映像进行更新并针对 Azure 运行时进行优化。  有关 Azure 合作伙伴的详细信息，请参阅以下链接：

* Azure 上的 Linux - [认可的分发](endorsed-distros.md)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=%27SUSE%27)
* Redhat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
* CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Azure 上的 Mesosphere DC/OS](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure Marketplace - 具有 Docker Swarm 的 Azure 容器服务](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)

## <a name="getting-started-with-linux-on-azure"></a>Azure 上的 Linux 入门
若要开始使用 Azure，需要 Azure 帐户、已安装 Azure CLI 和一对 SSH 公钥和私钥。

### <a name="sign-up-for-an-account"></a>注册帐户
使用 Azure 云的第一步是注册 Azure 帐户。  若要开始，请转到 [Azure 帐户注册](https://azure.microsoft.com/pricing/free-trial/)页。

### <a name="install-the-cli"></a>安装 CLI
使用新的 Azure 帐户，可以立即开始使用 Azure 门户（一个基于 Web 的管理面板）。  若要通过命令行管理 Azure 云，请安装 `azure-cli`。  在 Mac 或 Linux 工作站上安装 [Azure CLI 2.0](/cli/azure/install-azure-cli)。

### <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对
现在已有 Azure 帐户、Azure Web 门户和 Azure CLI。  下一步是创建 SSH 密钥对，使用它可以通过 SSH 连接到 Linux 而无需使用密码。  [在 Linux 和 Mac 上创建 SSH 密钥](mac-create-ssh-keys.md)可启用无密码登录和更高的安全性。

### <a name="create-a-vm-using-the-cli"></a>使用 CLI 创建 VM
使用 CLI 创建 Linux VM 是部署 VM 的一种快速方法，无需离开正在使用的终端。  通过命令行标志或开关提供可以在 Web 门户上指定的所有内容。  

* [使用 CLI 创建 Linux VM](quick-create-cli.md)

### <a name="create-a-vm-in-the-portal"></a>在门户中创建 VM
通过在 Azure Web 门户上创建 Linux VM，可以轻松地指向和单击用于访问部署的各个选项。  不是使用命令行标记或开关，而是能够查看各种选项和设置的良好 Web 布局。  通过命令行接口提供的所有功能也都在门户中提供。

* [使用门户创建 Linux VM](quick-create-portal.md)

### <a name="log-in-using-ssh-without-a-password"></a>不使用密码通过 SSH 登录
VM 现在正在 Azure 上运行，用户可以登录。  使用密码通过 SSH 登录既不安全耗时也长。  使用 SSH 密钥是最安全且最快捷的登录方式。  通过门户或 CLI 创建 Linux VM 时，有两种身份验证选择。  如果为 SSH 选择密码，则 Azure 将 VM 配置为允许通过密码登录。  如果选择使用 SSH 公钥，则 Azure 将 VM 配置为只允许通过 SSH 密钥登录，并禁止密码登录。 若要通过只允许 SSH 密钥登录来保护 Linux VM，请在门户或 CLI 中创建 VM 的过程中使用 SSH 公钥选项。

## <a name="related-azure-components"></a>相关 Azure 组件
## <a name="storage"></a>存储
* [Microsoft Azure 存储空间简介](../../storage/common/storage-introduction.md)
* [使用 azure-cli 将磁盘添加到 Linux VM](add-disk.md)
* [如何在 Azure 门户中将数据磁盘附加到 Linux VM](attach-disk-portal.md)

## <a name="networking"></a>网络
* [虚拟网络概述](../../virtual-network/virtual-networks-overview.md)
* [Azure 中的 IP 地址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [在 Azure 中打开 Linux VM 的端口](nsg-quickstart.md)
* [在 Azure 门户中创建完全限定的域名](portal-create-fqdn.md)

## <a name="containers"></a>容器
* [Azure 中的虚拟机和容器](containers.md)
* [Azure 容器服务简介](../../container-service/container-service-intro.md)
* [部署 Azure 容器服务群集](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>后续步骤
现在已概要了解 Azure 上的 Linux。  下一步是进一步的研究，并创建一些 VM 组件！

* [通过 Azure CLI 浏览不断增多的常见任务的示例脚本列表](cli-samples.md)
