---
title: 快速入门 - 在 Azure 门户中创建虚拟机规模集 | Microsoft Docs
description: 了解如何在 Azure 门户中快速创建虚拟机规模集
keywords: 虚拟机规模集
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: quickstart
ms.custom: H1Hack27Feb2017
ms.date: 03/27/18
ms.author: iainfou
ms.openlocfilehash: 03b4468fd1032b94cde5d83bc871c32e0c21df1c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
ms.locfileid: "30247352"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>快速入门：在 Azure 门户中创建虚拟机规模集
利用虚拟机规模集，可以部署和管理一组相同的、自动缩放的虚拟机。 可以手动缩放规模集中的 VM 数，也可以定义规则，以便根据资源使用情况（如 CPU 使用率、内存需求或网络流量）进行自动缩放。 然后，Azure 负载均衡器会将流量分配到规模集中的 VM 实例。 本快速入门介绍如何在 Azure 门户中创建虚拟机规模集。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="log-in-to-azure"></a>登录 Azure
通过 http://portal.azure.com 登录到 Azure 门户。


## <a name="create-virtual-machine-scale-set"></a>创建虚拟机规模集
可使用 Windows Server 映像或 Linux 映像（如 RHEL、CentOS、Ubuntu 或 SLES）部署规模集。

1. 在 Azure 门户的左上角单击“创建资源”。
2. 搜索“规模集”，选择“虚拟机规模集”，然后选择“创建”。
3. 为规模集输入名称，如 myScaleSet。
4. 选择所需 OS 类型，如 Windows Server 2016 Datacenter。
5. 输入所需资源组名（如 myResourceGroup）和位置（如美国东部）。
6. 输入所需的用户名，并选择首选的身份验证类型。
    - 密码长度必须至少为 12 个字符，并且必须满足以下 4 个复杂性要求的其中 3 个：1 个小写字符、1 个大写字符、1 个数字和 1 个特殊字符。 有关详细信息，请参阅[用户名和密码要求](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)。
    - 如果选择 Linux OS 磁盘映像，可选择 SSH 公钥。 仅提供公钥，如 *~/.ssh/id_rsa.pub*。 可从门户使用 Azure Cloud Shell [创建和使用 SSH 密钥](../virtual-machines/linux/mac-create-ssh-keys.md)。

7. 输入公共 IP 地址名称，如 myPublicIP。
8. 输入唯一的域名标签，如 myuniquedns。 此 DNS 标签构成规模集前端负载均衡器的 FQDN 的基础。
9. 若要确认规模集选项，请选择“创建”。

    ![在 Azure 门户中创建虚拟机规模集](./media/virtual-machine-scale-sets-create-portal/create-scale-set.png)


## <a name="connect-to-a-vm-in-the-scale-set"></a>连接到规模集中的 VM
在门户中创建规模集时，会创建一个负载均衡器。 网络地址转换 (NAT) 规则用于将流量分发到规模集实例（如 RDP 或 SSH）以实现远程连接。

查看这些 NAT 规则和规模集实例的连接信息：

1. 选择上一步骤中创建的资源组，如 myResourceGroup。
2. 从资源列表中，选择负载平衡器，如 myScaleSetLab。
3. 在窗口左侧的菜单中，选择“入站 NAT 规则”。

    ![入站 NAT 规则允许连接到虚拟机规模集实例](./media/virtual-machine-scale-sets-create-portal/inbound-nat-rules.png)

可以使用这些 NAT 规则连接到规模集中的每个 VM。 每个 VM 实例会列出目标 IP 地址和 TCP 端口值。 例如，如果目标 IP 地址为 104.42.1.19，TCP 端口为 50001，则会根据以下情况连接到 VM 实例：

- 对于 Windows 规模集，通过 RDP 连接到 `104.42.1.19:50001` 上的 VM 实例
- 对于 Linux 规模集，通过 SSH 连接到 `ssh azureuser@104.42.1.19 -p 50001` 上的 VM 实例

出现提示时，输入上一步中创建规模集时指定的凭据。 规模集实例是可与之正常交互的常规 VM。 有关如何在规模集实例上部署和运行应用程序的详细信息，请参阅[在虚拟机规模集上部署应用程序](virtual-machine-scale-sets-deploy-app.md)


## <a name="clean-up-resources"></a>清理资源
不再需要资源组、规模集和所有相关的资源时，可将其删除。 为此，请选择适用于 VM 的资源组，然后单击“删除”。


## <a name="next-steps"></a>后续步骤
在本快速入门中，我们已在 Azure 门户中创建了一个基本的规模集。 若要了解详细信息，请继续学习有关如何创建和管理 Azure 虚拟机规模集的教程。

> [!div class="nextstepaction"]
> [创建和管理 Azure 虚拟机规模集](tutorial-create-and-manage-powershell.md)