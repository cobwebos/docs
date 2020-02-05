---
title: Azure VMware 解决方案（AVS）-使用 AVS 私有云作为本地工作负荷的灾难站点
description: 介绍如何将你的 AVS 私有云设置为本地 VMware 工作负荷的灾难恢复站点
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1f34c4fb89d28a001a4af4d21879403a9ac5e860
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024698"
---
# <a name="set-up-avs-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>设置 AVS 私有云作为本地 VMware 工作负荷的灾难恢复站点

你的 AVS 私有云可设置为本地应用程序的恢复站点，以便在发生灾难时提供业务连续性。 恢复解决方案基于 Zerto 虚拟复制，作为复制和协调平台。 关键基础结构和应用程序虚拟机可以从本地 vCenter 持续复制到 AVS 私有云。 你可以使用 AVS 私有云进行故障转移测试，并确保在发生灾难期间应用程序的可用性。 可以遵循类似的方法，将 AVS 私有云设置为在不同位置受恢复站点保护的主站点。

> [!NOTE]
> 有关调整灾难恢复环境大小的指导原则，请参阅[Zerto 虚拟复制](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf)的 Zerto 文档大小注意事项。

AVS 解决方案：

* 无需设置专门用于灾难恢复（DR）的数据中心。
* 允许你利用为全球地理恢复部署 AVS 的 Azure 位置。
* 提供一个选项，用于降低部署成本和灾难恢复的总拥有成本。

解决方案要求：

* 在你的 AVS 私有云中安装、配置和管理 Zerto。
* 当 AVS 私有云为受保护的站点时，提供自己的 Zerto 许可证。 你可以将在 AVS 站点上运行的 Zerto 与本地站点配对，以获取许可。

下图显示了 Zerto 解决方案的体系结构。

![体系结构](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解决方案

以下部分介绍如何使用 AVS 私有云中的 Zerto 虚拟复制部署灾难恢复解决方案。

1. [先决条件](#prerequisites)
2. [你的 AVS 私有云上的可选配置](#optional-configuration-on-your-avs-private-cloud)
3. [在 AVS 私有云上设置 ZVM 和 VRA](#set-up-zvm-and-vra-on-your-avs-private-cloud)
4. [设置 Zerto 虚拟保护组](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>必备组件

若要启用从本地环境到 AVS 私有云的 Zerto 虚拟复制，请完成以下先决条件。

1. [设置本地网络与 AVS 私有云之间的站点到站点 VPN 连接](set-up-vpn.md)。
2. [设置 DNS 查找，以便将你的 Avs 私有云管理组件转发到 Avs 私有云 DNS 服务器](on-premises-dns-setup.md)。 若要启用 DNS 查找转发，请在本地 DNS 服务器中创建一个转发区域条目，以便 `*.AVS.io` 到 AVS DNS 服务器。
3. 设置 DNS 查找，以便将本地 vCenter 组件转发到本地 DNS 服务器。 DNS 服务器必须可通过站点到站点 VPN 通过 AVS 私有云访问。 若要获得帮助，请提交[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，并提供以下信息。 

    * 本地 DNS 域名
    * 本地 DNS 服务器 IP 地址

4. 在 AVS 私有云上安装 Windows server。 服务器用于安装 Zerto 虚拟管理器。
5. [升级你的 AVS 权限](escalate-private-cloud-privileges.md)。
6. 使用管理角色在你的 AVS 私有云 vCenter 上创建一个新用户，该用户使用作为 Zerto Virtual Manager 的服务帐户。

### <a name="optional-configuration-on-your-avs-private-cloud"></a>你的 AVS 私有云上的可选配置

1. 在你的 AVS 私有云 vCenter 上创建一个或多个资源池，以用作本地环境中 Vm 的目标资源池。
2. 在你的 AVS 私有云 vCenter 上创建一个或多个文件夹，用作本地环境中 Vm 的目标文件夹。
3. 为故障转移网络创建 Vlan 并设置防火墙规则。 打开[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)以获得帮助。
4. 为故障转移网络和测试网络创建分布式端口组，以便测试 Vm 的故障转移。
5. 安装[DHCP 和 DNS 服务器，](dns-dhcp-setup.md)或使用 AVS 私有云环境中的 Active Directory 域控制器。

### <a name="set-up-zvm-and-vra-on-your-avs-private-cloud"></a>在 AVS 私有云上设置 ZVM 和 VRA

1. 在 AVS 私有云中的 Windows server 上安装 Zerto Virtual Manager （ZVM）。
2. 使用在前面的步骤中创建的服务帐户登录到 ZVM。
3. 设置 Zerto Virtual Manager 的授权。
4. 在 AVS 私有云的 ESXi 主机上安装 Zerto 虚拟复制设备（VRA）。
5. 将你的 AVS 私有云 ZVM 与本地 ZVM 配对。

### <a name="set-up-zerto-virtual-protection-group"></a>设置 Zerto 虚拟保护组

1. 创建新的虚拟保护组（VPG），并指定 VPG 的优先级。
2. 选择需要保护以实现业务连续性的虚拟机，并根据需要自定义启动顺序。
3. 选择恢复站点作为你的 AVS 私有云和默认恢复服务器作为 AVS 私有云群集或你创建的资源组。 对于 AVS 私有云上的恢复数据存储，请选择**vsanDatastore** 。

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > 可以在 "VM 设置" 选项下为单个 Vm 自定义主机选项。

4. 根据需要自定义存储选项。
5. 指定要用于故障转移网络和故障转移测试网络的恢复网络作为之前创建的分布式端口组，并根据需要自定义恢复脚本。
6. 根据需要自定义各个 Vm 的网络设置，并创建 VPG。
7. 完成复制后，测试故障转移。

## <a name="reference"></a>参考

[Zerto 文档](https://www.zerto.com/myzerto/technical-documentation/)
