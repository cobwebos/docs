---
title: 在 Azure 流分析作业中轮换登录凭据
description: 本文介绍如何在 Azure 流分析作业中更新输入和输出接收器的凭据。
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2018
ms.openlocfilehash: b49b4fecb6be70987e7e6736d78f224c03f719bf
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2018
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>轮转流分析作业的输入和输出的登录凭据

每当重新生成流分析作业的输入或输出凭据时，应使用新的凭据更新作业。 必须在更新凭据前停止作业，不能在作业运行期间替换凭据。 为减少停止和重启作业之间的时滞，流分析支持从上一次输出开始继续执行作业。 本主题介绍轮换登录凭据和使用新凭据重启作业的过程。

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>重新生成新凭据和使用新凭据更新作业 

此部分将介绍如何重新生成 Blob 存储、事件中心、SQL 数据库和表存储的凭据。 

### <a name="blob-storagetable-storage"></a>Blob 存储/表存储
1. 登录 Azure 门户，浏览用作流分析作业输入/输出的存储帐户。    
2. 从设置部分打开“访问密钥”。 在两个默认密钥（key1、key2）中选择作业不使用的那个密钥，并重新生成该密钥：  
   ![重新生成存储帐户的密钥](media/stream-analytics-login-credentials-inputs-outputs/image1.png)
3. 复制新生成的密钥。    
4. 从 Azure 门户中，浏览流分析作业>选择“停止”并等待作业停止。    
5. 找到要更新凭据的 Blob/表存储输入/输出。    
6. 找到“存储帐户密钥”字段，在其中粘贴新生成的密钥，然后单击“保存”。    
7. 保存更改后将自动开始进行连接测试，可在通知选项卡上查看。有两种通知：一种对应于保存更新，另一种对应于测试连接：  
   ![编辑密钥后的通知](media/stream-analytics-login-credentials-inputs-outputs/image4.png)
8. 进行至 [从上次停止的时间启动作业] (#start-your-job-from-the-last-stopped-time) 部分。

### <a name="event-hubs"></a>事件中心

1. 登录 Azure 门户，浏览用作流分析作业输入/输出的事件中心。    
2. 从设置部分中，打开“共享的访问策略”，并选择所需访问策略。 在“主密钥”和“辅助密钥”中，选择作业不使用的那个密钥，并重新生成该密钥：  
   ![重新生成事件中心的密钥](media/stream-analytics-login-credentials-inputs-outputs/image2.png)
3. 复制新生成的密钥。    
4. 从 Azure 门户中，浏览流分析作业>选择“停止”并等待作业停止。    
5. 找到需要更新凭据的事件中心输入/输出。    
6. 找到“事件中心策略密钥”字段，在其中粘贴新生成的密钥，单击“保存”。    
7. 保存所做的更改时，连接测试会自动启动，请确保连接测试成功通过。    
8. 进行至[从上次停止的时间启动作业](#start-your-job-from-the-last-stopped-time)部分。

### <a name="sql-database"></a>SQL 数据库

需要连接到 SQL 数据库以更新现有用户的登录凭据。 可以使用 Azure 门户或客户端工具（如 SQL Server Management Studio）更新凭据。 本部分演示使用 Azure 门户更新凭据的过程。

1. 登录 Azure 门户，浏览用作流分析作业输出的 SQL 数据库。    
2. 从“数据资源管理器”中，登录/连接到数据库 > 选择“SQL server 身份验证”作为“授权类型”> 在“登录”和“密码”中键入相应详细信息>选择“确定”。  
   ![重新生成 SQL 数据库凭据](media/stream-analytics-login-credentials-inputs-outputs/image3.png)

3. 在查询选项卡中，通过运行以下查询（确保使用用户名替换 `<user_name>`，使用新密码替换 `<new_password>`）更改其中一个用户的密码：  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. 记录新的密码。    
5. 从 Azure 门户中，浏览流分析作业>选择“停止”并等待作业停止。    
6. 找到要轮换凭据的 SQL 数据库输出。 更新密码并保存更改。    
7. 保存所做的更改时，连接测试会自动启动，请确保连接测试成功通过。    
8. 进行至[从上次停止的时间启动作业](#start-your-job-from-the-last-stopped-time)部分。

### <a name="power-bi"></a>Power BI
1. 登录 Azure 门户，浏览流分析作业，选择“停止”并等待作业停止。    
2. 找到要续订凭据的 Power BI 输出 > 单击“续订授权”（应会看到成功消息）>“保存”更改。    
3. 保存所做的更改时，连接测试会自动启动，请确保连接测试成功通过。    
4. 进行至[从上次停止的时间启动作业](#start-your-job-from-the-last-stopped-time)部分。

## <a name="start-your-job-from-the-last-stopped-time"></a>从上次停止的时间启动作业

1. 导航到作业的“概述”窗格 > 选择“启动”以启动作业。    
2. 选择“上次停止时”> 单击“启动”。 请注意“上次停止时”选项仅在之前运行过作业且生成了输出的情况下出现。 作业的重启基于上次输出值的时间。
   ![启动作业](media/stream-analytics-login-credentials-inputs-outputs/image5.png)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
