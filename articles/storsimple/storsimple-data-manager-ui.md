---
title: "Microsoft Azure StorSimple Data Manager UI | Microsoft 文档"
description: "介绍如何使用 StorSimple Data Manager 服务 UI（个人预览版）"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 53a8599df2c647613122cd791b680e2e658586b0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="manage-using-the-storsimple-data-manager-service-ui-private-preview"></a>使用 StorSimple Data Manager 服务 UI（个人预览版）进行管理

本文介绍如何使用 StorSimple Data Manager UI 对驻留在 StorSimple 8000 系列设备上的数据执行数据转换。 转换后的数据然后可供其他 Azure 服务，如 Azure 媒体服务、 Azure HDInsight、 Azure 机器学习中，和 Azure 搜索。 


## <a name="use-storsimple-data-transformation"></a>使用 StorSimple 数据转换

StorSimple Data Manager 是可在其中实例化数据转换的资源。 使用数据转换服务，可以将 StorSimple 本地设备中的数据移到 Azure 存储中的 blob。 因此，需要在工作流中指定有关 StorSimple 设备以及要移到存储帐户中的数据的详细信息。

### <a name="create-a-storsimple-data-manager-service"></a>创建 StorSimple Data Manager 服务

执行以下步骤创建 StorSimple Data Manager 服务。

1. 若要创建 StorSimple Data Manager 服务，请转到 [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager)

2. 单击 **+** 图标，并搜索“StorSimple Data Manager”。 单击 StorSimple Data Manager 服务，并单击“创建”。

3. 如果已启用用于创建此服务的订阅，则将看到以下边栏选项卡。

    ![创建 StorSimple Data Manager 资源](./media/storsimple-data-manager-ui/create-new-data-manager-service.png)

4. 输入相关项，并单击“创建”。 指定的位置应该是存储存储帐户和 StorSimple Manager 服务的位置。 目前，仅支持“美国西部”和“西欧”区域。 因此，StorSimple Manager 服务、 Data Manager 服务和关联的存储帐户都应在上述支持的区域中。 创建用户大约需要一分钟时间。

### <a name="create-a-data-transformation-job-definition"></a>创建数据转换作业定义

在 StorSimple Data Manager 服务中，需要创建数据转换作业定义。 作业定义指定的数据的你感兴趣移动到的本机格式的存储帐户的详细信息。 

执行以下步骤可创建新的数据转换作业定义。

1.  导航到已创建的服务。 单击“+ 作业定义”。

    ![单击“+作业定义”](./media/storsimple-data-manager-ui/click-add-job-definition.png)

2. 此时会打开新作业定义边栏选项卡。 为作业定义指定名称并单击“源”。 在“配置数据源”边栏选项卡，指定 StorSimple 设备的详细信息和所需数据。

    ![创建作业定义](./media/storsimple-data-manager-ui//create-new-job-deifnition.png)

3. 由于这是新的 Data Manager 服务，因此不配置任何数据存储库。 要将 StorSimple Manager 添加为数据存储库，请在数据存储库下拉列表中单击“新增”，然后单击“添加数据存储库”。

4. 选择“StorSimple 8000 系列管理器”作为存储库类型，并输入 **StorSimple Manager** 的属性。 在“资源 Id”字段中，需要输入 StorSimple Manager 的注册密钥中 **:** 之前的数字。

    ![创建数据源](./media/storsimple-data-manager-ui/create-new-data-source.png)

5.  完成后单击“确定”。 这会保存数据存储库，并且此 StorSimple Manager 可以在其他作业定义中重复使用，而无需重新输入这些参数。 单击“确定”后，需要几秒钟时间此 StorSimple Manager 才会显示在下拉列表中。

6.  在**配置数据源**边栏选项卡，输入设备名称和具有你感兴趣的数据的卷名称。

7.  在**筛选器**子节中，输入包含你感兴趣的数据的根目录 (此字段应以开头`\`)。 还可以在此处添加任何文件筛选器。

8.  数据转换服务处理通过快照推送到 Azure 的数据。 运行此作业时，可以选择每次运行此作业时执行备份（以处理最新的数据） 或者使用云中的上次现有备份（如果要处理某些已存档的数据）。

    ![新数据源的详细信息](./media/storsimple-data-manager-ui/new-data-source-details.png)

9. 接下来，需要配置“目标”设置。 支持两种类型的目标 - Azure 存储帐户和 Azure 媒体服务帐户。 选择存储帐户，可将文件放入该帐户的 blob 中。 选择媒体服务帐户，可将文件放入该帐户的资产中。 同样，我们需要添加存储库。 在下拉列表中，选择“新增”，并选择“配置设置”。

    ![创建数据接收器](./media/storsimple-data-manager-ui/create-new-data-sink.png)

10. 在这里，可以选择要添加的存储库类型和与存储库关联的其他参数。 在这两种情况下，在作业运行时，都将创建存储队列。 此队列填充了可以使用的转换后 blob 的相关消息。 此队列的名称与作业定义的名称相同。 如果选择“媒体服务”作为存储库类型，则还可以在创建队列的位置输入存储帐户凭据。

    ![新数据接收器的详细信息](./media/storsimple-data-manager-ui/new-data-sink-details.png)

11. 添加数据存储库（需要几秒钟）后，会在“目标帐户名称”的下拉列表中找到该存储库。  选择所需的目标。

12. 单击“确定”以创建作业定义。 现在，作业定义已设置好。 可以通过 UI 多次使用此作业定义。

    ![添加新作业定义](./media/storsimple-data-manager-ui/add-new-job-definition.png)

### <a name="run-the-job-definition"></a>运行作业定义

每当需要将数据从 StorSimple 移到已在作业定义中指定的存储帐户时，便会需要调用它。 对于每次调用作业时更改参数，有一些灵活性。 步骤如下：

1. 选择 StorSimple Data Manager 服务，并转到“监视”。 单击“立即运行”。

    ![触发作业定义](./media/storsimple-data-manager-ui/run-now.png)

2. 选择要运行的作业定义。 单击“运行设置”，修改可能要为此次作业运行更改的任何设置。

    ![运行作业设置](./media/storsimple-data-manager-ui/run-settings.png)

3. 单击“确定”，并单击“运行”以启动作业。 若要监视此作业，请转到 StorSimple Data Manager 中的“作业”页。

    ![作业列表和状态](./media/storsimple-data-manager-ui/jobs-list-and-status.png)

4. 除了在“作业”边栏选项卡中监视外，还可以侦听存储队列，每次将文件从 StorSimple 移到存储帐户时都会在该队列中添加一条消息。


## <a name="next-steps"></a>后续步骤

[使用 .NET SDK 启动 StorSimple Data Manager 作业](storsimple-data-manager-dotnet-jobs.md)。