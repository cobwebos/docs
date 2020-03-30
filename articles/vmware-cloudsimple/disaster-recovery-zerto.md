---
title: Azure VMware 解决方案（按云简单 - 使用私有云作为本地工作负载的灾难站点）
description: 描述如何将云简单私有云设置为本地 VMware 工作负载的灾难恢复站点
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e019a9229b671be2fb73e758bd39f33657bc2d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083133"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>将云简单私有云设置为本地 VMware 工作负载的灾难恢复站点

云简单私有云可以设置为本地应用程序的恢复站点，在发生灾难时提供业务连续性。 恢复解决方案基于 Zerto 虚拟复制作为复制和业务流程平台。 关键基础设施和应用程序虚拟机可以从本地 vCenter 连续复制到私有云。 您可以使用私有云进行故障转移测试，并确保应用程序在灾难期间的可用性。 也可以采用类似方法将私有云设置为主站点，该站点受其他位置的恢复站点保护。

> [!NOTE]
> 有关调整灾难恢复环境大小的指南，请参阅[Zerto 文档"Zerto 虚拟复制大小调整注意事项](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf)"。

云简单解决方案：

* 无需设置专门用于灾难恢复 （DR） 的数据中心。
* 允许您利用部署 CloudSimple 的 Azure 位置，实现全球地理恢复能力。
* 为您提供了降低 DR 的部署成本和总拥有成本的选项。

该解决方案要求您：

* 在私有云中安装、配置和管理 Zerto。
* 当私有云是受保护的站点时，为 Zerto 提供您自己的许可证。 您可以将在 CloudSimple 网站上运行的 Zerto 与本地站点配对以进行许可。

下图显示了 Zerto 解决方案的体系结构。

![体系结构](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解决方案

以下各节介绍如何在私有云中使用 Zerto 虚拟复制部署 DR 解决方案。

1. [先决条件](#prerequisites)
2. [云简单私有云上的可选配置](#optional-configuration-on-your-private-cloud)
3. [在云简单私有云上设置 ZVM 和 VRA](#set-up-zvm-and-vra-on-your-private-cloud)
4. [设置泽托虚拟保护组](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>先决条件

要启用从本地环境到私有云的 Zerto 虚拟复制，请完成以下先决条件。

1. [在本地网络和云简单私有云之间建立站点到站点 VPN 连接](set-up-vpn.md)。
2. [设置 DNS 查找，以便将私有云管理组件转发到私有云 DNS 服务器](on-premises-dns-setup.md)。  要启用 DNS 查找的转发，请在本地 DNS 服务器`*.cloudsimple.io`中创建用于 CloudSimple DNS 服务器的转发区域条目。
3. 设置 DNS 查找，以便将本地 vCenter 组件转发到本地 DNS 服务器。  DNS 服务器必须通过站点到站点 VPN 从云简单私有云访问。 为寻求帮助，请提交[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，并提供以下信息。  

    * 本地 DNS 域名
    * 本地 DNS 服务器 IP 地址

4. 在私有云上安装 Windows 服务器。 服务器用于安装 Zerto 虚拟管理器。
5. [升级云简单权限](escalate-private-cloud-privileges.md)。
6. 在私有云 vCenter 上创建一个新用户，其管理角色用作 Zerto 虚拟管理器的服务帐户。

### <a name="optional-configuration-on-your-private-cloud"></a>私有云上的可选配置

1. 在私有云 vCenter 上创建一个或多个资源池，用作本地环境中 VM 的目标资源池。
2. 在私有云 vCenter 上创建一个或多个文件夹，用作本地环境中 VM 的目标文件夹。
3. 为故障转移网络创建 VLAN 并设置防火墙规则。 打开[支持](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)请求以寻求帮助。
4. 为故障转移网络和测试网络创建分布式端口组，以测试 VM 的故障转移。
5. 在私有云环境中安装[DHCP 和 DNS 服务器](dns-dhcp-setup.md)或使用活动目录域控制器。

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>在私有云上设置 ZVM 和 VRA

1. 在私有云中的 Windows 服务器上安装 Zerto 虚拟管理器 （ZVM）。
2. 使用在前面的步骤中创建的服务帐户登录到 ZVM。
3. 为 Zerto 虚拟管理器设置许可。
4. 在私有云的 ESXi 主机上安装 Zerto 虚拟复制设备 （VRA）。
5. 将私有云 ZVM 与本地 ZVM 配对。

### <a name="set-up-zerto-virtual-protection-group"></a>设置泽托虚拟保护组

1. 创建新的虚拟保护组 （VPG） 并指定 VPG 的优先级。
2. 选择需要保护业务连续性的虚拟机，并根据需要自定义启动顺序。
3. 选择恢复站点作为私有云，将默认恢复服务器作为私有云群集或您创建的资源组。 为私有云上的恢复数据存储选择**vsan DataStore。**

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > 您可以在"VM 设置"选项下为各个 VM 自定义主机选项。

4. 根据需要自定义存储选项。
5. 指定用于故障转移网络和故障转移测试网络的恢复网络作为较早创建的分布式端口组，并根据需要自定义恢复脚本。
6. 如有必要，自定义各个 VM 的网络设置并创建 VPG。
7. 复制完成后测试故障转移。

## <a name="reference"></a>参考

[泽托文档](https://www.zerto.com/myzerto/technical-documentation/)
