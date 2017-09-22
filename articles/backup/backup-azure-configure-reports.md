---
title: "为 Azure 备份配置报表"
description: "本文介绍了如何使用恢复服务保管库为 Azure 备份配置 Power BI 报表。"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 86e465f1-8996-4a40-b582-ccf75c58ab87
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/13/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 597c3eb49bc3d4f9b2311f232e4280027636266c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---
# <a name="configure-azure-backup-reports"></a>配置 Azure 备份报表
本文逐步介绍了如何使用恢复服务保管库配置 Azure 备份报表，以及如何使用 Power BI 访问这些报表。 执行这些步骤后，可直接转到 Power BI，以便查看所有报表、自定义和创建报表。 

## <a name="supported-scenarios"></a>支持的方案
1. 如果使用 Azure 恢复服务代理执行 Azure 虚拟机备份和将文件/文件夹备份到云中，支持配置 Azure 备份报表。
2. 暂不支持为 Azure SQL、DPM 和 Azure 备份服务器配置报表。
3. 如果为每个保管库配置同一存储帐户，可以跨保管库和订阅查看报表。 选择的存储帐户应与恢复服务保管库位于同一区域。
4. 在 Power BI 中，报表按计划每 24 小时刷新一次。 还可以在 Power BI 中临时刷新报表，这样客户存储帐户中的最新数据可用于呈现报表。 

