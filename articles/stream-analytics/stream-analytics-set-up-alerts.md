<properties 
	pageTitle="流分析警报 | Microsoft Azure" 
	description="了解流分析警报" 
	keywords="大数据分析、云服务中、物联网、托管服务、流处理、流分析、流数据"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.date="09/29/2015" 
	wacn.date=""/>

# 设置警报

## “监视”页

可以设置一条规则，在指标达到指定条件时触发警报。

例如，“如果最近 15 分钟的输出事件数 <100，则向电子邮件 ID：xyz@company.com” 发送电子邮件通知”。

可以通过门户依据度量值设置规则，也可以依据操作日志数据[通过编程方式](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)进行配置。

## 通过 Azure 门户设置警报

可在 Azure 管理门户中通过两种方式设置警报：

1.	流分析作业的“监视”选项卡  
2.	管理服务中的“操作日志”  

## 通过门户中作业的“监视”选项卡设置警报

1.	在“监视”选项卡中选择指标，再单击仪表板底部的“添加规则”按钮，然后设置你的规则。  

    ![仪表板](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)

2.	定义警报的名称和描述

    ![创建规则](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)

3.	输入阈值、警报评估窗口和针对警报的操作

    ![定义条件](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)

## 通过操作日志设置警报

1.	在 [Azure 门户](https://manage.windowsazure.com)中的“管理服务”中，转到“警报”选项卡。  
2.	单击“添加规则”  

    ![条件](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)

3.	定义警报的名称和描述。选择“流分析”作为服务类型，然后选择作业名称作为服务名称。

    ![定义警报](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)

## 获取帮助
如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/zh-CN/home?forum=AzureStreamAnalytics)

## 后续步骤

- [Azure 流分析简介](/documentation/articles/stream-analytics-introduction)
- [Azure 流分析入门](/documentation/articles/stream-analytics-get-started)
- [缩放 Azure 流分析作业](/documentation/articles/stream-analytics-scale-jobs)
- [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=82-->