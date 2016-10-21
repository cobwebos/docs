<properties
	pageTitle="如何将逻辑应用迁移到架构版本 2015 年 8 月 1 日预览版 | Microsoft Azure App Service"
	description="可以轻松地将逻辑应用迁移到最新架构版本。只需执行以下步骤。"
	services="logic-apps"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/23/2016"
	ms.author="deonhe"/>

# 如何将逻辑应用迁移到架构版本 2015 年 8 月 1 日预览版

若要将现有逻辑应用移动到新架构，请执行以下操作：
1. 在 Azure 门户中，打开你的逻辑应用
2. 单击“更新架构”：

 ![API 图标][step1]“更新架构”页中显示和提供的链接指向提供有关新架构中改进详细信息的文档：![API 图标][step2]

>[AZURE.NOTE] 选择“更新架构”时，自动运行迁移步骤，并为用户提供代码输出。可以使用此代码更新你的定义，不过必须确保遵循良好的编码做法（例如，以下**最佳做法**部分中所述的编码做法）。

## 将逻辑应用迁移到最新架构版本时的最佳做法：  

- 将迁移脚本复制到新的逻辑应用 - 不要覆盖旧的逻辑应用，直到完成测试并确认已迁移的应用能够按预期运行。
- 在投入生产**之前**，测试逻辑应用
- 迁移完成后，在可能的情况下开始更新逻辑应用，使用[托管 API](./apis-list.md)。例如，可以在使用 DropBox v1 的任意位置开始使用 Dropbox v2。


## 后续步骤
-  [了解如何手动迁移逻辑应用](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png

<!---HONumber=AcomDC_0921_2016-->