## <a name="prerequisites"></a>先决条件
1. 创建 [Azure 存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)，以便为报表配置此帐户。 此存储帐户用于存储报表的相关数据。
2. [创建 Power BI 帐户](https://powerbi.microsoft.com/landing/signin/)，以便可以使用 Power BI 门户查看、自定义并创建自己的报表。
3. 向存储帐户订阅和恢复服务保管库订阅注册资源提供程序 Microsoft.insights（如果尚未注册的话），以便报表数据可以流向存储帐户。 同样，必须依次转到 Azure 门户 >“订阅”>“资源提供程序”，并找到此提供程序进行注册。 

## <a name="configure-storage-account-for-reports"></a>配置报表的存储帐户
请按照下列步骤操作，使用 Azure 门户配置恢复服务保管库的存储帐户。 存储帐户只需配置一次。配置后，便可以直接转到 Power BI，从而查看内容包并利用报表。
1. 如果已打开恢复服务保管库，请转到下一步。 如果未打开恢复服务保管库，而是位于 Azure 门户中，请在“中心”菜单中单击“浏览”。

   * 在资源列表中，键入“恢复服务”。
   * 开始键入时，会根据输入筛选该列表。 出现“恢复服务保管库”时，请单击它。

      ![创建恢复服务保管库步骤 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     此时会显示恢复服务保管库列表。 在恢复服务保管库列表中选择一个保管库。

     此时会打开选定的保管库仪表板。
2. 在保管库下显示的项列表中，单击“监视和报表”部分下的“备份报表”，以配置报表的存储帐户。

      ![选择“备份报表”菜单项 - 第 2 步](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. 在“备份报表”边栏选项卡中，单击“配置”按钮。 此时，“Azure Application Insights”边栏选项卡打开，用于将数据推送到客户存储帐户。

      ![配置存储帐户 - 第 3 步](./media/backup-azure-configure-reports/configure-storage-account.PNG)
4. 将“状态”切换按钮设置为“开”，并选中“存档到存储帐户”复选框，以便报表数据可以开始流向存储帐户。

      ![启用诊断 - 第 4 步](./media/backup-azure-configure-reports/set-status-on.png)
5. 单击“存储帐户”选取器，从列表中选择用于存储报表数据的存储帐户，再单击“确定”。

      ![选择存储帐户 - 第 5 步](./media/backup-azure-configure-reports/select-storage-account.png)
6. 选中“Azure 备份报表”复选框，并移动滑块以选择此报表数据的保持期。 存储帐户中报表数据的保持期即为使用此滑块选择的时间段。

      ![选择存储帐户 - 第 6 步](./media/backup-azure-configure-reports/save-configuration.png)
7. 检查所有更改，再单击顶部的“保存”按钮，如上图所示。 此操作可确保保存所有更改，现在用于存储报表数据的存储帐户已配置完成。

> [!NOTE]
> 通过保存存储帐户配置报表后，为了完成初始数据推送，应等待 24 小时。 应仅在此之后导入 Power BI 中的 Azure 备份内容包。 有关更多详细信息，请参阅[常见问题解答部分](#frequently-asked-questions)。 
>
>

## <a name="view-reports-in-power-bi"></a>在 Power BI 中查看报表 
使用恢复服务保管库配置报表的存储帐户后，报表数据大约需要 24 小时才会开始流向存储帐户。 设置存储帐户 24 小时后，按照下列步骤操作，在 Power BI 中查看报表：
1. [登录](https://powerbi.microsoft.com/landing/signin/) Power BI。
2. 依次单击“获取数据”和“内容包库”中“服务”下的“获取”。 按照 [Power BI 文档中所述步骤操作，访问内容包](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-packs-services/)。

     ![导入内容包](./media/backup-azure-configure-reports/content-pack-import.png)
3. 在搜索栏中键入“Azure 备份”，再单击“立即获取”。

      ![获取内容包](./media/backup-azure-configure-reports/content-pack-get.png)
4. 输入在上面第 5 步中配置的存储帐户名称，再单击“下一步”按钮。

    ![输入存储帐户名称](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. 输入此存储帐户的存储帐户密钥。 可以在 Azure 门户中转到存储帐户，[查看并复制存储访问密钥](../storage/common/storage-create-storage-account.md#manage-your-storage-account)。 

     ![输入存储帐户](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. 单击“登录”按钮。 成功登录后，将看到“正在导入数据”通知。

    ![正在导入内容包](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    一段时间后，导入完成，将看到“成功”通知。 如果存储帐户中的数据很多，内容包导入时间可能会稍长一点。
    
    ![成功导入内容包](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. 成功导入数据后，“Azure 备份”内容包便会显示在导航窗格的“应用程序”内。 此时，列表显示 Azure 备份仪表板、报表和数据集，带黄色星号表示新导入的报表。 

     ![Azure 备份内容包](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. 单击“仪表板”下的“Azure 备份”，显示一组固定的关键报表。

      ![Azure 备份仪表板](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. 若要查看一组完整报表，请单击仪表板中的任意报表。

      ![Azure 备份“作业运行状况”选项卡](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. 单击报表中的各个选项卡，查看相应领域的报表。

      ![Azure 备份报表选项卡](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>常见问题
1. **如何确定报表数据是否已开始流向存储帐户？**
    
    可以转到配置的存储帐户，并选择容器。 如果容器包含 insights-logs-azurebackupreport 条目，则表示报表数据已开始流向存储帐户。

2. **数据多久向存储帐户和 Power BI 中的 Azure 备份内容包推送一次？**

   在用户配置存储帐户当天，数据大约需要 24 小时才能推送到存储帐户。 在这一次初始推送完成后，将按以下频率刷新数据，如下图所示。 
      * 与作业、警报、备份项、保管库、受保护的服务器和策略相关的数据：在客户登录时推送到客户存储帐户。
      * 与存储相关的数据：每 24 小时推送到客户存储帐户一次。
   
    ![Azure 备份报表数据推送频率](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  Power BI [计划每天刷新一次](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed)。 对于内容包，可以在 Power BI 中手动刷新数据。

3. **报表可以保留多长时间？** 

   配置存储帐户时，可以选择存储帐户中报表数据的保持期（通过上面“配置报表的存储帐户”部分中的第 6 步）。 此外，可以[在 Excel 中分析报表](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/)，并保存报表以根据需要延长保持期。 

4. **配置存储帐户后，报表中是否会显示我的所有数据？**

   “配置存储帐户”后生成的所有数据都会推送到存储帐户，并会显示在报表中。 不过，不会为报表推送正在进行的作业。 作业完成或失败后，便会发送到报表。

5. **如果我已配置存储帐户来查看报表，能否将配置更改为使用其他存储帐户？** 

   能，可以将配置更改为指向不同的存储帐户。 连接到 Azure 备份内容包时，应使用新配置的存储帐户。 此外，配置其他存储帐户后，新数据便会流向此存储帐户。 不过，更改配置前的旧数据仍会保留在旧存储帐户中。

6. **能否跨保管库和订阅查看报表？** 

   能，可以跨各种保管库配置同一存储帐户，以便跨保管库查看报表。 此外，还可以跨订阅为保管库配置同一存储帐户。 然后，可以使用此存储帐户在 Power BI 中连接到 Azure 备份内容包，从而查看报表。 不过，选择的存储帐户应与恢复服务保管库位于同一区域。
   
## <a name="troubleshooting-errors"></a>排查错误
| 错误详细信息 | 解决方法 |
| --- | --- |
| 为备份报表设置存储帐户后，“存储帐户”仍显示“未配置”。 | 如果已成功配置存储帐户，报告数据将忽略此问题而流通。 若要解决此问题，请转到“Azure 门户”>“更多服务”>“诊断设置”>“RS 保管库”>“编辑设置”。 删除以前配置的设置，然后从同一边栏选项卡中创建新设置。 此时请将“名称”字段设置为“服务”。 这应会显示配置的存储帐户。 |
|在 Power BI 中导入 Azure 备份内容包后，出现错误“404 - 未找到容器”。 | 如本文档中的建议，在恢复服务保管库中配置报表后，必须等待 24 小时，Power BI 中才会正确显示报表。 如果在 24 小时内尝试访问报表，将收到此错误，因为尚不存在完整的数据，无法显示有效报表。 |

## <a name="next-steps"></a>后续步骤
至此，已配置存储帐户并导入了 Azure 备份内容包，下一步是自定义这些报表，并使用报表数据模型创建报表。 有关详细信息，请参阅以下文章。

* [使用 Azure 备份报表数据模型](backup-azure-reports-data-model.md)
* [在 Power BI 中筛选报表](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [在 Power BI 中创建报表](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)


