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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 2a8ae3ab71b258d92d9761cc813b168717e44d82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61066405"
---
# <a name="lead-management-instructions-for-azure-table"></a>Azure 表的潜在客户管理说明

本文介绍如何配置 Azure 表以存储潜在销售顾客。 可以使用 Azure 表存储和自定义客户信息。

## <a name="to-configure-azure-table"></a>配置 Azure 表

1.  如果没有 Azure 帐户，可以[创建免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。

2.  激活 Azure 帐户后，登录到 [Azure 门户](https://portal.azure.com)。
3.  在 Azure 门户中，创建存储帐户。 下一个屏幕截图显示了如何创建存储帐户。 有关存储定价的详细信息，请参阅[存储定价](https://azure.microsoft.com/pricing/details/storage/)。

    ![创建 Azure 存储帐户的步骤](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  复制密钥的存储帐户连接字符串并将其粘贴到云合作伙伴门户的“存储帐户连接字符串”字段。 连接字符串的一个示例是 `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net`
    
    ![Azure 存储密钥](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

可以使用 [Azure 存储资源管理器](https://azurestorageexplorer.codeplex.com/)或任何其他工具来查看存储表中的数据。 此外可以导出 Azure 表中的数据。
数据。

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>（可选）将 Microsoft Flow 与 Azure 表配合使用

每次将潜在顾客添加到 Azure 表时，都可使用 [Microsoft Flow](https://docs.microsoft.com/flow/) 自动发送通知。 如果没有帐户，可以[注册免费帐户](https://flow.microsoft.com/)。

### <a name="lead-notification-example"></a>潜在顾客通知示例

使用此示例作为指南，创建一个简单的流，在将新的潜在顾客添加到 Azure 表时自动发送电子邮件通知。 此示例设置重复周期，以在更新表存储时每一小时发送一次潜在顾客信息。

1. 登录 Microsoft Flow 帐户。
2. 在左侧导航栏中，选择“我的流”。
3. 在顶部导航栏上，选择“+ 新建”。  
4. 在下拉列表中，选择“+ 从头开始创建”
5. 在“从头开始创建流”下，选择“从头开始创建”。

   ![从头开始创建新的流](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

6. 在连接器和触发器搜索页面上，选择“触发器”。
7. 在“触发器”下，选择“重复周期”。
8. 在“重复周期”窗口中，为间隔保留默认设置 1。 在“频率”下拉列表中，选择“小时”。

   >[!NOTE] 
   >虽然此示例使用 1 小时间隔，但你可以选择最适合业务需求的间隔和频率。

   ![将重复周期频率设定为 1 小时](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

9. 选择“+新建步骤”。
10. 搜索“获取过去时间”，然后在“操作”下选择“获取过去时间”。 

    ![查找并选择“获取过去时间”操作](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

11. 在“获取过去时间”窗口中，将间隔设置为 1。  从“时间单位”下拉列表中，选择“小时”。
    >[!IMPORTANT] 
    >确保此“间隔”和“时间”单位与为“重复周期”配置的“间隔”和“频率”匹配。

    ![设置过去时间间隔](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >可以随时检查流以验证是否正确配置了每个步骤。 要检查流，请从流菜单栏中选择“流检查器”。

下一组步骤中，将连接到 Azure 表，并设置处理逻辑以处理新的潜在顾客。

1. 在执行“获取过去时间”步骤后，选择“+ 新建步骤”，然后搜索“获取实体”。
2. 在“操作”下，选择“获取实体”，然后选择“显示高级选项”。
3. 在“获取实体”窗口中，提供以下字段的信息：

   - 表 - 输入 Azure 表存储的名称。 下一个屏幕截图显示了为此示例输入“MarketPlaceLeads”时的提示。 

     ![为 Azure 表名选择自定义值](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - 筛选查询 - 单击此字段，弹出窗口中将显示“获取过去时间”图标。 选择“过去时间”，将其用作时间戳以筛选查询。 此外，也可以将此功能粘贴到以下字段：`gt datetime'@{body('Get_past_time')}'`

     ![设置筛选查询功能](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

4. 选择“新建步骤”可以添加条件，通过扫描 Azure 表获取新潜在顾客。

   ![使用“新建”步骤添加条件以扫描 Azure 表](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

5. 在“选择操作”窗口中，选择“操作”，然后选择“条件”控件。

     ![添加条件控件](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

6. 在“条件”窗口中，选择“选择值”字段，然后在弹出窗口中选择“表达式”。
7. 将 `length(body('Get_entities')?['value'])` 粘贴到 fx 字段中。 选择“确定”以添加此功能。 要完成条件设置：

   - 从下拉列表中选择“大于”。
   - 输入 0 作为值 

     ![在条件中添加一个函数](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

8. 根据条件的结果设置要采取的操作。

     ![根据条件结果设置操作](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

9. 如果条件解析为“如果否”，则不执行任何操作。 
10. 如果条件解析为“如果是”，则触发连接 Office 365 帐户以发送电子邮件的操作。 选择“添加操作”。
11. 选择“发送电子邮件”。 
12. 在“发送电子邮件”窗口中，提供以下字段的信息：

    - 收件人 - 输入将收到此通知的所有人的电子邮件地址。
    - 主题 - 提供电子邮件的主题。 例如：新潜在顾客！
    - **正文**： 添加要包含在每封电子邮件中的文本（可选），然后将 `('Get_entities')?['value']` 作为插入潜在顾客信息的函数粘贴到正文。

      >[!NOTE] 
      >可将其他静态或动态数据点插入此电子邮件的正文中。

       ![设置潜在顾客通知的电子邮件](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

13. 选择“保存”以保存流。 Microsoft Flow 将自动测试流中的错误。 如果没有任何错误，流将在保存后开始运行。

下一个屏幕截图显示了最终流应如何显示的示例。

 ![最终流序列](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

### <a name="managing-your-flow"></a>管理流

在流运行后管理流十分容易。  可完全控制流。 例如，可以停止它、编辑它、查看运行历史记录，然后获取分析。 下一个屏幕截图显示了可用于管理流的选项。 

 ![管理流](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

流将一直保持运行状态，直到你使用“关闭流量”选项来停止流。

若未收到任何潜在顾客电子邮件通知，则表示新的潜在顾客尚未添加到 Azure 表中。 若有任何流故障，将收到类似下一个屏幕截图中的示例的电子邮件。

 ![流失败电子邮件通知](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>后续步骤

[配置潜在客户](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)