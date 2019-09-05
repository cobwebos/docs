---
title: Azure Data Box Heavy 订购教程 | Microsoft Docs
description: 了解部署先决条件以及如何订购 Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: dce3549bde4c62245e1d2b1f8ac0c88c0b70260c
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241393"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>教程：订购 Azure Data Box Heavy


Azure Data Box Heavy 是一个混合解决方案，可以快速、方便、可靠地将本地数据导入 Azure。 请先将数据传输到 Microsoft 提供的 770 TB（大致可用容量）存储设备，然后将设备寄回。 然后，此数据将上传到 Azure。

本教程介绍如何订购 Azure Data Box Heavy。 本教程的介绍内容包括：

> [!div class="checklist"]
> * Data Box Heavy 先决条件
> * 订购 Data Box Heavy
> * 跟踪订单
> * 取消订单

## <a name="prerequisites"></a>先决条件

请先完成下述适用于 Data Box 服务和设备的配置先决条件，然后部署设备。

### <a name="for-installation-site"></a>安装场地

在开始之前，请确保：

- 设备可以通过标准的门廊和入口通道。 但是，请确保设备可以通过所有入口通道。 设备规格：宽度26 英寸，长度48 英寸，高度28 英寸。
- 如果在第一层以外的楼层安装设备，必须能够通过升降机或活动梯来接触设备。 设备重约 500 磅。
- 务必在数据中心内的平坦场地上操作，该场地靠近可用的网络连接，并能够放得下 Data Box Heavy 大小的设备。


### <a name="for-service"></a>对于服务

