---
title: 如何使用共享资源创建实验室 |Azure 实验室服务
description: 了解如何创建需要在学生间共享资源的实验室。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 9cb5698f95aa220208fb02a35a52ff5363a173ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85443360"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>如何在 Azure 实验室服务中使用共享资源创建实验室

有时，在创建教室实验室时，可能需要在实验室中的所有学生间共享某些资源。  例如，你有一个用于数据库类的授权服务器或 SQL Server。  本文介绍为实验室启用共享资源的步骤。  我们还将讨论如何限制对该共享资源的访问权限。

## <a name="architecture"></a>体系结构

如下面的关系图所示，我们将有一个实验室帐户。  实验室帐户将具有 vnet 对等互连设置，以便实验室的虚拟网络连接到共享资源的网络。  在下图中，有两个虚拟网络具有不重叠的 IP 范围。  这些 IP 范围只是示例范围。  另请注意，共享资源虚拟网络与实验室帐户位于同一订阅中。

![带有共享资源体系结构的实验室服务](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>设置共享资源

必须先创建共享资源的虚拟网络，然后才能创建实验室。  有关如何创建虚拟网络的详细信息，请参阅[创建虚拟网络](../virtual-network/quick-create-portal.md)。  规划虚拟网络范围，使其不与实验室计算机的 ip 地址重叠很重要。  有关规划网络的详细信息，请参阅[计划虚拟网络](../virtual-network/virtual-network-vnet-plan-design-arm.md)一文。 在本示例中，共享资源位于范围为 10.2.0.0/16 的虚拟网络中。  如果尚未执行此操作，请[创建一个子网](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)来容纳共享资源。  在此示例中，我们使用 10.2.0.0/24 范围，但你的范围可能会不同，具体取决于你的网络需求。

共享资源可以是在虚拟机上运行的软件或 Azure 提供的服务。 共享资源应该通过专用 IP 地址提供。  通过仅通过专用 IP 提供共享资源，你可以限制对该共享资源的访问权限。

该图还显示了网络安全组（NSG），可用于限制来自学生 VM 的流量。  例如，你可以编写一条安全规则，该规则指出来自学生 VM 的 IP 地址的流量只能访问一个共享资源，而不能访问其他任何资源。  有关如何设置安全规则的详细信息，请参阅[管理网络安全组](../virtual-network/manage-network-security-group.md#work-with-security-rules)。 如果要限制对特定实验室的共享资源的访问，请从[实验室帐户的实验室设置](manage-labs.md#view-labs-in-a-lab-account)获取实验室的 IP 地址，并将入站规则设置为仅允许来自该 IP 地址的访问。  不要忘记允许端口49152到65535的 IP 地址。  （可选）可以使用 "[虚拟机池" 页](how-to-set-virtual-machine-passwords.md)查找学生 vm 的专用 IP 地址。

如果共享资源是运行必要软件的 Azure 虚拟机，则可能需要修改虚拟机的默认防火墙规则。

## <a name="lab-account"></a>实验室帐户

若要使用共享资源，必须将实验室帐户设置为使用[对等互连虚拟网络](how-to-connect-peer-virtual-network.md)。  在这种情况下，我们会对虚拟网络进行对等互连。

>[!WARNING]
>在将实验室帐户对等互连到共享资源虚拟网络**后**，必须创建类的实验室。  
模板计算机

将实验室帐户对等互连虚拟网络后，模板计算机现在应可以访问共享资源。  可能需要根据所访问的共享资源更新防火墙规则。
