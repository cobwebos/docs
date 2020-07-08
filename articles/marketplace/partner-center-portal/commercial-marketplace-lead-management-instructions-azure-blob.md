---
title: 使用 Azure Blob 存储进行潜在顾客管理 - Microsoft 商业市场
description: 了解如何使用 Azure Blob 为 Microsoft AppSource 和 Azure 市场配置潜在顾客
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: dsindona
ms.openlocfilehash: 471675de58161fc0c0110e2968089da062c6da87
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85960556"
---
# <a name="use-azure-blob-storage-to-manage-commercial-marketplace-leads"></a>使用 Azure Blob 存储管理商业市场潜在顾客

>[!Caution]
>Azure Blob 存储的商业市场支持已弃用，不再是处理产品/服务潜在顾客的一种选项。 如果你当前所拥有商业市场产品/服务的潜在顾客管理是针对 Azure Blob 配置的，你将无法再收到潜在顾客。 请将你的潜在顾客管理配置更新为任何其他潜在顾客管理选项。 有关其他选项，请参阅[潜在顾客管理登陆页面](./commercial-marketplace-get-customer-leads.md)。

 如果合作伙伴中心未明确支持你的客户关系管理 (CRM) 系统接收 Microsoft AppSource 和 Azure 市场潜在顾客，则你可以使用 Azure Blob 存储。 然后，可以选择导出数据并将其导入 CRM 系统。 本文中的说明将指导你完成创建 Azure 存储帐户以及该帐户下 blob 的过程。 此外，还可以使用 Power Automate 创建一个新流，以便在你的产品/服务收到潜在顾客时发送电子邮件通知。

>[!NOTE]
>这些说明中使用的 Power Automate 连接器需要付费 Power Automate 订阅。 在按照本文中的说明进行操作之前，请务必考虑到这一点。

## <a name="configure-azure-blob-storage"></a>配置 Azure Blob 存储

1. 如果没有 Azure 帐户，可以[创建免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。

2. 激活 Azure 帐户后，登录到 [Azure 门户](https://portal.azure.com)。

3. 在 Azure 门户中，使用以下过程创建存储帐户。  
    1. 选择左侧菜单栏中的“+ 创建资源”。  右侧将显示“新建”窗格（边栏选项卡）。
    2. 在“新建”窗格中选择“存储” 。  右侧将显示“精选”列表。
    3. 选择“存储帐户”开始创建帐户。  按照[创建存储帐户](../../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)一文中的说明进行操作。

    ![创建 Azure 存储帐户的步骤](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-create.png)

    有关存储帐户的详细信息，请参阅此[快速入门](../../storage/blobs/storage-quickstart-blobs-portal.md)。  有关存储定价的详细信息，请参阅[存储定价](https://azure.microsoft.com/pricing/details/storage/)。

4. 等待存储帐户预配完成，该过程通常需要几分钟时间。  然后从 Azure 门户的“主页”页面访问你的存储帐户，方法是选择“查看所有资源”或选择 Azure 门户左侧导航菜单栏中的“所有资源”  。

    ![访问 Azure 存储帐户](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-access.png)

5. 从“存储帐户”窗格中，选择“访问密钥”，然后复制该密钥的“连接字符串”值。 保存此值，因为它是接收市场产品/服务潜在顾客时需要在发布门户中提供的“存储帐户连接字符串”值。

     连接字符串的一个示例是：

     ```sql
     DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
     ```

    ![Azure 存储密钥](./media/commercial-marketplace-lead-management-instructions-azure-blob/azure-storage-keys-2.png)

6. 从“存储帐户”页面上，选择 blob。

   ![Azure 存储密钥](./media/commercial-marketplace-lead-management-instructions-azure-blob/select-blobs.png)

7. 在 blob 页上，选择“+ 容器”按钮。

8. 键入新容器的“名称”。 容器名称必须小写，必须以字母或数字开头，并且只能包含字母、数字和短划线 (-) 字符。 有关容器名称和 Blob 名称的详细信息，请参阅 [Naming and referencing containers, blobs, and metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)（命名和引用容器、Blob 和元数据）。

    保存此值，因为它是接收市场产品/服务潜在顾客时需要在发布门户中提供的“容器名称”值。

9. 将容器的公共访问级别设置为“专用(无匿名访问)”。

10. 选择“确定”创建容器。

    ![新建容器](./media/commercial-marketplace-lead-management-instructions-azure-blob/new-container.png)

## <a name="configure-your-offer-to-send-leads-to-azure-blob-storage"></a>配置你的产品/服务以将潜在顾客发送到 Azure Blob 存储

准备好在发布门户中为你的产品/服务配置潜在顾客管理信息后，按以下步骤进行操作：

1. 导航到你产品/服务的“产品/服务设置”页面。
2. 在“潜在顾客”部分下，选择“连接” 。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-azure-blob/customer-leads.png" alt-text="潜在顾客":::

3. 在“连接详细信息”弹出窗口中，为“潜在顾客目标”选择 Azure Blob。

    ![连接详细信息](./media/commercial-marketplace-lead-management-instructions-azure-blob/connect-details.png) 

4. 提供根据这些说明获得的“容器名称”和“存储帐户连接字符串” 。

    * 容器名称示例：`marketplaceleadcontainer`
    * 存储帐户连接字符串示例：`DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net`![连接详细信息](./media/commercial-marketplace-lead-management-instructions-azure-blob/connection-details.png) 

5. 选择“保存”。

    > [!NOTE]
    > 必须先完成产品/服务其余内容的配置并进行发布，然后才能接收产品/服务的潜在顾客。


