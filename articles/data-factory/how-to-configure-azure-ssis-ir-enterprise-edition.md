---
title: 预配 Azure-SSIS 集成运行时企业版 | Microsoft Docs
description: 本文介绍 Azure-SSIS 集成运行时企业版的功能以及如何配置它
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 73997345895bc54f54db1d66c0c6c24c24153dd2
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36267839"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>预配 Azure-SSIS 集成运行时企业版

在 Azure-SSIS 集成运行时企业版中可以使用以下高级功能：
-   变更数据捕获 (CDC) 组件
-   Oracle、Teradata 和 SAP BW 连接器
-   SQL Server Analysis Services (SSAS) 和 Azure Analysis Services (AAS) 连接器和转换
-   模糊分组和模糊查找转换
-   字词提取和字词查找转换

上述某些功能需要安装附加的组件来自定义 Azure-SSIS IR。 有关如何安装附加组件的详细信息，请参阅 [Azure-SSIS 集成运行时的自定义安装](how-to-configure-azure-ssis-ir-custom-setup.md)。

## <a name="enterprise-features"></a>企业功能

| **企业功能** | **说明** |
|---|---|
| CDC 组件 | CDC 源、控制任务和拆分器转换已预装在 Azure-SSIS IR 企业版上。 若要连接到 Oracle，还需要在另一台计算机上安装 CDC 设计器和服务。 |
| Oracle 连接器 | Oracle 连接管理器、源和目标已预装在 Azure-SSIS IR 企业版上。 还需要在 Azure-SSIS IR 上安装 Oracle 调用接口 (OCI) 驱动程序，并在必要时配置 Oracle 传输网络底层 (TNS)。 有关详细信息，请参阅 [Azure-SSIS 集成运行时的自定义设置](how-to-configure-azure-ssis-ir-custom-setup.md)。 |
| Teradata 连接器 | 需要在 Azure-SSIS IR 企业版上安装 Teradata 连接管理器、源、目标，以及 Teradata 并行传输程序 (TPT) API 和 Teradata ODBC 驱动程序。 有关详细信息，请参阅 [Azure-SSIS 集成运行时的自定义设置](how-to-configure-azure-ssis-ir-custom-setup.md)。 |
| SAP BW 连接器 | SAP BW 连接管理器、源和目标已预装在 Azure-SSIS IR 企业版上。 还需要在 Azure-SSIS IR 上安装 SAP BW 驱动程序。 这些连接器支持 SAP BW 7.0 或更低版本。 若要连接到更高版本的 SAP BW 或其他 SAP 产品，可以从第三方 ISV 购买 SAP 连接器并将其安装在 Azure-SSIS IR 上。 有关如何安装附加组件的详细信息，请参阅 [Azure-SSIS 集成运行时的自定义安装](how-to-configure-azure-ssis-ir-custom-setup.md)。 |
| Analysis Services 组件               | 数据挖掘模型定型目标、维度处理目标、分区处理目标以及数据挖掘查询转换已预装在 Azure-SSIS IR 企业版上。 所有这些组件支持 SQL Server Analysis Services (SSAS)，但只有分区处理目标支持 Azure Analysis Services (AAS)。 若要连接到 SSAS，还需要[在 SSISDB 中配置 Windows 身份验证凭据](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)。 除了这些组件以外，Analysis Services 执行 DDL 任务、Analysis Services 处理任务和数据挖掘查询任务也已预装在 Azure-SSIS IR 标准/企业版上。 |
| 模糊分组和模糊查找转换  | 模糊分组和模糊查找转换已预装在 Azure-SSIS IR 企业版上。 这些组件支持使用 SQL Server 和 Azure SQL 数据库来存储参考数据。 |
| 字词提取和字词查找转换 | 字词提取和字词查找转换已预装在 Azure-SSIS IR 企业版上。 这些组件支持使用 SQL Server 和 Azure SQL 数据库来存储参考数据。 |

## <a name="instructions"></a>说明

1.  下载并安装 [Azure PowerShell（5.4 或更高版本）](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018)。

2.  使用 PowerShell 预配或重新配置 Azure-SSIS IR 时，请在启动 Azure-SSIS IR 之前，先运行 `Set-AzureRmDataFactoryV2IntegrationRuntime` 并指定 **Enterprise** 作为 **Edition** 参数的值。 下面是一个示例脚本：

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>后续步骤

-   [Azure-SSIS 集成运行时的自定义安装](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [如何为 Azure-SSIS 集成运行时开发付费或许可的自定义组件](how-to-develop-azure-ssis-ir-licensed-components.md)
