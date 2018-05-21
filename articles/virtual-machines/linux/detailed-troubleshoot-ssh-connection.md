---
title: Azure VM 的详细 SSH 故障排除 | Microsoft Docs
description: 对 Azure 虚拟机连接问题进行较详细 SSH 故障排除的步骤
keywords: ssh 连接被拒绝,ssh 错误,azure ssh,SSH 连接失败
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 88f3ca3202359f9f45f5b9a5054ab95b40558520
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>对连接到 Azure 中 Linux VM 时出现的问题进行 SSH 故障排除的具体步骤
有许多可能的原因会导致 SSH 客户端无法连接到 VM 上的 SSH 服务。 如果已经执行了较[常规的 SSH 故障排除步骤](troubleshoot-ssh-connection.md)，则需要进一步排查连接问题。 本文将指导用户完成详细的故障排除步骤，以确定 SSH 连接失败的位置及其解决方法。

## <a name="take-preliminary-steps"></a>采取预备步骤
下图显示了与错误相关的组件。

![显示 SSH 服务组件的图表](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

以下步骤将帮助用户查明失败的原因，并得出解决方法或应对措施。

1. 在门户中检查 VM 的状态。
   在 [Azure 门户](https://portal.azure.com)中，选择“虚拟机” > “VM 名称”。

   VM 的状态窗格应显示“正在运行”。 向下滚动以显示计算、存储和网络资源的最近活动。

2. 选择“设置”检查终结点、IP 地址、网络安全组和其他设置。

   VM 必须有为 SSH 流量定义的终结点，可以在“终结点”或“[网络安全组](../../virtual-network/security-overview.md)”查看 SSH 流量。 将 VM 中使用 Resource Manager 创建的终结点存储在网络安全组中。 请验证对网络安全组应用的规则，以及子网中是否引用了这些规则。

要验证网络连接，请检查所配置的终结点，并了解是否可通过其他协议（例如 HTTP 或其他服务）连接到该 VM。

在执行这些步骤之后，重新尝试 SSH 连接。

## <a name="find-the-source-of-the-issue"></a>查找问题的来源
如果计算机上的 SSH 客户端无法连接到 Azure VM 上的 SSH 服务，则原因可能是以下区域存在问题或配置错误：

* [SSH 客户端计算机](#source-1-ssh-client-computer)
* [组织边缘设备](#source-2-organization-edge-device)
* [云服务终结点和访问控制列表 (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [网络安全组](#source-4-network-security-groups)
* [基于 Linux 的 Azure VM](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>来源 1：SSH 客户端计算机
要将计算机从失败原因中排除，请验证计算机是否能够与其他基于 Linux 的本地计算机建立 SSH 连接。

![突出显示 SSH 客户端计算机组件的图表](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

如果连接失败，请检查计算机上是否存在以下问题：

* 本地防火墙设置阻止了入站或出站 SSH 流量 (TCP 22)
* 本地安装的客户端代理软件阻止了 SSH 连接
* 本地安装的网络监视软件阻止了 SSH 连接
* 监视流量或允许/禁止特定类型流量的其他类型的安全软件

如果存在其中一种情况，请暂时禁用相关软件，并尝试与本地计算机建立 SSH 连接，以找出计算机上阻止连接的原因。 然后，与网络管理员合作以更正软件设置，从而允许 SSH 连接。

如果使用的是证书身份验证，则需验证你是否具有这些权限以访问主目录中的 .ssh 文件夹：

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa（或存储私钥的其他任何文件）
* Chmod 644 ~/.ssh/known_hosts（包含已通过 SSH 连接到的主机）

## <a name="source-2-organization-edge-device"></a>来源 2：组织边缘设备
要将组织边缘设备从失败原因中排除，请验证直接连接到 Internet 的计算机是否可以与 Azure VM 建立 SSH 连接。 如果是通过站点到站点 VPN 或 Azure ExpressRoute 连接来访问 VM，请跳转到[来源 4：网络安全组](#nsg)。

![突出显示组织边缘设备的图表](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

如果没有直接连接到 Internet 的计算机，可以在其自己的资源组或云服务中创建新的 Azure VM，并使用该 VM。 有关详细信息，请参阅[在 Azure 中创建运行 Linux 的虚拟机](quick-create-cli.md)。 测试完成后，请删除资源组或 VM 以及云服务。

如果可以创建与直接连接到 Internet 的计算机之间的 SSH 连接，则检查组织边缘设备中是否存在以下问题：

* 内部防火墙阻止了与 Internet 的 SSH 连接
* 代理服务器阻止了 SSH 连接
* 边界网络中的设备上运行的入侵检测或网络监视软件阻止了 SSH 连接

与网络管理员合作以更正组织边缘设备的设置，从而允许与 Internet 建立 SSH 流量连接。

## <a name="source-3-cloud-service-endpoint-and-acl"></a>来源 3：云服务终结点和 ACL
> [!NOTE]
> 此来源仅适用于使用经典部署模型创建的 VM。 对于使用 Resource Manager 创建的 VM，请跳转到[来源 4：网络安全组](#nsg)。

要将云服务终结点和 ACL 从失败原因中排除，请验证同一虚拟网络中的其他 Azure VM 是否可以使用 SSH 进行连接。

![突出显示云服务终结点和 ACL 的图表](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

如果同一虚拟网络中没有其他 VM，可以轻松创建一个 VM。 有关详细信息，请参阅[使用 CLI 在 Azure 上创建 Linux VM](quick-create-cli.md)。 测试完成后，请删除多余的 VM。

如果可以与同一虚拟网络中的某个 VM 建立 SSH 连接，请检查以下区域：

* **目标 VM 上 SSH 流量的终结点配置。** 终结点的专用 TCP 端口应该与 VM 上的 SSH 服务正在侦听的 TCP 端口匹配。 （默认端口为 22）。 请在 Azure 门户中选择“虚拟机” > “VM 名称” > “设置” > “终结点”来验证 SSH TCP 端口号。
* **目标虚拟机上的 SSH 流量终结点的 ACL。** ACL 允许指定基于源 IP 地址允许或拒绝的从 Internet 传入的流量。 错误配置的 ACL 可能会阻止 SSH 流量传入终结点。 检查 ACL 以确保允许从代理服务器或其他边缘服务器的公共 IP 地址传入的流量。 有关详细信息，请参阅[关于网络访问控制列表 (ACL)](../../virtual-network/virtual-networks-acl.md)。

要将终结点从问题原因中排除，请删除当前终结点，创建另一个终结点，并指定 SSH 名称（公共和专用端口号为 TCP 端口 22）。 有关详细信息，请参阅[在 Azure 中的虚拟机上设置终结点](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>来源 4：网络安全组
通过使用网络安全组，可以对允许的入站和出站流量进行更精细的控制。 可以创建跨 Azure 虚拟网络中的子网和云服务的规则。 检查网络安全组规则，以确保允许来自和去往 Internet 的 SSH 流量。
有关详细信息，请参阅[关于网络安全组](../../virtual-network/security-overview.md)。

还可使用 IP Verify 验证 NSG 配置。 有关详细信息，请参阅 [Azure network monitoring overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)（Azure 网络监视概述）。 

## <a name="source-5-linux-based-azure-virtual-machine"></a>来源 5：基于 Linux 的 Azure 虚拟机
最后一个可能出现问题的来源是 Azure 虚拟机本身。

![突出显示基于 Linux 的 Azure 虚拟机的图表](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

如果尚未这样做，请按照[为基于 Linux 的虚拟机重置密码](reset-password.md)中的说明进行操作。

再次尝试从计算机建立连接。 如果仍然失败，则可能存在以下问题：

* 目标虚拟机上未运行 SSH 服务。
* TCP 端口 22 上未侦听 SSH 服务。 要进行测试，可在本地计算机上安装一个远程登录客户端，并运行“telnet *cloudServiceName*.cloudapp.net 22”。 此步骤将确定虚拟机是否允许与 SSH 终结点进行入站和出站通信。
* 目标虚拟机上的本地防火墙具有阻止入站或出站 SSH 流量的规则。
* Azure 虚拟机上运行的入侵检测或网络监视软件阻止了 SSH 连接。

## <a name="additional-resources"></a>其他资源
有关对应用程序访问进行故障排除的详细信息，请参阅 [Troubleshoot access to an application running on an Azure virtual machine](troubleshoot-app-connection.md)（对在 Azure 虚拟机上运行的应用程序的访问进行故障排除）
