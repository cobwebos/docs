---
title: 有关为 Azure VM 上的 Service Fabric 群集创建基础结构的教程 - Azure Service Fabric | Microsoft Docs
description: 本教程介绍如何设置 Azure VM 基础结构来运行 Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: v-vasuke
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: v-vasuke
ms.custom: mvc
ms.openlocfilehash: c9dd9cf0f0fb6d20d6837b07ab46d376e379ca25
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177727"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>教程：创建用于托管 Service Fabric 群集的 Azure VM 基础结构

Service Fabric 独立群集为你提供选择自己的环境的选项，并创建群集作为 Service Fabric 所采用的“任何 OS、任何云”方法的一部分。 在本系列教程中，我们将创建一个托管在 Azure VM 上的独立群集，并将应用程序安装到其中。

本教程是一个系列中的第一部分。 在本文中，我们将生成所需的 Azure VM 资源用于托管 Service Fabric 的独立群集。 在后续文章中，我们需要安装 Service Fabric 独立套件、将示例应用程序安装到群集，最后清理群集。

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 创建一组 AzureVM 实例
> * 修改安全组
> * 登录到其中一个实例
> * 准备 Service Fabric 的实例

## <a name="prerequisites"></a>先决条件

需要一个 Azure 订阅才能完成此教程。  如果没有帐户，请转到 [Azure 门户](https://portal.azure.com)创建一个帐户。

## <a name="create-azure-virtual-machine-instances"></a>创建 Azure 虚拟机实例

1. 登录 Azure 门户，选择“虚拟机”（而不是“虚拟机(经典)”）  。

   ![Azure 门户 VM][az-console]

2. 选择”添加”按钮，这将打开“创建虚拟机”窗体   。

3. 在“基本”选项卡上，确保选择你想选择的订阅和资源组（建议使用新资源组）  。

4. 将“映像”类型更改为“Windows Server 2016 Datacenter”   。 
 
5. 将实例“大小”更改为“标准 DS2 v2”   。 设置一个管理员用户名和密码，记下它们的内容   。

6. 暂时将“入站端口规则”保留为“已阻止”；我们将在下一节中配置它们  。

7. 在“网络”选项卡上，创建一个新虚拟网络并记下其名称   。

8. 接下来，将“NIC 网络安全组”设置为“高级”   。 创建新的安全组，记下其名称，并创建以下规则以允许来自任何源的 TCP 流量：

   ![sf-inbound][sf-inbound]

   * 端口 `3389`，适用于 RDP 和 ICMP（基本连接）。
   * 端口 `19000-19003`，适用于 Service Fabric。
   * 端口 `19080-19081`，适用于 Service Fabric。
   * 端口 `8080`，适用于 web 浏览器请求。

   > [!TIP]
   > 若要在 Service Fabric 中将虚拟机连接到一起，托管基础结构的 VM 需有相同的凭据。  可通过两种常用方法获取一致的凭据：将所有 VM 加入同一个域，或者在每个 VM 上设置相同的管理员密码。 幸运的是，Azure 支持同一虚拟网络上所有虚拟机轻松连接，因此我们肯定能在同一网络上拥有我们的所有实例  。

9. 添加另一个规则。 将源设置为“服务标记”，并将源服务标记设置为“VirtualNetwork”   。 Service Fabric 需要为群集内通信打开以下端口：135、137-139、445、20001-20031、20606-20861。

   ![vnet-inbound][vnet-inbound]

10. 其他选项可以接受默认状态。 如果你愿意，可以查看它们，然后启动你的虚拟机。

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>创建 Service Fabric 群集的更多实例

再启动两个虚拟机，确保与上一部分所述的设置相同  。 特别注意保持相同的管理员用户名和密码。 不应该重新创建虚拟网络和 NIC 网络安全组；选择你已从下拉菜单中创建的虚拟网络和 NIC 网络安全组   。 部署每个实例可能需要几分钟时间。

## <a name="connect-to-your-instances"></a>连接到实例

1. 从“虚拟机”部分选择一个你的实例  。

2. 在“概述”选项卡中，记下专用 IP 地址   。 然后单击“连接”。 

3. 在“RDP”选项卡中，注意我们使用的是之前特别设置为打开的公共 IP 地址和端口 3389  。 下载 RDP 文件。
 
4. 打开 RDP 文件，并在出现提示时输入 VM 设置中提供的用户名和密码。

5. 连接到实例后，需要验证远程注册表是否正在运行，并打开必需的端口。

6. 若要在防火墙中打开端口，请使用以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139
   ```

7. 在其他实例中重复此过程，请再次记下专用 IP 地址。

## <a name="verify-your-settings"></a>验证设置

1. 若要验证基本连接，使用 RDP 连接到一个 VM。

2. 在 VM 中打开命令提示符，然后使用 ping 命令从一个 VM 连接到另一个 VM，将以下 IP 地址替换为先前记下的一个专用 IP 地址（不是你已经连接到 VM 的 IP）  。

   ```
   ping 172.31.20.163
   ```

   如果类似于 `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` 的输出重复出现四次，则表示实例之间的连接正常。

   现在你的实例已为 Service Fabric 准备好。

## <a name="next-steps"></a>后续步骤

本系列教程的第一篇文章介绍了如何启动三个 Azure VM 实例，并将其配置为用于 Service Fabric 安装：

> [!div class="checklist"]
> * 创建一组 Azure VM 实例
> * 修改安全组
> * 登录到其中一个实例
> * 准备 Service Fabric 的实例

请继续学习本系列教程的第二篇文章，了解如何在群集中配置 Service Fabric。

> [!div class="nextstepaction"]
> [安装 Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
