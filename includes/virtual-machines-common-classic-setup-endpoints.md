---
title: include 文件
description: include 文件
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/23/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ee5faedd4f59aa791424a1f178f0462922f21d28
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58115792"
---
每个终结点都拥有*公用端口*和*专用端口*：

* Azure 负载均衡器使用公用端口侦听从 Internet 传入的虚拟机流量。
* 虚拟机使用专用端口侦听传入流量（通常发送到虚拟机上运行的应用程序或服务）。

使用 Azure 门户创建终结点时，将为 IP 协议和众所周知的网络协议的 TCP 或 UDP 端口提供默认值。 对于自定义终结点，请指定正确的 IP 协议（TCP 或 UDP），以及公用和专用端口。 若要将传入流量随机分布到多个虚拟机，请创建包含多个终结点的负载均衡集。

创建终结点后，可以使用访问控制列表 (ACL) 定义规则，根据传入流量的源 IP 地址允许或拒绝终结点的公用端口的传入流量。 但是，如果虚拟机位于 Azure 虚拟网络中，请改用网络安全组。 有关详细信息，请参阅[关于网络安全组](../articles/virtual-network/security-overview.md)。

> [!NOTE]
> 将对与 Azure 自动设置的远程连接终结点关联的端口自动完成 Azure 虚拟机的防火墙配置。 对于为所有其他终结点指定的端口，不会自动对虚拟机防火墙进行任何配置。 为虚拟机创建终结点时，请确保虚拟机的防火墙也允许与终结点配置对应的协议和专用端口的流量。 若要配置防火墙，请参阅有关在虚拟机上运行的操作系统的文档或联机帮助。
>
>

## <a name="create-an-endpoint"></a>创建终结点
1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“虚拟机”，然后选择要配置的虚拟机。

3. 在“设置”组中选择“终结点”。 此时会显示“终结点”页，其中列出了虚拟机的所有当前终结点。 （本示例适用于 Windows VM。 如果是 Linux VM，则默认显示一个 SSH 终结点。）

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![EndPoints](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)


4. 在终结点条目上方的命令栏中，选择“添加”。 此时会显示“添加终结点”页。

5. 对于“名称”，请输入终结点的名称。

6. 对于“协议”，请选择“TCP”或“UDP”。

7. 对于“公共端口”，请输入来自 Internet 的传入流量的端口号。 

8. 对于“专用端口”，请输入虚拟机正在侦听的端口号。 公共和专用端口号可以不同。 确保已将虚拟机的防火墙配置为允许与协议和专用端口对应的流量。

9. 选择“确定”。

新终结点将在“终结点”页上列出。

![成功创建终结点](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>管理终结点上的 ACL
若要定义一组可以发送流量的计算机，终结点上的 ACL 可以基于源 IP 地址限制流量。 按照下列步骤，在终结点上添加、修改或删除 ACL。

> [!NOTE]
> 如果终结点是负载均衡集的一部分，则会将对终结点上 ACL 作出的任何更改应用到该集中的所有终结点。
>
>

如果虚拟机位于 Azure 虚拟网络中，请使用网络安全组，而不要使用 ACL。 有关详细信息，请参阅[关于网络安全组](../articles/virtual-network/security-overview.md)。

1. 登录到 Azure 门户。

2. 选择“虚拟机”，然后选择要配置的虚拟机的名称。

3. 选择“终结点”。 在终结点列表中选择相应的终结点。 ACL 列表位于页面底部。

   ![指定 ACL 详细信息](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. 使用列表中的行为 ACL 添加、删除或编辑规则，并更改其顺序。 “远程子网”值是从 Internet 传入流量的 IP 地址范围，Azure 负载均衡器将使用该值根据流量的源 IP 地址允许或拒绝传入流量。 请务必以无类域间路由 (CIDR) 格式（也称为地址前缀格式）指定 IP 地址范围。 例如，`10.1.0.0/8`。

   ![新的 ACL 条目](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


可以使用规则只允许来自与 Internet 上计算机对应的特定计算机的流量，或拒绝来自特定已知地址范围的流量。

按照从第一个规则开始并以最后一个规则结束的顺序评估规则。 因此，规则应按最低限制到最高限制排序。 有关详细信息，请参阅[什么是网络访问控制列表](../articles/virtual-network/virtual-networks-acl.md)。
