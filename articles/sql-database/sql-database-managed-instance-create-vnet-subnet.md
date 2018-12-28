---
title: Azure SQL 数据库托管实例创建 VNet | Microsoft Docs
description: 本主题介绍如何创建可在其中部署 Azure SQL 数据库托管实例的虚拟网络 (VNet)。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: ebdfc80d3802ad8eb7da6fb7f152efdaee8d777d
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344935"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例配置 VNet

本主题说明如何创建可在其中部署 Azure SQL 数据库托管实例的有效 VNet 和子网。

Azure SQL 数据库托管实例必须部署在 Azure [虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 中。 此部署支持以下方案： 
- 保护专用 IP 地址。
- 直接从本地网络连接到托管实例 
- 将托管实例连接到链接服务器或其他本地数据存储 
- 将托管实例连接到 Azure 资源  

  > [!Note]
  > 在部署第一个实例之前，应[确定托管实例的子网大小](sql-database-managed-instance-determine-size-vnet-subnet.md)，因为将资源放入子网中后，便无法调整子网大小。
  > 如果打算使用现有的虚拟网络，则需要修改该网络的配置，以适应托管实例。 有关详细信息，请参阅[根据托管实例修改现有的虚拟网络](sql-database-managed-instance-configure-vnet-subnet.md)。 

## <a name="create-a-new-virtual-network"></a>创建新的虚拟网络

创建和配置虚拟网络的最简单方法是使用 Azure 资源管理器部署模板。

1. 登录到 Azure 门户。

2. 使用“部署到 Azure”按钮在 Azure 云中部署虚拟网络：

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  此按钮将打开一个窗体，可以使用该窗体来配置你可以在其中部署托管实例的网络环境。

  > [!Note]
  > 此 Azure 资源管理器模板将部署包含两个子网的虚拟网络。 一个名为 **ManagedInstances** 的子网预留给托管实例并且已预配置了路由表，而名为 **Default** 的另一个子网用于应当访问托管实例的其他资源（例如 Azure 虚拟机）。 如果不需要 **Default** 子网，可以将其删除。

3. 配置网络环境。 在以下窗体上，可以配置网络环境的参数：

![配置 Azure 网络](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

可以更改 VNet 和子网的名称并调整与网络资源关联的 IP 范围。 按下“购买”按钮后，此窗体将创建并配置你的环境。 如果不需要两个子网，可以删除默认子网。 

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例](sql-database-managed-instance.md)。
- 了解[托管实例中的连接体系结构](sql-database-managed-instance-connectivity-architecture.md)。
- 了解如何[根据托管实例修改现有的虚拟网络](sql-database-managed-instance-configure-vnet-subnet.md)
- 有关如何创建 VNet、创建托管实例，以及从数据库备份还原数据库的教程，请参阅[创建 Azure SQL 数据库托管实例](sql-database-managed-instance-get-started.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)
