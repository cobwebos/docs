---
title: Azure 表 |Azure 应用商店
description: 为 Azure 表配置潜在顾客管理。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dsindona
ms.openlocfilehash: f511a60b533d6d1e0b1ae8847d0ee0fb6be3500c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288829"
---
# <a name="configure-lead-management-using-an-azure-table"></a>使用 Azure 表配置潜在顾客管理

如果合作伙伴中心中没有明确支持客户关系管理 （CRM） 系统以接收 Azure 应用商店和 AppSource 潜在顾客，则可以使用 Azure 表来处理这些潜在顾客。 然后，您可以选择导出数据并将其导入 CRM 系统。 本文中的说明将引导您完成创建 Azure 存储帐户以及该帐户下的 Azure 表的过程。 此外，您可以使用 Microsoft Flow 创建新流，在产品/服务收到潜在顾客时发送电子邮件通知。

## <a name="configure-azure-table"></a>配置 Azure 表

1. 如果没有 Azure 帐户，可以[创建免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。
2. Azure 帐户处于活动状态后，请登录到 Azure[门户](https://portal.azure.com)。
3. 在 Azure 门户中，使用以下过程创建存储帐户。  
    1. 选择 "在左侧菜单栏中**创建资源**"。  "**新建**"窗格（边栏）将向右显示。
    2. 在 **"新建"** 窗格中选择 **"存储**"。  **右侧将显示"精选**"列表。
    3. 选择 **"存储帐户**"以开始创建帐户。  按照文章"[创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)"中的说明进行操作。

        ![创建 Azure 存储帐户的步骤](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png)

        有关存储帐户的详细信息，请选择[快速入门教程](https://docs.microsoft.com/azure/storage/)。  有关存储定价的详细信息，请参阅[存储定价](https://azure.microsoft.com/pricing/details/storage/)。

4. 等待存储帐户预配，此过程通常需要几分钟时间。  然后，通过选择 **"查看所有资源**"或从 Azure 门户的左侧导航菜单栏中选择 **"所有资源**"，从 Azure 门户的**主页**访问存储帐户。

    ![访问 Azure 存储帐户](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png)

5. 从存储帐户窗格中，选择 **"访问密钥"** 并复制密钥的连接*字符串*值。 保存此值，因为这是存储*帐户连接字符串*值，您需要在发布门户中提供该值才能接收市场产品的潜在顾客。 

    连接刺痛的一个示例是：

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

    ![Azure 存储密钥](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png)

6. 从存储帐户窗格中，选择 **"表"** 并选择 **"表**"以创建表。 输入表的名称，然后选择 **"确定**"。 如果要配置 MS Flow 以在收到潜在顾客时接收电子邮件通知，请根据需要保存此值。

    ![Azure 表](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    可以使用 [Azure 存储资源管理器](https://archive.codeplex.com/?p=azurestorageexplorer)或任何其他工具来查看存储表中的数据。 您还可以在 Azure 表中导出数据。 

## <a name="optional-use-microsoft-flow-with-an-azure-table"></a>（可选）将 Microsoft 流与 Azure 表一起使用  

每次将潜在顾客添加到 Azure 表时，都可使用 [Microsoft Flow](https://docs.microsoft.com/flow/) 自动发送通知。 如果您没有帐户，您可以[注册一个免费帐户](https://flow.microsoft.com/)。

### <a name="lead-notification-example"></a>潜在顾客通知示例

使用此示例作为指南，创建一个简单的流，在将新的潜在顾客添加到 Azure 表时自动发送电子邮件通知。 此示例设置一个重复，以便在更新表存储时每小时发送一次潜在顾客信息。

1. 登录到您的 Microsoft 流帐户。
2. 在左侧导航栏中，选择“我的流”****。
3. 在顶部导航栏上，选择“+ 新建”****。  
4. 在下拉列表中，**从空白**中选择 + 计划

   ![我的流 = 计划 - 从空白*](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

5.  在 *"生成"下的计划流*窗口上 *，每个*选择"1"为间隔，"小时"频率。 此外，如果您愿意，请为流指定一个名称。 选择 **“创建”**。

    >[!Note]
    >尽管此示例使用 1 小时间隔，但您可以选择最适合业务需求的间隔和频率。

    ![生成计划流。](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

6. 选择 **= 新步骤**。
7. 在"*选择操作*窗口"搜索"过去时间"，然后选择"在操作下**获取过去时间**"。

   ![选择操作。](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

8. 在“获取过去时间”窗口中，将间隔设置为 1********。 从“时间单位”下拉列表中，选择“小时”********。

    >[!Important]
    >确保此间隔和时间单元与步骤 5 中为"重复"配置的间隔和频率匹配。

    ![设置过去时间间隔](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

>[!TIP] 
>可以随时检查流以验证是否正确配置了每个步骤。 要检查流，请从流菜单栏中选择“流检查器”****。

在下一组步骤中，您将连接到 Azure 表，并设置处理逻辑以处理新潜在顾客。

9. 在"获取过去时间"步骤后，选择 **"新建步骤**"，然后在 *"选择操作*"窗口中搜索"获取实体"。
10. 在 **"操作"** 下，选择**获取实体（Azure 表存储）。**
11. 在**Azure 表存储**窗口中，提供以下字段的信息，然后选择"**创建**：

    * *连接名称*- 为在此流和 Azure 表之间建立的连接提供有意义的名称。
    * *存储帐户名称*- 提供 Azure 表的存储帐户的名称。 您可以在存储帐户的访问**密钥**页中找到此项。
    * *共享存储密钥*- 为 Azure 表的存储帐户提供密钥值。 您可以在存储帐户的访问**密钥**页中找到此项。

        ![Azure 表存储。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

    单击"创建"后，您将看到 *"获取实体"* 窗口。 此处选择 **"显示高级选项**"并提供以下字段的信息：

       * *表*- 选择 Azure 表存储的名称（从有关如何配置 Azure 表的说明的第 6 步）。 下一个屏幕截图显示为此示例选择"市场潜在顾客"表时的提示。

            ![Azure 表获取实体。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

        * *筛选器查询*- 选择此字段并将此函数粘贴到字段中：`Timestamp gt datetime'@{body('Get_past_time')}'`

            ![Azure 表获取实体 - 筛选资源。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

12. 现在，您已完成与 Azure 表的连接设置，请选择 **"新建步骤**"以添加条件以扫描 Azure 表以创建新的潜在顾客。 

13. 在 **"选择操作"** 窗口中，选择 **"操作**"，然后选择 **"条件"控件**。

    ![Azure 表 - 选择操作。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

14. 在“条件”窗口中，选择“选择值”字段，然后在弹出窗口中选择“表达式”************。

15. 将 `length(body('Get_entities')?['value'])` 粘贴到 fx 字段中******。 选择“确定”以添加此功能****。 

16. 要完成条件设置：
    1. 从下拉列表中选择"大于"。
    2. 输入 0 作为值

        ![Azure 表 - 条件。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

在接下来的几个步骤中，您将根据条件的结果设置要执行的操作。

* 如果条件解析为 **"如果否**"，则不要执行任何操作。
* 如果条件解析为“如果是”，则触发连接 Office 365 帐户以发送电子邮件的操作****。 

17. 选择在 **"如果是"** 下**添加操作**。

    ![Azure 表 - 条件，[如果是]。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

18. 选择**发送电子邮件（Office 365 Outlook）。**

    ![Azure 表 - 条件，[如果是]，发送电子邮件。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!Note]
    >如果要使用其他电子邮件提供商搜索，请选择"发送电子邮件通知（邮件）"作为操作。 这些说明将向您展示如何使用 Office 365 Outlook 进行配置，但对于不同的电子邮件提供商，这些说明类似。

19. 在**Office 365 Outlook**窗口中，提供有关以下字段的信息：

    1. 收件人 - 输入将收到此通知的所有人的电子邮件地址****。
    1. **主题**- 为电子邮件提供主题。 例如：新线索！
    1. **正文**- 添加要包含在每封电子邮件中的文本（可选），然后粘贴到正文`body('Get_entities')?['value']`中。

    >[!Note]
    >可将其他静态或动态数据点插入此电子邮件的正文中。

    ![Azure 表 - 条件，[如果是]，Office 365 Outlook 窗口。](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

20. 选择“保存”以保存流****。 Microsoft Flow 将自动测试流中的错误。 如果没有任何错误，则流在保存后开始运行。

下一个屏幕截图显示了最终流应如何显示的示例。

![最终流的一个示例。](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>管理您的流程

运行后管理流非常简单。 可完全控制流。 例如，可以停止它、编辑它、查看运行历史记录，然后获取分析。 下一个屏幕截图显示了可用于管理流的选项。 

 ![管理流](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

流将一直保持运行状态，直到你使用“关闭流量”选项来停止流****。

如果未收到任何潜在顾客电子邮件通知，则意味着尚未将新潜在顾客添加到 Azure 表中。 如果有任何流故障，您将收到一封电子邮件，如下一个屏幕捕获中的示例。

 ![流失败电子邮件通知](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>将产品/服务配置为将潜在顾客发送到 Azure 表

准备好在发布门户中配置产品/服务的首席管理信息时，请按照以下步骤操作：

1. 导航到产品 **/服务"产品/服务"设置**页面。
2. 在"潜在顾客管理"部分下选择 **"连接**"。
3. 在"连接详细信息"弹出窗口上，选择**潜在顾客目标的** **Azure 表**，然后按照前面的步骤粘贴到**存储帐户连接字符串**字段，从创建的 Azure 存储帐户的连接字符串中粘贴。
4. 选择“保存”。**** 

>[!Note]
>您必须完成配置产品/服务的其余部分并发布它，然后才能收到产品/服务的潜在顾客。

生成潜在顾客时，Microsoft 会将潜在顾客发送到 Azure 表。 如果配置了流，也会将电子邮件发送到您配置的电子邮件地址。

![潜在顾客管理](./media/commercial-marketplace-lead-management-instructions-azure-table/lead-management.png)

![潜在顾客管理 - 连接详细信息](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

![潜在顾客管理 - 连接详细信息存储帐户](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

