---
title: Azure 表 | Microsoft Docs
description: 为 Azure 表配置潜在顾客管理。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 60e3e3d81b07bf7ae681b5cef2d6d9681877a35f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805830"
---
<a name="lead-management-instructions-for-azure-table"></a>Azure 表的潜在客户管理说明
============================================

本文介绍如何配置 Azure 表以存储潜在销售顾客。 可以使用 Azure 表存储和自定义客户信息。

## <a name="to-configure-azure-table"></a>配置 Azure 表

1.  如果没有 Azure 帐户，可以[创建免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。

2.  激活 Azure 帐户后，登录到 [Azure 门户](https://portal.azure.com)。
3.  在 Azure 门户中，创建存储帐户。 下一个屏幕截图显示了如何创建存储帐户。 有关存储定价的详细信息，请参阅[存储定价](https://azure.microsoft.com/pricing/details/storage/)。

    ![创建 Azure 存储帐户的步骤](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  复制密钥的存储帐户连接字符串并将其粘贴到云合作伙伴门户的“存储帐户连接字符串”字段。 连接字符串的一个示例是 `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net `
    
    ![Azure 存储密钥](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

可以使用 [Azure 存储资源管理器](http://azurestorageexplorer.codeplex.com/)或任何其他工具来查看存储表中的数据。 此外可以导出 Azure 表中的数据。
数据。

## <a name="optional-to-use-azure-functions-with-an-azure-table"></a>（可选）将 Azure Functions 与 Azure 表配合使用

如果想要自定义如何接收潜在顾客，请将 [Azure Functions](https://azure.microsoft.com/services/functions/) 与 Azure 表配合使用。 使用 Azure Functions 服务，可使你能够自动执行潜在顾客生成过程。

以下步骤说明如何创建使用计时器的 Azure Function。 每隔五分钟函数便会在 Azure 表中查找新记录，然后使用 SendGrid 服务发送电子邮件通知。


1.  在 Azure 订阅中[创建](https://portal.azure.com/#create/SendGrid.SendGrid)免费的 SendGrid 服务帐户。

    ![创建 SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)

2.  创建 SendGrid API 密钥 
    - 选择“管理”以转到 SendGrid UI
    - 选择“设置”、“API 密钥”，然后创建一个具有邮件发送 -\> 完全访问权限的密钥
    - 保存 API 密钥


    ![SendGrid API 密钥](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)


3.  使用名为“消耗计划”的“宿主计划”选项[创建](https://portal.azure.com/#create/Microsoft.FunctionApp)一个 Azure 函数应用。

    ![创建 Azure 函数应用](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)


4.  创建新的函数定义。

    ![创建 Azure 函数定义](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)
 

5.  若要让该函数在特定时间发送更新，请选择“TimerTrigger-CSharp”作为启动器选项。

     ![Azure Function 时间触发器选项](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)


6.  将“开发”代码替换为以下代码示例。 用你想要用于发件人和接收人的地址来编辑电子邮件地址。

        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;
        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }
        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";
            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();
            // Check how many rows were added
            int rowsCount = rowsList.Count;
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");
                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };
                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);
                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }

    ![Azure Function 代码片段](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)


7.  选择“集成”和“输入”，即可定义 Azure 表连接。

    ![Azure Function 集成](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)


8.  输入表名称，并通过选择“新建”来定义连接字符串。


    ![Azure Function 表连接](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9.  现在，将 Output 定义为 SendGrid，并保留所有默认值。

    ![SendGrid 输出](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)

    ![SendGrid 输出默认值](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. 使用名为“SendGridApiKey”和从 SendGrid UI 中的 API 密钥获取的值向 Function App 设置添加 SendGrid API 密钥

    ![SendGrid 管理](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![SendGrid 管理密钥](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

函数配置完成后，“集成”部分中的代码应类似下面的示例。

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }

11. 最后一步是导航到函数的开发 UI，然后选择“运行”以启动计时器。 现在，每当有新的潜在顾客来访时，你都将收到通知。
