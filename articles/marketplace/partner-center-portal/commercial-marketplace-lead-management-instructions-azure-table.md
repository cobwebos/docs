---
title: 使用 Azure 表存储进行潜在顾客管理 - Microsoft 商业市场
description: 了解如何使用 Azure 表存储为 Microsoft AppSource 和 Azure 市场配置潜在顾客
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 08/25/2020
ms.openlocfilehash: 2dca0ae02f2d079e98b51e1222114db1f2104b96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90030791"
---
# <a name="use-azure-table-storage-to-manage-commercial-marketplace-leads"></a>使用 Azure 表存储管理商业市场潜在顾客

如果合作伙伴中心未明确支持你的客户关系管理 (CRM) 系统接收 Microsoft AppSource 和 Azure 市场潜在顾客，则可使用 Azure 表存储来处理这些潜在顾客。 然后，可以选择导出数据并将其导入 CRM 系统。 本文介绍如何在该帐户下创建 Azure 存储帐户和表。 此外，还可以使用 Power Automate 在产品/服务收到潜在客户时发送电子邮件通知，从而创建新的流。

## <a name="configure-an-azure-storage-account"></a>配置 Azure 存储帐户

1. 如果没有 Azure 帐户，可以[创建免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。
1. 激活 Azure 帐户后，登录 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户中，按照以下过程创建存储帐户：

    1. 选择左侧菜单中的“+ 创建资源”。 “新建”窗格随即在右侧显示。
    1. 在“新建”窗格中选择“存储” 。 “特别推荐”列表随即在右侧显示。
    1. 选择“存储帐户”开始创建帐户。 按照[创建存储帐户](../../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)中的说明操作。

        :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-create.png" alt-text="创建 Azure 存储帐户的步骤。":::

        有关存储帐户的详细信息，请参阅[快速入门教程](../../storage/index.yml)。 有关存储定价的详细信息，请参阅[存储定价](https://azure.microsoft.com/pricing/details/storage/)。

1. 等待存储帐户预配完成。 此过程通常需要几分钟时间。

## <a name="create-a-table-in-your-storage-account"></a>在存储帐户中创建表

1. 在 Azure 门户的 " **主页** " 页上，选择 " **查看所有资源** " 以访问存储帐户。 也可以从 Azure 门户的左侧菜单栏中选择“所有资源”。

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-access.png" alt-text="创建 Azure 存储帐户的步骤。":::

1. 从“存储帐户”窗格中，选择“访问密钥”，然后复制该密钥的“连接字符串”值 。 保存此值，因为它是需要在发布门户中提供的“存储帐户连接字符串”值，需要提供该值才可接收 Azure 市场产品/服务的潜在顾客。

    下面是连接字符串的示例。

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.screens.net
    ```

    :::image type="content" source="media/commercial-marketplace-lead-management-instructions-azure-table/azure-storage-keys.png" alt-text="创建 Azure 存储帐户的步骤。":::


1. 从“存储帐户”窗格中，选择“表”，然后选择“+ 表”以创建表 。 输入表的名称，然后选择“确定”。 请保存此值，因为如果要将流配置为在收到潜在顾客时接收电子邮件通知，则需要此值。

    ![Azure 表](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-tables.png)

    可以使用 [Azure 存储资源管理器](https://archive.codeplex.com/?p=azurestorageexplorer)或任何其他工具来查看存储表中的数据。 还可以导出 Azure 表中的数据。

## <a name="optional-use-power-automate-to-get-lead-notifications"></a>（可选）使用 Power Automate 获取潜在顾客通知

每次将潜在顾客添加到 Azure 存储表时，都可使用 [Power Automate](https://docs.microsoft.com/flow/) 自动获取通知。 如果没有帐户，可以[注册免费帐户](https://flow.microsoft.com/)。

### <a name="lead-notification-example"></a>潜在顾客通知示例

该示例创建了一个流，在将新的潜在顾客添加到 Azure 存储表时自动发送电子邮件通知。 此示例设置了重复周期，以在更新表存储时每小时发送一次潜在顾客信息。

1. 登录 Power Automate 帐户。
1. 在左侧栏中，选择“我的流”。
1. 在顶部栏中选择“+ 新建”。
1. 在下拉列表中，选择“+ 计划 - 从空白开始”。

   ![我的流 + 计划 - 从空白开始](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-scheduled-from-blank.png)

1. 在“生成计划流”窗口中，对于“重复频率”，选择“1”作为间隔，选择“小时”作为频率   。 另外，如果需要，请为流命名。 选择“创建”。

   >[!NOTE]
   >虽然此示例使用 1 小时的间隔，但你可以选择最适合业务需求的间隔和频率。

   ![生成计划流](./media/commercial-marketplace-lead-management-instructions-azure-table/build-scheduled-flow.png)

1. 选择“+新建步骤”。
1. 在“选择操作”窗口中，搜索“获取过去的时间” 。 然后在“操作”下，选择“获取过去的时间” 。

   ![选择操作](./media/commercial-marketplace-lead-management-instructions-azure-table/choose-an-action.png)

1. 在“获取过去的时间”窗口中，将“间隔”设置为“1”  。 从“时间单位”下拉列表中，选择“小时” 。

    >[!IMPORTANT]
    >确保在步骤 8 中发送的间隔和时间单位与在步骤 5 中配置的重复间隔和频率匹配。

    ![设置过去的时间间隔](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-getpast-time.png)

   >[!TIP]
   >可以随时检查流以验证是否正确配置了每个步骤。 要检查流，请从“流”菜单栏中选择“流检查器” 。

   下一组步骤中，将连接到表，并设置处理逻辑以处理新的潜在顾客。

1. 选择“+新建步骤”。 然后在“选择操作”窗口中搜索“获取实体” 。
1. 在“操作”下，选择“获取实体(Azure 表存储)” 。
1. 在“Azure 表存储”窗口中，提供以下框的信息并选择“创建” ：

    * **连接名称**：为要在此流和表之间建立的连接提供有意义的名称。
    * **存储帐户名称**：提供表的存储帐户名称。 可以在存储帐户的“访问密钥”页上找到此名称。
    * **共享存储密钥**：为表的存储帐户提供密钥值。 可以在存储帐户的“访问密钥”页上找到此值。

      ![Azure 表存储窗口](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-storage.png)

   选择“创建”后，“获取实体”窗口随即显示 。 在此处，选择“显示高级选项”，并提供以下框的信息：

   * **Table**：选择表的名称（从[创建表](#create-a-table-in-your-storage-account)中）。 下图显示了在 `marketplaceleads` 此示例中选择了 "表" 时的提示。

     ![获取实体窗口](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities.png)

   * **筛选查询**：选择此框，并将以下函数粘贴到框中：`Timestamp gt datetime'@{body('Get_past_time')}'`

     ![获取实体，筛选查询框](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-get-entities-filter-query.png)

1. 现在，你已经完成了 Azure 表的连接设置，请选择“新建步骤”以添加条件，通过扫描 Azure 表获取新潜在顾客。

1. 在“选择操作”窗口中，选择“操作” 。 然后选择“条件控制”。

    ![选择操作窗口](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-choose-an-action.png)

1. 在“条件”窗口中，选择“选择值” 。 然后在弹出窗口中选择“表达式”。

1. 将 `length(body('Get_entities')?['value'])` 粘贴到 fx 框中。 选择“确定”以添加此功能。

1. 要完成条件设置：
    1. 从下拉列表中选择“大于”。
    2. 输入“0”作为值。

        ![条件窗口](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition.png)

   在接下来的几个步骤中，将根据条件的结果设置要采取的操作：

   * 如果条件解析为“如果否”，则不执行任何操作。
   * 如果为 **"是"**，则会触发连接工作或学校帐户以发送电子邮件的操作。 

1. 选择“如果是”下的“添加操作” 。

    ![条件窗口，如果是，添加操作](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes.png)

1. 选择“发送电子邮件(Office 365 Outlook)”。

    ![条件窗口，如果是，发送电子邮件](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-send-email.png)

    >[!NOTE]
    >要使用其他电子邮件提供程序，请搜索并改为选择“发送电子邮件通知(邮件)”作为操作。 这些说明显示了如何使用 Office 365 Outlook 进行配置，但对于不同的电子邮件提供程序，说明是类似的。

1. 在 Office 365 Outlook 窗口中，提供以下框的信息：

    1. **收件人**：输入将收到此通知的所有人的电子邮件地址。
    1. **使用者**：提供电子邮件的主题。 例如，新潜在顾客！
    1. **正文**：添加要包含在每封电子邮件中的文本（可选），然后粘贴到 `body('Get_entities')?['value']` 中。

    >[!NOTE]
    >可将其他静态或动态数据点插入此电子邮件的正文中。

    ![条件窗口，如果是，Office 365 Outlook 窗口](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-condition-if-yes-outlook.png)

1. 选择“保存”以保存流。 Power Automate 可自动测试流是否存在错误。 如果没有任何错误，流将在保存后开始运行。

下图显示了最终流的外观示例。

![最终流的示例](./media/commercial-marketplace-lead-management-instructions-azure-table/final-flow.png)

### <a name="manage-your-flow"></a>管理流

在流运行后管理流十分容易。 可完全控制流。 例如，可以停止它、编辑它、查看运行历史记录，然后获取分析。 下图显示了可用于管理流的选项。

 ![管理流](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-manage-completed.png)

流将一直保持运行状态，直到你使用“关闭流量”选项来停止流。

若未收到任何潜在顾客电子邮件通知，则表示新的潜在顾客尚未添加到 Azure 表中。 若有任何流故障，将收到类似此示例的电子邮件。

 ![流失败电子邮件通知](./media/commercial-marketplace-lead-management-instructions-azure-table/ms-flow-failure-note.png)

## <a name="configure-your-offer-to-send-leads-to-the-azure-table"></a>将你的产品/服务配置为将潜在顾客发送到 Azure 表

准备好在发布门户中为产品/服务配置潜在顾客管理信息后，执行以下步骤。

1. 转到产品/服务的“产品/服务设置”页。

1. 在“潜在顾客”部分下，选择“连接” 。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-table/customer-leads.png" alt-text="创建 Azure 存储帐户的步骤。":::

1. 在“连接详细信息”弹出窗口中，为“潜在顾客目标”选择“Azure 表”  。 
     ![潜在顾客管理，连接详细信息](./media/commercial-marketplace-lead-management-instructions-azure-table/connection-details.png)

1. 在“存储帐户连接字符串”框中，粘贴按照之前的步骤创建的 Azure 存储帐户的连接字符串。
     ![潜在顾客管理，连接详细信息存储帐户](./media/commercial-marketplace-lead-management-instructions-azure-table/azure-table-connection-details.png)

1. **联系人电子邮件**：为公司中应在收到新潜在顾客时收到电子邮件通知的人员提供邮件。 可以提供多个电子邮件，用分号分隔。

1. 选择“确定”。

要确保已成功连接到潜在顾客目标，请选择“验证”按钮。 如果成功，则在潜在顾客目标中会出现测试潜在顾客。

>[!NOTE]
>必须先完成产品/服务的其余内容的配置并进行发布，然后才能接收产品/服务的潜在顾客。

生成潜在顾客时，Microsoft 会将潜在顾客发送到 Azure 表中。 如果你配置了流，则还会向配置的电子邮件地址发送电子邮件。
