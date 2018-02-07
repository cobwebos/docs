---
title: "Microsoft Azure StorSimple Data Manager UI | Microsoft 文档"
description: "介绍如何使用 StorSimple 数据管理器服务 UI"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: d704cf8e6840c6a7b0a637c404d421f9f1497c46
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2018
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>管理 Azure 门户中的 StorSimple 数据管理器服务

本文介绍如何使用 StorSimple 数据管理器 UI 来转换驻留在 StorSimple 8000 系列设备上的数据。 然后，Azure 媒体服务、Azure HDInsight、Azure 机器学习和 Azure 搜索等其他 Azure 服务可以使用转换的数据。


## <a name="use-storsimple-data-transformation"></a>使用 StorSimple 数据转换

StorSimple 数据管理器是可在其中实例化数据转换的资源。 通过数据转换服务，可将数据从 StorSimple 格式转换为 Blob 或 Azure 文件中的本机格式。 若要转换 StorSimple 本机格式数据，需要指定有关 StorSimple 8000 系列设备以及要转换的目标数据的详细信息。

### <a name="create-a-storsimple-data-manager-service"></a>创建 StorSimple Data Manager 服务

执行以下步骤创建 StorSimple Data Manager 服务。

1. 使用 Microsoft 帐户凭据登录到 [Azure 门户](https://portal.azure.com/)。

2. 单击“+ 创建资源”并搜索 StorSimple 数据管理器。

    ![创建 StorSimple 数据管理器服务 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. 单击“StorSimple 数据管理器”，并单击“创建”。
    
    ![创建 StorSimple 数据管理器服务 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. 对于新的服务，请指定以下内容：

    1. 为 StorSimple 数据管理器提供一个唯一的“服务名称”。 这是可用于标识该服务的友好名称。 名称可以为 3 到 24 个字符，包括字母、数字和连字符。 名称必须以字母或数字开头和结尾。

    2. 从下拉列表中选择一个“订阅”。 订阅链接到计费帐户。 如果你只有一个订阅，则该字段将自动填充（并且不可选）。

    3. 选择现有的资源组，或创建一个新的组。 有关详细信息，请参阅 [Azure 资源组](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/)。

    4. 为存储存储帐户和 StorSimple 数据管理器服务的服务指定“位置”。 StorSimple 设备管理器服务、数据管理器服务和关联的存储帐户都应在支持的区域中。
    
    5. 若要在仪表板上获取此服务的链接，请选择“固定到仪表板”。
    
    6. 单击“创建”。

    ![创建 StorSimple 数据管理器服务 3](./media/storsimple-data-manager-ui/create-service-4.png)

创建服务需要几分钟时间。 成功创建服务并看到新服务后，会显示一条通知。

### <a name="create-a-data-transformation-job-definition"></a>创建数据转换作业定义

在 StorSimple Data Manager 服务中，需要创建数据转换作业定义。 作业定义指定你希望以本机格式移至存储帐户的 StorSimple 数据的详细信息。 创建作业定义后，可使用不同的运行时设置再次运行此作业。

执行以下步骤，创建作业定义。

1. 导航到已创建的服务。 转到“管理”>“作业定义”。

2. 单击“+ 作业定义”。

    ![单击“+作业定义”](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. 为作业定义提供名称。 名称的长度可介于 3 到 63 个字符之间。 名称可包含大小写字母、数字和连字符。

4. 指定作业运行的位置。 此位置可不同于配置服务的位置。

5. 单击“源”，指定源数据存储库。

    ![配置源数据存储库](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. 由于这是新的 Data Manager 服务，因此不配置任何数据存储库。 在“配置数据源”中，指定 StorSimple 8000 系列设备和目标数据的详细信息。

   若要将 StorSimple 设备管理器添加为数据存储库，请在数据存储库下拉列表中单击“新增”，然后单击“添加数据存储库”。

    ![添加新的数据存储库](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
    1. 选择“StorSimple 8000 系列管理器”作为数据存储库类型。
    
    2. 为数据源存储库输入友好名称。
    
    3. 从下拉列表中，选择与 StorSimple 设备管理器服务相关联的订阅。
    
    4. 为“资源”提供 StorSimple 设备管理器的名称。

    5. 输入 StorSimple 设备管理器服务的服务数据加密密钥。 

    ![配置源数据存储库 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

    完成后单击“确定”。 此操作将保存数据存储库。 在其他作业定义中重新使用此 StorSimple 设备管理器，而不必再次输入这些参数。 点击“确定”后，需要几秒钟时间，新创建的源数据存储库才会显示在下拉列表中。

7. 从“数据存储库”的下拉列表中，选择创建的数据存储库。 

    1. 输入包含目标数据的 StorSimple 8000 系列设备的名称。

    2. 指定驻留在具有目标数据的 StorSimple 设备上的卷的名称。

    3. 在“筛选器”子节中，以 \MyRootDirectory\Data 格式输入包含目标数据的根目录。 不支持诸如 \C:\Data 的驱动器号。 还可以在此处添加任何文件筛选器。

    4. 数据转换服务处理通过快照推送到 Azure 的数据。 运行此作业时，可以选择每次运行此作业时执行备份（以处理最新的数据）或者使用云中的上次现有备份（如果要处理某些已存档的数据）。

    5. 单击“确定”。

    ![配置源数据存储库 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. 接下来，需要配置目标数据存储库。 选择存储帐户，可将文件放入该帐户的 blob 中。 在下拉列表中，选择“新增”，并选择“配置设置”。

9. 选择要添加的存储库类型和与存储库关联的其他参数。

    如果选择存储帐户类型目标，可指定友好名称、订阅（选择与服务相同的订阅或其他）以及存储帐户。
        ![配置目标数据存储库 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    作业运行时，将创建存储队列。 此队列填充了可以使用的转换后 blob 的相关消息。 此队列的名称与作业定义的名称相同。
    
10. 添加数据存储库后，请等候几分钟。
    
    1. 从“目标帐户名称”的下拉列表中选择创建的存储库作为目标。

    2. 选择 blob 或文件作为存储类型。 指定已转换的数据所在的存储容器的名称。 单击“确定”。

        ![配置目标数据存储库存储帐户](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. 还可以选中在运行作业前显示预估作业持续时间的选项。 单击“确定”以创建作业定义。 现在，作业定义已完成。 通过 UI 可使用不同的运行时设置多次使用此作业定义。

    ![完成作业定义](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    新创建的作业定义被添加到该服务的作业定义列表中。

### <a name="run-the-job-definition"></a>运行作业定义

每当需要将数据从 StorSimple 移到已在作业定义中指定的存储帐户时，需要运行它。 在运行时，可以用不同的方式指定某些参数。 步骤如下：

1. 选择 StorSimple 数据管理器服务，然后转到“管理”>“作业定义”。 选择并单击要运行的作业定义。
     
     ![启动作业运行 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. 单击“立即运行”。
     
     ![启动作业运行 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. 单击“运行设置”，修改可能要为此次作业运行更改的任何设置。 单击“确定”，并单击“运行”以启动作业。

    ![启动作业运行 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. 若要监视此作业，请转到 StorSimple 数据管理器中的“作业”。 除了在“作业”边栏选项卡中监视外，还可以侦听存储队列，每次将文件从 StorSimple 移到存储帐户时都会在该队列中添加一条消息。

    ![启动作业运行 4](./media/storsimple-data-manager-ui/start-job-run4.png)


## <a name="next-steps"></a>后续步骤

[使用 .NET SDK 启动 StorSimple Data Manager 作业](storsimple-data-manager-dotnet-jobs.md)。