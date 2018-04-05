---
title: 将 Key Vault 与 Azure 中 Windows VM 上的 SQL Server 集成 (Resource Manager) | Microsoft 文档
description: 了解如何自动配置用于 Azure 密钥保管库的 SQL Server 加密。 本主题说明如何将 Azure 密钥保管库集成用于通过 Resource Manager 创建的 SQL Server 虚拟机。
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/23/2017
ms.author: jroth
ms.openlocfilehash: 7df0bc4b74694baa6b1c8a30d0c126b248e51168
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>在 Azure 虚拟机上配置 SQL Server 的 Azure Key Vault 集成 (Resource Manager)
> [!div class="op_single_selector"]
> * [资源管理器](virtual-machines-windows-ps-sql-keyvault.md)
> * [经典](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>概述
SQL Server 加密功能多种多样，包括[透明数据加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)、[列级加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) 和[备份加密](https://msdn.microsoft.com/library/dn449489.aspx)。 这些加密形式要求管理和存储用于加密的加密密钥。 Azure 密钥保管库 (AKV) 服务专用于在一个高度可用的安全位置改进这些密钥的安全性和管理。 [SQL Server 连接器](http://www.microsoft.com/download/details.aspx?id=45344)使 SQL Server 能够使用 Azure 密钥保管库中的这些密钥。

如果在本地计算机上运行 SQL Server，请[按照此处的步骤从本地 SQL Server 计算机访问 Azure 密钥保管库](https://msdn.microsoft.com/library/dn198405.aspx)。 但对于 Azure VM 中的 SQL Server，可以使用 *Azure 密钥保管库集成*功能节省时间。

启用此功能后，它会自动安装 SQL Server 连接器，配置 EKM 提供程序以访问 Azure Key Vault，并创建允许你访问保管库的凭据。 在前面提到的本地文档列出的步骤中，可以看到此功能自动完成步骤 2 和步骤 3。 仍需手动执行的唯一操作是创建密钥保管库和密钥。 之后，会自动进行 SQL VM 的整个设置。 在此功能完成设置后，可以执行 T-SQL 语句，以按照通常的方式加密数据库或备份。

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>启用和配置 AKV 集成
为现有 VM 预配或配置 AKV 集成时可以启用该集成。

### <a name="new-vms"></a>新的 VM
如果使用 Resource Manager 预配新的 SQL Server 虚拟机，Azure 门户提供了启用 Azure 密匙保管库集成的步骤。 Azure 密钥保管库功能仅适用于企业版、开发人员版和评估版的 SQL Server。

![SQL Azure 密钥保管库集成](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

有关预配的详细演练，请参阅[在 Azure 门户中预配 SQL Server 虚拟机](virtual-machines-windows-portal-sql-server-provision.md)。

### <a name="existing-vms"></a>现有 VM
对于现有的 SQL Server 虚拟机，请选择 SQL Server 虚拟机。 然后选择“设置”边栏选项卡的“SQL Server 配置”部分。

![现有 VM 的 SQL AKV 集成](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

在“SQL Server 配置”边栏选项卡的自动密钥保管库集成部分，单击“编辑”按钮。

![配置现有 VM 的 SQL AKV 集成](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

完成后，单击“SQL Server 配置”边栏选项卡底部的“确定”按钮保存更改。

> [!NOTE]
> 我们在此处创建的凭据名称将在稍后映射到 SQL 登录名。 这允许 SQL 登录名访问密钥保管库。 
>
>

> [!NOTE]
> 还可以使用模板配置 AKV 集成。 有关详细信息，请参阅[用于 Azure 密钥保管库集成的 Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)。
> 
> 

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

