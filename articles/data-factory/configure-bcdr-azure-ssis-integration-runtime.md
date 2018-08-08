---
title: Azure-SSIS 集成运行时的业务连续性和灾难恢复 (BCDR) 建议 | Microsoft Docs
description: 本文概述 Azure-SSIS 集成运行时的业务连续性和灾难恢复建议。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 37347df2d543116085f52fed76c692b60fac2ad6
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295181"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-recommendations-for-azure-ssis-integration-runtime"></a>Azure-SSIS 集成运行时的业务连续性和灾难恢复 (BCDR) 建议

若要实现灾难恢复，可以停止区域中当前正在运行的 Azure-SSIS 集成运行时，然后切换到另一个区域以重启该运行时。 我们建议使用 [Azure 配对区域](../best-practices-availability-paired-regions.md)来实现此目的。

## <a name="prerequisites"></a>先决条件

- 确保为 Azure SQL 数据库服务器启用灾难恢复，以防该服务器同时发生服务中断。 有关详细信息，请参阅[使用 Azure SQL 数据库确保业务连续性的相关概述](../sql-database/sql-database-business-continuity.md)。

- 如果在当前区域中使用虚拟网络，则需要在新区域中使用另一个虚拟网络连接到 Azure-SSIS 集成运行时。 有关详细信息，请参阅[将 Azure-SSIS 集成运行时加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)。

- 如果使用自定义设置，可能需要为存储自定义设置脚本和关联文件的 Blob 容器准备另一个 SAS URI，以便在中断期间可以继续访问该容器。 有关详细信息，请参阅[在 Azure-SSIS 集成运行时中配置自定义设置](how-to-configure-azure-ssis-ir-custom-setup.md)。

## <a name="steps"></a>Steps

遵循以下步骤停止 Azure-SSIS IR，切换到新区域，然后再次启动该 IR。

1. 在原始区域中停止 IR。

2. 在 PowerShell 中调用以下命令来更新 IR

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    有关此 PowerShell 命令的详细信息，请参阅[在 Azure 数据工厂中创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)

3. 再次启动 IR。

## <a name="next-steps"></a>后续步骤

考虑 Azure-SSIS IR 的以下其他配置选项：

- [配置 Azure-SSIS 集成运行时以实现高性能](configure-azure-ssis-integration-runtime-performance.md)

- [自定义 Azure-SSIS 集成运行时的设置](how-to-configure-azure-ssis-ir-custom-setup.md)

- [预配 Azure-SSIS 集成运行时企业版](how-to-configure-azure-ssis-ir-enterprise-edition.md)
