---
title: 将 Key Vault 与 Azure 中 Windows VM 上的 SQL Server 集成 (Resource Manager) | Microsoft 文档
description: 了解如何自动配置用于 Azure 密钥保管库的 SQL Server 加密。 本主题说明如何将 Azure 密钥保管库集成用于通过 Resource Manager 创建的 SQL 虚拟机。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5f1a8e2768c7ea9f252a35ab58480c613d3face6
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231958"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>为 Azure VM (Resource Manager) 上的 SQL Server 配置 Azure 密钥保管库集成
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server 加密功能多种多样，包括[透明数据加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)、[列级加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) 和[备份加密](https://msdn.microsoft.com/library/dn449489.aspx)。 这些加密形式要求管理和存储用于加密的加密密钥。 Azure 密钥保管库服务专用于在一个高度可用的安全位置改进这些密钥的安全性和管理。 [SQL Server 连接器](https://www.microsoft.com/download/details.aspx?id=45344) 使 SQL Server 能够使用 Azure 密钥保管库中的这些密钥。

如果在本地运行 SQL Server，请按照此处提供的步骤[通过本地 SQL Server 实例访问 Azure Key Vault](https://msdn.microsoft.com/library/dn198405.aspx)。 但对于 Azure VM 中的 SQL Server，可以使用 *Azure Key Vault 集成*功能节省时间。

启用此功能后，它会自动安装 SQL Server 连接器、配置 EKM 提供程序以访问 Azure 密钥保管库，并创建凭据以使你能够访问保管库。 在前面提到的本地文档列出的步骤中，可以看到此功能自动完成步骤 2 和步骤 3。 仍需手动执行的唯一操作是创建密钥保管库和密钥。 之后，会自动进行 SQL Server VM 的整个设置。 在此功能完成设置后，可以执行 Transact-SQL (T-SQL) 语句，以按照通常的方式加密数据库或备份。

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > 可扩展密钥管理 (EKM) 提供程序版本 1.0.4.0 通过 [SQL 基础结构即服务 (IaaS) 扩展](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)安装在 SQL Server VM 上。 升级 SQL IaaS 扩展将不会更新提供程序版本。 如有需要（例如，迁移到 SQL 托管实例时），请考虑手动更新 EKM 提供程序版本。


## <a name="enabling-and-configuring-key-vault-integration"></a>启用和配置密钥保管库集成
为现有 VM 预配或配置密钥保管库集成时可以启用该集成。

### <a name="new-vms"></a>新的 VM
如果使用资源管理器预配新的 SQL 虚拟机，Azure 门户提供了启用 Azure 密匙保管库集成的方式。 Azure 密钥保管库功能仅适用于企业版、开发人员版和评估版的 SQL Server。

![SQL Azure 密钥保管库集成](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

有关预配的详细演练，请参阅[在 Azure 门户中预配 SQL 虚拟机](create-sql-vm-portal.md)。

### <a name="existing-vms"></a>现有 VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

对于现有的 SQL 虚拟机，请打开 [SQL 虚拟机资源](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)并在“设置”下选择“安全”。  选择 **启用**以启用 Azure Key Vault 集成。 

![现有 VM 的 SQL 密钥保管库集成](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

完成后，选择“安全”页底部的“应用”按钮，以保存更改 。

> [!NOTE]
> 我们在此处创建的凭据名称将在稍后映射到 SQL 登录名。 这允许 SQL 登录名访问密钥保管库。 


> [!NOTE]
> 还可以使用模板配置密钥保管库集成。 有关详细信息，请参阅 [Azure quickstart template for Azure Key Vault integration](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)（用于 Azure 密钥保管库集成的 Azure 快速入门模板）。


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
