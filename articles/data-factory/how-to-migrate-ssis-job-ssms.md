---
title: 将本地 SQL Server Integration Services (SSIS) 作业迁移到 Azure 数据工厂
description: 本文介绍如何使用 SQL Server Management Studio 将 SQL Server Integration Services (SSIS) 作业迁移到 Azure 数据工厂管道/活动/触发器。
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
ms.openlocfilehash: 6b95162d34b706b0bbb3e2940ea214e5a662655d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984901"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>使用 SSMS 将 SQL Server 代理作业迁移到 ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

迁移 SSIS 包后，在[将本地 SQL Server Integration Services (SSIS) 工作负载迁移到 ADF 中的 SSIS](scenario-ssis-migration-overview.md) 时，可以通过 SQL Server Management Studio (SSMS) SSIS 作业迁移向导将作业步骤类型为“SQL Server Integration Services 包”的 SQL Server 代理作业批量迁移到 Azure 数据工厂 (ADF) 管道/活动/计划触发器****。

一般情况下，对于作业步骤类型适当的选定 SQL 代理作业，SSIS 作业迁移向导可以执行以下操作****：

- 将本地 SSIS 包位置映射到包所迁移到的、可由 ADF 中的 SSIS 访问的位置。
    > [!NOTE]
    > 仅支持文件系统的包位置。
- 按如下所示将具有适用作业步骤的适用作业迁移到相应的 ADF 资源：

|SQL 代理作业对象  |ADF 资源  |注释|
|---------|---------|---------|
|SQL 代理作业|管道     |将为 \<job name> 生成管道名称**。 <br> <br> 内置代理作业不适用： <li> SSIS 服务器维护作业 <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|SSIS 作业步骤|执行 SSIS 包活动|<li> 活动名称将是 \<step name>。 <li> 在作业步骤中使用的代理帐户将作为此活动的 Windows 身份验证进行迁移。 <li> 在迁移中将忽略作业步骤中定义的除“使用 32 位运行时”以外的执行选项** **。 <li> 在迁移中将忽略作业步骤中定义的验证**。|
|schedule      |计划触发器        |将为 \<schedule name> 生成计划触发器的名称**。 <br> <br> 在迁移中将忽略 SQL 代理作业计划中的以下选项： <li> 二级间隔。 <li> *SQL Server 代理启动时自动启动* <li> *CPU 空闲时启动* <li> 工作日和周末** ** <time zone> <br> 下面是将 SQL 代理作业计划迁移到 ADF 计划触发器后的差异： <li> ADF 计划触发器后续运行独立于先前已触发的运行的执行状态。 <li> ADF 计划触发器重复周期配置不同于 SQL 代理作业中的每日频率。|

- 在本地输出文件夹中生成 Azure 资源管理器 (ARM) 模板，并直接部署到数据工厂，或在以后手动进行部署。 有关 ADF 资源管理器模板的详细信息，请参阅 [Microsoft.DataFactory 资源类型](https://docs.microsoft.com/azure/templates/microsoft.datafactory/allversions)。

## <a name="prerequisites"></a>先决条件

本文介绍的功能需要 SQL Server Management Studio 18.5 或更高版本。 若要获取 SSMS 最新版本，请参阅[下载 SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)。

## <a name="migrate-ssis-jobs-to-adf"></a>将 SSIS 作业迁移到 ADF

1. 在 SSMS 的对象资源管理器中依次选择“SQL Server 代理”、“作业”，然后右键单击并选择“将 SSIS 作业迁移到 ADF”****。
![屏幕截图显示 SQL Server Management Studio 对象资源管理器，你可以在其中选择作业，然后将作业迁移到 D F。](media/how-to-migrate-ssis-job-ssms/menu.png)

1. 登录到 Azure，依次选择“Azure 订阅”、“数据工厂”、“Integration Runtime”。 “Azure 存储”为可选。如果要迁移的 SSIS 作业具有 SSIS 文件系统包，在包位置映射步骤中将使用 Azure 存储。
![菜单](media/how-to-migrate-ssis-job-ssms/step1.png)

1. 将 SSIS 作业中的 SSIS 包和配置文件的路径映射到已迁移管道可以访问的目标路径。 在此映射步骤中，可以执行以下操作：

    1. 选择一个源文件夹，然后选择“添加映射”****。
    1. 更新源文件夹路径。 有效的路径是包的文件夹路径或父文件夹路径。
    1. 更新目标文件夹路径。 默认路径是在步骤 1 中选择的默认存储帐户的相对路径。
    1. 通过“删除映射”删除选定的映射****。
![屏幕截图显示 "映射 S s 包和配置路径" 页，您可以在其中添加映射。 ](media/how-to-migrate-ssis-job-ssms/step2.png)
 ![屏幕截图显示 "映射 S s 包和配置路径" 页，您可以在其中更新源和目标文件夹路径。](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. 选择要迁移的适用作业，并配置相应的“已执行的 SSIS 包活动”的设置**。

    - 默认设置：在默认情况下应用到所有选定步骤**。 有关每个属性的详细信息，请查看当包位置为“文件系统(包)”时与“[执行 SSIS 包活](how-to-invoke-ssis-package-ssis-activity.md)动”操作对应的“设置”选项卡** **。
    ![屏幕截图显示了 "选择我的工作" 页，您可以在其中配置相应的已执行 SSIS 包活动的设置。](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - 步骤设置：配置选定步骤的设置**。
        
        **应用默认设置**：默认已选中。 如果取消选择此选项，则只配置选定步骤的设置。  
        有关其他属性的详细信息，请查看当包位置为“文件系统(包)”时与“[执行 SSIS 包活](how-to-invoke-ssis-package-ssis-activity.md)动”操作对应的“设置”选项卡** **。
    ![屏幕截图显示了 "选择我的作业" 页面，您可以在其中应用默认设置。](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. 生成并部署 ARM 模板。
    1. 选择或输入已迁移的 ADF 管道的 ARM 模板的输出路径。 系统会自动创建文件夹（如果不存在）。
    2. 选择选项“将 ARM 模板部署到数据工厂”****：
        - 默认未选中。 以后可以手动部署生成的 ARM 模板。
        - 选择将生成的 ARM 模板直接部署到数据工厂。
    ![屏幕截图显示 "配置迁移" 页，你可以在其中选择或输入已迁移 ADF 管道的 ARM 模板的输出路径，并选择 "将 ARM 模板部署到你的数据工厂" 选项。](media/how-to-migrate-ssis-job-ssms/step4.png)

1. 迁移，然后检查结果。
![屏幕截图显示 "迁移结果" 页面，该页面显示迁移的进度。](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>后续步骤

[运行和监视管道](how-to-invoke-ssis-package-ssis-activity.md)
