---
title: 将本地 SQL 服务器集成服务 （SSIS） 作业迁移到 Azure 数据工厂
description: 本文介绍如何使用 SQL 服务器管理工作室将 SQL 服务器集成服务 （SSIS） 作业迁移到 Azure 数据工厂管道/活动/触发器。
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
ms.openlocfilehash: ee51be1d994c3b81765266e95c48d321a2f43b14
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80989436"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>使用 SSMS 将 SQL 服务器代理作业迁移到 ADF

在迁移 SSIS 包后[，将本地 SQL 服务器集成服务 （SSIS） 工作负载迁移到 ADF 中的 SSIS](scenario-ssis-migration-overview.md)时，可以通过 SQL 服务器管理工作室 （SSMS） **SSIS 作业迁移向导**对 SQL Server 代理作业进行批处理迁移，该作业步骤类型为 SQL Server 集成服务包到 Azure 数据工厂 （ADF） 管道/活动/计划触发器。

通常，对于具有适用作业步骤类型的选定 SQL 代理作业 **，SSIS 作业迁移向导**可以：

- 将本地 SSIS 包位置映射到包迁移到的位置，SSIS 可在 ADF 中访问这些位置。
    > [!NOTE]
    > 仅支持文件系统的包位置。
- 将具有适用作业步骤的适用作业迁移到相应的 ADF 资源，如下所示：

|SQL 代理作业对象  |ADF 资源  |说明|
|---------|---------|---------|
|SQL 代理作业|管道     |将生成管道的名称 *，用于\<作业名称>*。 <br> <br> 内置代理作业不适用： <li> SSIS 服务器维护作业 <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|SSIS 作业步骤|执行 SSIS 包活动|<li> 活动的名称将为\<>步骤名称。 <li> 作业步骤中使用的代理帐户将作为此活动的 Windows 身份验证进行迁移。 <li> 除了在作业步骤中定义的*使用 32 位运行时*之外，*执行选项*将在迁移中忽略。 <li> 在作业步骤中定义的*验证*将在迁移中忽略。|
|schedule      |计划触发器        |计划名称>将*\<生成计划触发器的名称*。 <br> <br> SQL 代理作业计划中的以下选项将在迁移中忽略： <li> 第二级间隔。 <li> *SQL Server 代理启动时自动启动* <li> *CPU 空闲时启动* <li> *工作日和**周末*<time zone> <br> 以下是 SQL 代理作业计划迁移到 ADF 计划触发器后的差异： <li> ADF 计划触发器后续运行独立于前触发运行的执行状态。 <li> ADF 计划触发器定期配置不同于 SQL 代理作业中的每日频率。|

- 在本地输出文件夹中生成 Azure 资源管理器 （ARM） 模板，并直接或以后手动部署到数据工厂。 有关 ADF 资源管理器模板的详细信息，请参阅[Microsoft.DataFactory 资源类型](https://docs.microsoft.com/azure/templates/microso.ft.datafactory/allversions)。

## <a name="prerequisites"></a>先决条件

本文中描述的功能需要 SQL Server 管理工作室版本 18.5 或更高版本。 若要获取 SSMS 最新版本，请参阅[下载 SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)。

## <a name="migrate-ssis-jobs-to-adf"></a>将 SSIS 作业迁移到 ADF

1. 在 SSMS 中，在对象资源管理器中，选择 SQL 服务器代理，选择作业，然后右键单击并选择**将 SSIS 作业迁移到 ADF**。
![菜单](media/how-to-migrate-ssis-job-ssms/menu.png)

1. 登录 Azure，选择 Azure 订阅、数据工厂和集成运行时。 Azure 存储是可选的，如果要迁移的 SSIS 作业具有 SSIS 文件系统包，则在包位置映射步骤中使用 Azure 存储。
![菜单](media/how-to-migrate-ssis-job-ssms/step1.png)

1. 将 SSIS 作业中的 SSIS 包和配置文件的路径映射到迁移管道可以访问的目标路径。 在此映射步骤中，您可以：

    1. 选择源文件夹，然后**添加映射**。
    1. 更新源文件夹路径。 有效路径是包的文件夹路径或父文件夹路径。
    1. 更新目标文件夹路径。 默认值是默认存储帐户的相对路径，在步骤 1 中选择。
    1. 通过**删除映射**删除选定的映射。
![步骤2](media/how-to-migrate-ssis-job-ssms/step2.png)
![步骤2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. 选择要迁移的适用作业，并配置相应*执行 SSIS 包活动的*设置。

    - *默认设置*，默认情况下适用于所有选定的步骤。 有关每个属性的详细信息，请参阅在包位置为*文件系统 （包）* 时[执行 SSIS 包活动的](how-to-invoke-ssis-package-ssis-activity.md)*"设置"选项卡*。
    ![步骤3-1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *步骤设置*，配置所选步骤的设置。
        
        **应用默认设置**：选择默认值。 取消选择以仅配置所选步骤的设置。  
        有关其他属性的详细信息，请参阅包位置为*文件系统 （包）*[时执行 SSIS 包活动的](how-to-invoke-ssis-package-ssis-activity.md)*"设置"选项卡*。
    ![步骤3-2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. 生成和部署 ARM 模板。
    1. 为迁移的 ADF 管道的 ARM 模板选择或输入输出路径。 如果不存在，将自动创建文件夹。
    2. 选择将**ARM 模板部署到数据工厂**的选项：
        - 默认值未选中。 您可以稍后手动部署生成的 ARM 模板。
        - 选择该选择可直接将生成的 ARM 模板部署到数据工厂。
    ![步骤 4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. 迁移，然后检查结果。
![步骤5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>后续步骤

[运行和监视管道](how-to-invoke-ssis-package-ssis-activity.md)
