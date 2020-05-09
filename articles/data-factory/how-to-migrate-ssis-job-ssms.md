---
title: 将本地 SQL Server Integration Services （SSIS）作业迁移到 Azure 数据工厂
description: 本文介绍如何使用 SQL Server Management Studio 将 SQL Server Integration Services （SSIS）作业迁移到 Azure 数据工厂管道/活动/触发器。
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: b27fe2abc50396b527e61487acf9797db59c1cce
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627579"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>通过 SSMS 将 SQL Server 代理作业迁移到 ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在[将本地 SQL Server Integration Services （SSIS）工作负荷迁移到 ADF 中的 ssis](scenario-ssis-migration-overview.md)时，迁移 ssis 包后，可以通过 SQL SERVER MANAGEMENT STUDIO （SSMS） **SSIS 作业迁移向导**，将作业步骤类型为 SQL Server Integration Services 包的 SQL Server 代理作业批处理迁移到 AZURE 数据工厂（ADF）管道/活动/计划触发器。

通常，对于具有适用作业步骤类型的选定 SQL 代理作业， **SSIS 作业迁移向导**可以：

- 将本地 SSIS 包位置映射到将包迁移到的位置，这些包可由 ADF 中的 SSIS 访问。
    > [!NOTE]
    > 仅支持文件系统的包位置。
- 按如下所示将适用的作业迁移到相应的 ADF 资源：

|SQL 代理作业对象  |ADF 资源  |说明|
|---------|---------|---------|
|SQL 代理作业|管道     |将*为\<作业名称>生成*管道名称。 <br> <br> 内置代理作业不适用： <li> SSIS 服务器维护作业 <li> syspolicy_purge_history <li> collection_set_ * <li> mdw_purge_data_ * <li> sysutility_ *|
|SSIS 作业步骤|执行 SSIS 包活动|<li> 活动名称将是\<步骤名称>。 <li> 在作业步骤中使用的代理帐户将作为此活动的 Windows 身份验证进行迁移。 <li> 迁移中将忽略在作业步骤中定义的 "*使用32位运行时*" 之外的*执行选项*。 <li> 在迁移中将忽略在作业步骤中定义的*验证*。|
|schedule      |计划触发器        |将*为\<计划名称>生成*计划触发器的名称。 <br> <br> SQL 代理作业计划中的以下选项在迁移中将被忽略： <li> 二级时间间隔。 <li> *SQL Server 代理启动时自动启动* <li> *CPU 空闲时启动* <li> *工作日*和*周末*<time zone> <br> 下面是将 SQL 代理作业计划迁移到 ADF 计划触发器后的区别： <li> ADF 计划触发器后续运行与前面的已触发运行的执行状态无关。 <li> ADF 计划触发器循环配置与 SQL 代理作业中的每日频率不同。|

- 在本地输出文件夹中生成 Azure 资源管理器（ARM）模板，并直接或在以后手动部署到数据工厂。 有关 ADF 资源管理器模板的详细信息，请参阅[DataFactory 资源类型](https://docs.microsoft.com/azure/templates/microsoft.datafactory/allversions)。

## <a name="prerequisites"></a>先决条件

本文中所述的功能需要 SQL Server Management Studio 版本18.5 或更高版本。 若要获取 SSMS 最新版本，请参阅[下载 SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)。

## <a name="migrate-ssis-jobs-to-adf"></a>将 SSIS 作业迁移到 ADF

1. 在 SSMS 的对象资源管理器中，选择 "SQL Server 代理"，选择 "作业"，然后右键单击并选择 "**将 SSIS 作业迁移到 ADF**"。
![下拉菜单](media/how-to-migrate-ssis-job-ssms/menu.png)

1. 登录 Azure，选择 "Azure 订阅"、"数据工厂" 和 "Integration Runtime"。 Azure 存储是可选的，如果要迁移的 SSIS 作业具有 SSIS 文件系统包，则可在包位置映射步骤中使用。
![下拉菜单](media/how-to-migrate-ssis-job-ssms/step1.png)

1. 将 ssis 包中的 SSIS 包和配置文件的路径映射到迁移的管道可访问的目标路径。 在此映射步骤中，您可以：

    1. 选择源文件夹，然后单击 "**添加映射**"。
    1. 更新源文件夹路径。 有效路径是包的文件夹路径或父文件夹路径。
    1. 更新目标文件夹路径。 默认值是在步骤1中选择的默认存储帐户的相对路径。
    1. 通过**删除映射**删除所选映射。
![步骤 2](media/how-to-migrate-ssis-job-ssms/step2.png)
![步骤 2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. 选择要迁移的适用作业，并配置相应的已*执行 SSIS 包活动*的设置。

    - *默认设置*，默认情况下适用于所有选定的步骤。 有关每个属性的详细信息，请参阅当包位置为*文件系统（包）* 时[执行 SSIS 包活动](how-to-invoke-ssis-package-ssis-activity.md)的 "*设置" 选项卡*。
    ![步骤 3-1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *步骤设置*，配置所选步骤的设置。
        
        **应用默认设置**：选择默认值。 取消选择此选项将仅为所选步骤配置设置。  
        有关其他属性的详细信息，请参阅当包位置为*文件系统（包）* 时[执行 SSIS 包活动](how-to-invoke-ssis-package-ssis-activity.md)的 "*设置" 选项卡*。
    ![步骤 3-2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. 生成并部署 ARM 模板。
    1. 选择或输入已迁移 ADF 管道的 ARM 模板的输出路径。 如果不存在，将自动创建文件夹。
    2. 选择 "将**ARM 模板部署到数据工厂**" 选项：
        - 未选择默认值。 可以在以后手动部署生成的 ARM 模板。
        - 选择此配置可直接将生成的 ARM 模板部署到数据工厂。
    ![步骤 4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. "迁移"，然后检查结果。
![步骤5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>后续步骤

[运行和监视管道](how-to-invoke-ssis-package-ssis-activity.md)