在开始之前，请确保：
- 具有 Microsoft Azure 存储帐户和访问凭据。
- 确保用于 Data Box 服务的订阅是下述类型之一：
    - Microsoft 企业协议 (EA)。 详细了解 [EA 订阅](https://azure.microsoft.com/pricing/enterprise-agreement/)。
    - 云解决方案提供商 (CSP)。 详细了解 [Azure CSP 计划](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)。
    - Microsoft Azure 赞助。 详细了解 [Azure 赞助计划](https://azure.microsoft.com/offers/ms-azr-0036p/)。

- 确保拥有订阅所有者或参与者访问权限，以便创建 Data Box Heavy 订单。

### <a name="for-device"></a>对于设备

在开始之前，请确保：
- 设备已打开包装。
- 应该将一个主机连接到数据中心网络。 Data Box Heavy 将从此计算机复制数据。 主机必须按照 [Azure Data Box Heavy 系统要求](data-box-system-requirements.md)中的说明运行支持的操作系统。
- 需要使用一台配备 RJ-45 线缆的笔记本电脑连接到本地 UI 并配置设备。 使用笔记本电脑配置设备的每个节点一次。
- 数据中心需要有高速网络。 强烈建议你至少建立一个 10 GbE 连接。
- 需要为每个设备节点使用一条 40 Gbps 或 10 Gbps 线缆。 选择与 [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) 网络接口兼容的线缆：

    - 40 Gbps 线缆的设备端需是 QSFP+。
    - 对于 10 Gbps 线缆，需要使用 SFP+ 线缆，其一端插入 10 G 交换机，插入设备的另一端配备 QSFP+ 转 SFP+ 适配器（或 QSA 适配器）。
    - 设备附带了电源线。

## <a name="order-data-box-heavy"></a>订购 Data Box Heavy

在 Azure 门户中执行以下步骤以订购设备。

1. 使用 Microsoft Azure 凭据在以下 URL 登录：[https://portal.azure.com](https://portal.azure.com)。
2. 选择“+ 创建资源”并搜索 *Azure Data Box*。  选择“Azure Data Box”  。
    
   [![搜索“Azure Data Box 1”](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. 选择“创建”  。

4. 检查 Data Box 服务是否在你的区域可用。 输入或选择以下信息，然后选择“应用”。 

    |设置  |值  |
    |---------|---------|
    |Subscription     | 对于 Data Box 服务，选择一个 EA、CSP 或 Azure 赞助订阅。 <br> 订阅链接到计费帐户。       |
    |传输类型     | 选择“导入到 Azure”。         |
    |来源国家/地区     | 选择数据当前所在的国家/地区。         |
    |目标 Azure 区域     | 选择要在其中传输数据的 Azure 区域。        |

    [![选择 Data Box 系列可用性](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. 选择“Data Box Heavy”。  单次订购的最大可用容量为 770 TB。

    [![选择 Data Box Heavy](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. 在“订单”中指定“订单详细信息”。   输入或选择以下信息，然后选择“下一步”。 
    
    |设置  |值  |
    |---------|---------|
    |Name     | 提供友好名称用于跟踪订单。 <br> 名称可以为 3 到 24 个字符，包括字母、数字和连字符。 <br> 名称必须以字母或数字开头和结尾。      |
    |Resource group     | 使用现有资源组，或创建新的资源组。 <br> 资源组是可以统一管理或部署的资源的逻辑容器。         |
    |目标 Azure 区域     | 选择存储帐户的区域。 <br> 有关详细信息，请转到[适用区域](https://azure.microsoft.com/global-infrastructure/services/?products=databox)。        |
    |存储目标     | 从存储帐户和/或托管磁盘中进行选择。 <br> 根据指定的 Azure 区域，从现有存储帐户的筛选列表中选择一个或多个存储帐户。 <br>Data Box Heavy 可以与最多 10 个存储帐户关联。 <br> 也可以创建新的**常规用途 v1**、**常规用途 v2** 或 **Blob 存储帐户**。 <br> 不支持 Azure Data Lake Storage Gen 2 帐户。 请参阅[设备支持的存储帐户](data-box-heavy-system-requirements.md#supported-storage-accounts)。 <br>支持使用虚拟网络的存储帐户。 若要允许 Data Box 服务使用受保护的存储帐户，请在存储帐户网络防火墙设置中启用受信任的服务。 有关详细信息，请了解如何[将 Azure Data Box 服务添加为受信任的服务](../storage/common/storage-network-security.md#exceptions)。|

    如果使用存储帐户作为存储目标，则会看到以下屏幕截图：

    ![存储帐户的 Data Box Heavy 订单](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    如果除了用作存储目标的存储帐户，还使用 Data Box Heavy 从本地 VHD 创建托管磁盘，则需提供以下信息：

    |设置  |值  |
    |---------|---------|
    |资源组     | 若要从本地 VHD 创建托管磁盘，请创建新的资源组。 使用现有资源组的前提是，资源组是在以前由 Data Box 服务为托管磁盘创建 Data Box Heavy 订单时创建的。 <br> 指定多个用分号分隔的资源组。 最多支持 10 个资源组。|

    ![托管磁盘的 Data Box Heavy 订单](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    为托管磁盘指定的存储帐户用作临时存储帐户。 Data Box 服务将 VHD 作为页 Blob 上传到临时存储帐户，然后将其转换为托管磁盘并移到资源组。 有关详细信息，请参阅[验证 Azure 中的数据上传](data-box-deploy-picked-up.md#verify-data-upload-to-azure)。

7. 在“寄送地址”中，提供你的姓名、公司的名称和邮政地址，以及有效的电话号码。  选择“验证地址”。  

    服务将验证寄送地址，以确定服务是否在该区域可用。 如果服务在指定的寄送地址可用，则会将结果通知给你。 选择“**下一步**”。

8. 在“通知详细信息”中，指定电子邮件地址。  服务会将有关任何订单状态更新的电子邮件通知发送到指定的电子邮件地址。

    我们建议使用组电子邮件，以便在组中的管理员离任后，可以持续收到通知。

9. 查看与订单、联系人、通知和隐私条款相关的信息**摘要**。 选中对应于同意隐私条款的复选框。

10. 选择“订单”。  创建订单需要几分钟时间。


## <a name="track-the-order"></a>跟踪订单

下单后，可以从 Azure 门户跟踪订单状态。 转到你的 Data Box Heavy 订单，然后转到“概览”来查看状态。  门户中会显示订单处于“已订购”状态。 

如果设备缺货，你会收到通知。 如果设备有货，Microsoft 会确定要发货的设备，并准备发货。 在设备准备期间，会执行以下操作：

- 将为与设备关联的每个存储帐户创建 SMB 共享。
- 将为每个共享生成访问凭据（例如用户名和密码）。
- 也会生成用于解锁设备的设备密码。
- 锁定 Data Box Heavy 的目的是在任何时候防止对设备进行未经授权的访问。

设备准备完成后，门户会显示订单处于“已处理”状态。 

![处理的 Data Box Heavy 订单](media/data-box-overview/data-box-order-status-processed.png)

然后，Microsoft 会安排区域承运人发运设备。 设备发货后，你会收到跟踪号。 门户会显示订单处于“已发运”状态。 

![派遣的 Data Box Heavy 订单](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>取消订单

若要取消此订单，请在 Azure 门户中转到“概览”，然后在命令栏中单击“取消”。  

下单后，只要订单状态尚未标记为“已处理”，就可以随时取消订单。
 
若要删除已取消的订单，请转到“概况”，然后在命令栏中单击“删除”。  

## <a name="next-steps"></a>后续步骤

本教程介绍了 Azure Data Box Heavy 主题，例如：

> [!div class="checklist"]
> * 先决条件
> * 订购 Data Box Heavy
> * 跟踪订单
> * 取消订单

请继续学习下一教程，了解如何设置 Data Box Heavy。

> [!div class="nextstepaction"]
> [设置 Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
