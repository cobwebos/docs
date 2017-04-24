---
title: "使用 IPv6 部署面向 Internet 的负载均衡器 - Azure 模板 | Microsoft 文档"
description: "如何为 Azure 负载均衡器和负载均衡的 VM 部署 IPv6 支持。"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-resource-manager
keywords: "ipv6, azure 负载均衡器, 双堆栈, 公共 ip, 本机 ipv6, 移动, iot"
ms.assetid: 2998e943-13fc-4ea9-a68c-875e53a08db3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 95962833f853886476630d703c8959bad1852e50
ms.lasthandoff: 03/15/2017

---

# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>使用模板部署分配了 IPv6 的面向 Internet 的负载均衡器解决方案

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [模板](load-balancer-ipv6-internet-template.md)

Azure 负载均衡器是位于第 4 层 (TCP, UDP) 的负载均衡器。 该负载均衡器可以在云服务或负载均衡器集的虚拟机中运行状况良好的服务实例之间分配传入流量，从而提供高可用性。 Azure 负载均衡器还可以在多个端口和/或多个 IP 地址上显示这些服务。

## <a name="example-deployment-scenario"></a>示例部署方案

下图演示了使用本文所述示例模板部署的负载均衡解决方案。

![负载均衡器方案](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

在此方案中，将创建以下 Azure 资源：

* 虚拟网络接口，用于每个已分配 IPv4 和 IPv6 地址的 VM
* 已分配 IPv4 和 IPv6 公共 IP 地址的面向 Internet 的负载均衡器
* 两个负载均衡规则，用于将公共 VIP 映射到专用终结点
* 包含两个 VM 的可用性集
* 两个虚拟机 (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>使用 Azure 门户部署模板

本文档参考了 [Azure 快速入门模板](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/)库中发布的模板。 可以从该库下载模板，或者在 Azure 中直接从库启动部署。 本文假设已将模板下载到本地计算机。

1. 打开 Azure 门户，使用有权在 Azure 订阅中创建 VM 和网络资源的帐户登录。 此外，除非使用现有资源，否则该帐户必须有权创建资源组和存储帐户。
2. 在菜单中单击“+新建”，然后在搜索框中键入“模板”。 在搜索结果中选择“模板部署”。

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. 在“全部”边栏选项卡中，单击“模板部署”。

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. 单击“创建”。

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. 单击“编辑模板”。 删除现有内容，复制/粘贴模板文件的整个内容（包括左右大括号 {}），然后单击“保存”。

    > [!NOTE]
    > 如果使用的是 Microsoft  Internet  Explorer，在粘贴内容时，将会看到一个对话框，询问是否允许访问 Windows 剪贴板。 请单击“允许访问”。

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. 单击“编辑参数”。 在“参数”边栏选项卡中，根据指导在“模板参数”部分中指定值，然后单击“保存”关闭“参数”边栏选项卡。 在“自定义部署”边栏选项卡中选择订阅、选择现有资源组或创建一个资源组。 如果要创建资源组，请选择资源组的位置。 接下来，单击“法律条款”，然后单击法律条款后面的“购买”。 Azure 开始部署资源。 部署所有资源需要花费几分钟时间。

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    有关这些参数的详细信息，请参阅本文后面的[模板参数和变量](#template-parameters-and-variables)部分。

7. 若要查看模板创建的资源，请单击“浏览”，向下滚动列表，直到看到“资源组”，然后单击它。

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. 在“资源组”边栏选项卡上，单击步骤 6 中指定的资源组名称。 随后可以看到所有已部署资源的列表。 如果一切正常，“上次部署”下面应会显示“成功”。 否则，请确保使用的帐户有权创建所需的资源。

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > 如果在完成步骤 6 之后立即浏览资源组，“上次部署”将显示“正在部署”状态，因为此时正在部署资源。

9. 在资源列表中单击“myIPv6PublicIP”。 可以看到它在 IP 地址下有一个 IPv6 地址，其 DNS 名称是在步骤 6 中为 dnsNameforIPv6LbIP 参数指定的值。 此资源是公共 IPv6 地址和主机名，可以访问 Internet 客户端。

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>验证连接

成功部署模板后，可以通过完成以下任务来验证连接：

1. 登录到 Azure 门户，并连接到模板部署创建的每个 VM。 如果部署的是 Windows Server VM，请从命令提示符运行 ipconfig /all。 可以看到 VM 同时使用了 IPv4 和 IPv6 地址。 如果部署的是 Linux VM，则需要根据 Linux 分发版提供的说明，将 Linux OS 配置为接收动态 IPv6 地址。
2. 从已连接到 IPv6 Internet 的客户端发起与负载均衡器公共 IPv6 地址的连接。 若要确认负载均衡器是否在两个 VM 之间均衡负载，可在每个 VM 上安装一个 Web 服务器，例如 Microsoft Internet Information Services (IIS)。 每台服务器的默认网页上可能会包含“Server0”或“Server1”文本用于唯一标识该服务器。 然后，在已连接到 IPv6 Internet 的客户端上打开 Internet 浏览器，导航到为负载均衡器的 dnsNameforIPv6LbIP 参数指定的主机名，确认每个 VM 的端到端 IPv6 连接。 如果只在其中一台服务器看到网页，可能需要清除浏览器缓存。 打开多个专用浏览会话。 随后应会看到来自每台服务器的响应。
3. 从已连接到 IPv4 Internet 的客户端发起与负载均衡器公共 IPv4 地址的连接。 若要确认负载均衡器是否在两个 VM 之间均衡负载，可按步骤 2 中的详述使用 IIS 进行测试。
4. 从每个 VM 向已连接到 IPv6 或 IPv4 的 Internet 设备发起出站连接。 在这两种情况下，目标设备看到的源 IP 是负载均衡器的公共 IPv4 或 IPv6 地址。

> [!NOTE]
> IPv4 和 IPv6 的 ICMP 在 Azure 网络中已被阻止。 因此，使用 ping 等 ICMP 工具始终会失败。 若要测试连接，请使用 TCP 替代方法，例如 TCPing 或 PowerShell Test-NetConnection cmdlet。 请注意，图中显示的 IP 地址是可能会出现的示例值。 由于 IPv6 地址是动态分配的，你收到的地址可能会根据区域的不同而异。 此外，负载均衡器上公共 IPv6 地址前缀与后端池中专用 IPv6 地址的前缀经常不同。

## <a name="template-parameters-and-variables"></a>模板参数和变量

Azure Resource Manager 模板包含可根据需要自定义的多个变量和参数。 对于不希望用户更改的固定值，可以使用变量。 对于在部署模板不希望用户提供的值，可以使用参数。 该示例模板是针对本文所述的方案配置的。 可以根据环境需求自定义此模板。

本文中使用的示例模板包含以下变量和参数：

| 参数/变量 | 说明 |
| --- | --- |
| adminUsername |指定用于登录到虚拟机的管理员帐户名。 |
| adminPassword |指定用于登录到虚拟机的管理员帐户密码。 |
| dnsNameforIPv4LbIP |指定想要分配为负载均衡器公共名称的 DNS 主机名。 此名称解析为负载均衡器的公共 IPv4 地址。 此名称必须是小写，并与正则表达式匹配：^[a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP |指定想要分配为负载均衡器公共名称的 DNS 主机名。 此名称解析为负载均衡器的公共 IPv6 地址。 此名称必须是小写，并与正则表达式匹配：^[a-z][a-z0-9-]{1,61}[a-z0-9]$. 此名称可与 IPv4 地址的名称相同。 当名称相同时，如果客户端针对此名称发出 DNS 查询，Azure 将返回 A 和 AAAA 记录。 |
| vmNamePrefix |指定 VM 名称前缀。 创建 VM 时，模板将在名称后面追加一个编号（0、1 等等）。 |
| nicNamePrefix |指定网络接口名称前缀。 创建网络接口时，模板将在名称后面追加一个编号（0、1 等等）。 |
| storageAccountName |输入现有存储帐户的名称，或指定模板创建的新存储帐户的名称。 |
| availabilitySetName |输入要配合 VM 使用的可用性集的名称 |
| addressPrefix |用于定义虚拟网络地址范围的地址前缀 |
| subnetName |为 VNet 创建的子网的名称 |
| subnetPrefix |用于定义子网地址范围的地址前缀 |
| vnetName |指定 VM 使用的 VNet 的名称 |
| ipv4PrivateIPAddressType |专用 IP 地址使用的分配方法（Static 或 Dynamic） |
| ipv6PrivateIPAddressType |专用 IP 地址使用的分配方法 (Dynamic)。 IPv6 仅支持 Dynamic（动态）分配。 |
| numberOfInstances |模板部署的负载均衡实例的数目 |
| ipv4PublicIPAddressName |指定用来与负载均衡器公共 IPv4 地址通信的 DNS 名称。 |
| ipv4PublicIPAddressType |公共 IP 地址使用的分配方法（Static 或 Dynamic） |
| Ipv6PublicIPAddressName |指定用来与负载均衡器公共 IPv6 地址通信的 DNS 名称。 |
| ipv6PublicIPAddressType |公共 IP 地址使用的分配方法 (Dynamic)。 IPv6 仅支持 Dynamic（动态）分配。 |
| lbName |指定负载均衡器的名称。 此名称将显示在门户中，或者通过 CLI 或 PowerShell 命令引用它时会用到它。 |

模板中的其余变量是 Azure 创建资源时分配的派生值。 请不要更改这些变量。

