<properties
   pageTitle="管理 Azure SQL 数据仓库中的可伸缩性任务（Azure 门户）| Azure"
   description="扩大 Azure SQL 数据仓库的性能的 Azure 门户任务。通过调整 DWU 更改计算资源。或者，暂停和恢复计算资源来节省成本。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.date="04/28/2016"
   wacn.date=""/>

# 管理 Azure SQL 数据仓库中的可伸缩性任务（Azure 门户）

> [AZURE.SELECTOR]
- [概述](/documentation/articles/sql-data-warehouse-overview-scalability)
- [门户](/documentation/articles/sql-data-warehouse-manage-scale-out-tasks)
- [PowerShell](/documentation/articles/sql-data-warehouse-manage-scale-out-tasks-powershell)
- [REST](/documentation/articles/sql-data-warehouse-manage-scale-out-tasks-rest-api)
- [TSQL](/documentation/articles/sql-data-warehouse-manage-scale-out-tasks-tsql)


弹性扩大计算资源和内存，以满足不断变化的工作负荷需求，并通过在非高峰时段缩减资源来节约成本。

此任务集合使用 Azure 门户实现：

- 通过调整 DWU 缩放性能
- 暂停计算资源
- 恢复计算资源

若要了解此信息，请参阅[性能可伸缩性概述][]。

<a name="scale-performance-bk"></a>

## 缩放性能

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description（SQL 数据仓库缩放 DWU 说明）](../includes/sql-data-warehouse-scale-dwus-description)]

更改计算资源：

1. 打开“[Azure 门户][]”，打开你的数据库，然后单击“缩放”。

    ![单击“缩放”][1]

1. 在“缩放”边栏选项卡上，向左或向右移动滑块，以更改 DWU 设置。

    ![移动滑块][2]

1. 单击“保存”。将显示确认消息。单击“是”以确认或“否”以取消。

    ![点击“保存”(Save)][3]

<a name="pause-compute-bk"></a>

## 暂停计算

[AZURE.INCLUDE [SQL Data Warehouse pause description（SQL 数据仓库暂停说明）](../includes/sql-data-warehouse-pause-description)]

暂停数据库：

1. 打开“[Azure 门户][]”，并打开你的数据库。请注意，状态为“联机”。 

    ![联机状态][6]

1. 若要挂起计算和内存资源，请单击“暂停”，随后将显示确认消息。单击“是”以确认或“否”以取消。

    ![确认暂停][7]

1. 当 SQL 数据仓库正在启动时，状态将为“正在暂停”。
2. 当状态为“已暂停”时，则暂停操作完成，将停止对你收取 DWU 费用。

    ![暂停状态][4]

<a name="resume-compute-bk"></a>

## 恢复计算

[AZURE.INCLUDE [SQL Data Warehouse resume description（SQL 数据仓库恢复说明）](../includes/sql-data-warehouse-resume-description)]
恢复数据库：

1. 打开“Azure 门户”[][]，并打开你的数据库。请注意，状态为“已暂停”。 

    ![暂停数据库][4]

1. 若要恢复数据库，单击“启动”，随后将显示确认消息。单击“是”以确认或“否”以取消。

    ![确认恢复][5]

1. 当 SQL 数据仓库正在启动时，状态将为“正在恢复”。
2. 当状态为“联机”时，该数据库已就绪。

    ![联机状态][6]

<a name="next-steps-bk"></a>

## 后续步骤
有关详细信息，请参阅[管理概述][]。

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-scale.png
[2]: ./media/sql-data-warehouse-manage-scale-out-tasks/move-slider.png
[3]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-save.png
[4]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-database.png
[5]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-database.png
[7]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-confirm.png

<!--Article references-->
[管理概述]: /documentation/articles/sql-data-warehouse-overview-manage
[性能可伸缩性概述]: /documentation/articles/sql-data-warehouse-overview-scalability

<!--MSDN references-->


<!--Other Web references-->

[Azure 门户]: http://portal.azure.com/

<!---HONumber=Mooncake_0613_2016-->