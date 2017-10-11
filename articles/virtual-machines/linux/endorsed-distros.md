---
title: "Linux 的认可分发 |Microsoft 文档"
description: "了解 Linux Azure 认可的分发，包括 Ubuntu、 CentOS、 Oracle 和 SUSE 的指南。"
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 39cb2464eb593a29c4436afb5c14419b704ebff4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="linux-on-distributions-endorsed-by-azure"></a>在 Azure 认可的分发上的 Linux
合作伙伴提供 Azure 应用商店中的 Linux 映像。 我们正在与各个 Linux 社区合作，以向认可的分发列表中添加更多风格。 在此期间，从应用商店将不可用的分发，你可以始终会将你自己的 Linux 按照的准则[创建和上载包含 Linux 操作系统的虚拟硬盘](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。

## <a name="supported-distributions-and-versions"></a>受支持的分发和版本
下表列出的 Linux 分发和在 Azure 支持的版本。 请参阅[在 Microsoft Azure 中支持的 Linux 映像](https://support.microsoft.com/en-us/kb/2941892)更多详细信息。

Microsoft 直接影响上游 Linux 内核的内核模块的 HYPER-V 和 Azure 的 Linux Integration Services (LIS) 驱动程序。  默认情况下，某些 LIS 驱动程序内置于分发的内核。 旧基于 Red Hat Enterprise (RHEL) 上的分发 / CentOS 都可用作在单独的下载[hyper-v 的 Linux 集成服务版本 4.1](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)。 请参阅[Linux 内核要求](create-upload-generic.md#linux-kernel-requirements)有关 LIS 驱动程序的详细信息。

Azure Linux 代理已预安装在 Azure 应用商店映像，通常能从分发的包存储库。 可以在上找到源代码[GitHub](https://github.com/azure/walinuxagent)。

| 分发 | 版本 | 驱动程序 | 代理 |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3 + 7.0 + |CentOS 6.3: [LIS 下载](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 +: 在内核中 |包： 在[存储库](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/)"WALinuxAgent"下 <br/>源代码： [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |在内核中 |源代码： [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9 +、 8.2 + |在内核中 |包： 在"waagent"下的存储库 <br/>源代码： [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |在内核中 |包： 在"WALinuxAgent"下的存储库 <br/>源代码： [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7 + 7.1 + |在内核中 |包： 在"WALinuxAgent"下的存储库 <br/>源代码： [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |适用于 SAP 的 SLES/SLES<br>11 SP4<br>12 SP1 +|在内核中 |包：<p> 为 11 英寸[Cloud: Tools](https://build.opensuse.org/project/show/Cloud:Tools)存储库<br>为在"python azure 代理"下的"公共云"模块中包含的 12<br/>源代码： [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE 闰 42.1 + |在内核中 |包： 在[Cloud: Tools](https://build.opensuse.org/project/show/Cloud:Tools) "python azure 代理"下的存储库 <br/>源代码： [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04、 14.04、 16.04、 16.10 |在内核中 |包： 在"walinuxagent"下的存储库 <br/>源代码： [GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="partners"></a>合作伙伴

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

从 CoreOS 网站：

*CoreOS 旨在安全性、 一致性和可靠性。而不是安装包通过 yum 或 apt，CoreOS 使用 Linux 容器来管理你的抽象级别更高的服务。单个服务的代码和所有依赖项都打包在一个容器，它可以运行在一个或多个 CoreOS 计算机中。*

### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ 是一种独立咨询和服务公司，致力于开发和实现专业人员的解决方案使用可用的软件。 与主要的开放源代码专家 Credativ 具有国际认知与使用它们的支持的许多 IT 部门。 结合使用与 Microsoft Credativ 正在当前准备相应 Debian 映像适用于 Debian 8 (Jessie) 和 Debian 之前 7 (Wheezy)。 这两个映像专门设计为在 Azure 上运行，并可以轻松管理通过平台。 Credativ 还支持长期维护和更新 Debian 映像的适用于通过其打开源支持中心的 Azure。

### <a name="oracle"></a>Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle 的策略是提供广泛的： 公有云和私有云的解决方案。 策略为客户提供了选择和灵活性中如何在 Oracle 云中的 Oracle 软件和其他云中的部署。 与 Microsoft oracle 的合作关系使客户能够在 Microsoft 公有云和私有云，凭借可信的证书颁发部署 Oracle 软件和支持从 Oracle。  Oracle 的承诺和 Oracle 公有云和私有云解决方案中的投资保持不变。

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/strategic-alliance/microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

开放源解决方案的全球领先提供商，Red Hat 帮助超过 90%的财富 500 强企业解决的业务挑战，对齐他们的 IT 和业务策略并准备用于将来的技术。 Red Hat 实现这一点提供通过一个打开的业务模型和一个种经济、 可预测订阅模型的安全解决方案。

### <a name="suse"></a>SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

在 Azure 上的 SUSE Linux Enterprise Server 是一个经验证的平台，提供卓越的可靠性和安全性云计算。 SUSE 的通用 Linux 平台无缝集成与 Azure 云服务，以便交付易于管理的云环境中。 与从多个独立软件供应商提供适用于 SUSE Linux Enterprise Server 的 9,200 多个认证应用程序，SUSE 可确保在数据中心中运行受支持的工作负荷可以在 Azure 上自信地部署。

### <a name="canonical"></a>规范
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Canonical 工程和开放社区监管驱动器 Ubuntu 的成功在客户端、 服务器和云计算，其中包括使用者的个人云服务。 Canonical 期望使用的统一、 可用平台 Ubuntu 中,，从手机到云，为电话、 平板电脑、 TV 和桌面提供一的系列连贯的接口。 这一全新概念从而使 Ubuntu 成为各种机构公共云提供商到消费类电子产品制造商和各个技术专家们收藏的第一个选项。

与开发人员和世界各地的工程中心，Canonical 唯一定位要成为硬件制造商、 内容提供商和软件开发人员，以将 Ubuntu 解决方案推向市场的 Pc、 服务器和手持设备。
