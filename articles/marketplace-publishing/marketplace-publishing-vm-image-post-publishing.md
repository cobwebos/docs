---
title: "管理你在 Azure 应用商店上的虚拟机映像 | Microsoft Docs"
description: "有关如何在初始发布后管理你在 Azure 应用商店上的虚拟机映像的详细指南。"
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: ed2921750f93f344a4c3dbef31d9f523dedc0aae


---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>有关 Azure 应用商店中的虚拟机产品/服务的发布后指南
本文介绍如何在 Azure 应用商店中更新实时虚拟机产品/服务。 它还指导你完成向现有产品/服务添加一个或多个新 SKU 以及从 Azure 应用商店中删除实时虚拟机产品/服务或 SKU 的过程。

在产品/服务和 SKU 暂存在 [Azure 门户](http://portal.azure.com)中后，你无法更改下面的给定字段：

* **产品/服务标识符：**[发布门户 -> 虚拟机 -> 选择产品/服务 -> VM 映像选项卡 -> 产品/服务标识符]
* **SKU 标识符：**[发布门户 -> 虚拟机 -> 选择产品/服务 -> SKU选项卡 -> 添加 SKU]
* **发布者命名空间：**[发布门户 -> 虚拟机 -> 演练选项卡 -> 告诉我们你公司的情况（可在“步骤 2 注册你的公司”下找到）-> 发布者命名空间 -> 命名空间]

在产品/服务和 SKU 在 [Azure 应用商店](http://azure.microsoft.com/marketplace)中列出后，你无法更改下面的给定字段：

* **产品/服务标识符：**[发布门户 -> 虚拟机 -> 选择产品/服务 -> VM 映像选项卡 -> 产品/服务标识符]
* **SKU 标识符：**[发布门户 -> 虚拟机 -> 选择产品/服务 -> SKU选项卡 -> 添加 SKU]
* **发布者命名空间：**[发布门户 -> 虚拟机 -> 演练选项卡 -> 告诉我们你公司的情况（可在“步骤 2 注册”下找到）-> 发布者命名空间 -> 命名空间]
* **端口** [发布门户 -> 虚拟机 -> 选择产品/服务 -> VM 映像选项卡 -> 打开端口]
* **已列出的 SKU 的定价更改**
* **已列出的 SKU 的计费模型更改**
* **已列出的 SKU 的计费区域删除**
* **已列出的 SKU 的数据磁盘计数变更**

## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1.如何更新 SKU 的技术详细信息
按照如下步骤操作，可将新版本添加到已列出的 SKU 并重新发布你的产品/服务：

1. 登录[发布门户](https://publish.windowsazure.com)。
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧菜单中，单击“VM 映像”选项卡。
4. 从“VM 映像”选项卡的“SKU”部分中，找到要更新的 SKU。
5. 然后，添加新的 SKU 版本号并单击“+”按钮。 新版本格式应为 X.Y.Z，其中 X、Y、Z 为整数。 版本只应进行增量更改。
6. 在“OS VHD URL”框中，添加为操作系统 VHD 创建的共享访问签名 URI 并保存所做的更改。
   
   > [!IMPORTANT]
   > 无法递增/递减已列出的 SKU 的数据磁盘计数。 在此情况下，需要创建新的 SKU。 有关详细指南，请参阅部分 [3.如何在已列出的产品/服务下添加新的 SKU](#3-how-to-add-a-new-sku-under-a-live-offer)。
   > 
   > 
7. 进行更改后，导航到“发布”选项卡并单击“推送到过渡”按钮。 有关在过渡环境中测试产品/服务的详细指南，请参阅此[链接](marketplace-publishing-vm-image-test-in-staging.md)
8. 在过渡环境中测试你的产品/服务后，导航到发布门户中的“发布”选项卡，并单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布你的产品/服务。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2.如何更新产品/服务或 SKU 的非技术详细信息
你可以更新 Azure 应用商店中实时产品/服务或 SKU 的非技术（市场营销、法律、支持、类别）详细信息。

### <a name="21-update-the-offer-description-and-logos"></a>2.1 更新产品/服务说明和徽标
按照如下步骤操作，可更新产品/服务详细信息并重新发布你的产品/服务：

1. 登录[发布门户](https://publish.windowsazure.com)。
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧菜单中，单击“市场营销”选项卡。
4. 单击“英语(美国)”按钮。
5. 在左侧菜单中，单击“详细信息”选项卡。 在“详细信息”选项卡的“说明”部分下，你可以更新产品/服务标题、产品/服务摘要、产品/服务详细摘要并保存所做的更改。
   
   > [!NOTE]
   > 更新 SKU 详细信息时，请注意以下事项。
   > **不要在产品/服务说明和 SKU 说明下输入重复文本。不要在 SKU 标题和产品/服务详细摘要下输入重复文本。不要在 SKU 标题和产品/服务摘要下输入重复文本。**
   > 
   > 
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. 在“详细信息”选项卡的“徽标”部分下，你可以更新徽标。 但是，确保徽标遵循 [Azure 应用商店准则](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)（请参阅部分步骤 1：提供应用商店市场营销内容 -> 详细信息 -> Azure 应用商店徽标准则）。
   
   > [!NOTE]
   > 特大图标是可选的。 你可以选择不上传特大图标。 但是，上传特大图标后，无法通过任何预配将其从发布门户中删除。 在此情况下，必须遵循[特大图标准则](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)（请参阅部分步骤1：提供应用商店市场营销内容 -> 详细信息 -> 特大徽标横幅的其他准则）。
   > 
   > 
7. 进行更改后，导航到“发布”选项卡并单击“推送到过渡”按钮。 有关在过渡环境中测试产品/服务的详细指南，请参阅此[链接](marketplace-publishing-vm-image-test-in-staging.md)。
8. 在过渡环境中测试你的产品/服务后，导航到发布门户中的“发布”选项卡，并单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布你的产品/服务。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2. 更新 SKU 说明
按照如下步骤操作，可更新 SKU 详细信息并重新发布你的产品/服务：

1. 登录[发布门户](https://publish.windowsazure.com)
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧菜单中，单击“市场营销”选项卡。
4. 单击“英语(美国)”按钮。
5. 在左侧菜单中，单击“计划”选项卡。 在“计划”选项卡的“SKU”部分下，你可以更新 SKU 标题、SKU 摘要和 SKU 说明详细信息并保存所做的更改。
   
   > [!NOTE]
   > 更新 SKU 详细信息时，请注意以下事项。 **不要在产品/服务说明和 SKU 说明下输入重复文本。不要在 SKU 标题和产品/服务详细摘要下输入重复文本。不要在 SKU 标题和产品/服务摘要下输入重复文本。**
   > 
   > 
6. 进行更改后，导航到“发布”选项卡并单击“推送到过渡”按钮。 有关在过渡环境中测试产品/服务的详细指南，请参阅此链接
7. 在过渡环境中测试你的产品/服务后，导航到发布门户中的“发布”选项卡，并单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布你的产品/服务。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3 更改现有链接或添加新链接
按照如下步骤操作，可更改现有链接或添加新链接，然后重新发布你的产品/服务：

1. 登录[发布门户](https://publish.windowsazure.com)
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧菜单中，单击“市场营销”选项卡。
4. 单击“英语(美国)”按钮。
5. 在左侧菜单中，单击“链接”选项卡。
6. 如果想要添加新链接，则在“链接”部分下单击“添加链接”按钮。 “添加链接”对话框将打开。 在此对话框中，你可以添加链接标题和 URL 字段并保存所做的更改。 你可以输入任何包含可帮助客户的信息的链接。
7. 如果你想要更新或删除现有链接，选择适当的链接并相应地单击编辑按钮或删除按钮。
   
   > [!NOTE]
   > 请确保已在本部分中输入的链接可工作正常，因为在生产请求过程中，将对这些链接进行验证。
   > 
   > 
8. 进行更改后，导航到“发布”选项卡并单击“推送到过渡”按钮。 有关在过渡环境中测试产品/服务的详细指南，请参阅此[链接](marketplace-publishing-vm-image-test-in-staging.md)。
9. 在过渡环境中测试你的产品/服务后，导航到发布门户中的“发布”选项卡，并单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布你的产品/服务。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2.4 更改现有示例图像或添加新示例图像
按照如下步骤操作，可更改现有示例图像或添加新示例图像，然后重新发布你的产品/服务：

> [!NOTE]
> 仅在 [https://portal.azure.com](https://portal.azure.com) 中显示一张示例图像。
> 
> 

1. 登录[发布门户](https://publish.windowsazure.com)
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧菜单中，单击“市场营销”选项卡。
4. 单击“英语(美国)”按钮。
5. 在左侧菜单中，单击“示例图像”选项卡。
6. 如果想要添加新的示例图像，在“示例图像”部分下单击“上传新图像”按钮，然后保存所做的更改。
   
   > [!NOTE]
   > 包括示例图像是一个可选步骤。
   > 
   > 
7. 如果想要更新或删除现有示例图像，找到适当的示例图像，然后相应地单击“替换图像”按钮或删除按钮。
8. 进行更改后，导航到“发布”选项卡并单击“推送到过渡”按钮。 有关在过渡环境中测试产品/服务的详细指南，请参阅此[链接](marketplace-publishing-vm-image-test-in-staging.md)。
9. 在过渡环境中测试你的产品/服务后，导航到发布门户中的“发布”选项卡，并单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布你的产品/服务。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2.5 更新法律内容
按照如下步骤操作，可更新法律内容并重新发布你的产品/服务：

1. 登录[发布门户](https://publish.windowsazure.com)
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧菜单中，单击“市场营销”选项卡。
4. 单击“英语(美国)”按钮。
5. 在左侧菜单中，单击“法律”选项卡。 在“法律”部分下，可以更新你的策略/使用条款。 在“使用条款”文本框中输入或粘贴策略/条款并保存所做的更改。
6. 法律使用条款的字符数限制为 1,000,000 个字符。
7. 进行更改后，导航到“发布”选项卡并单击“推送到过渡”按钮。 有关在过渡环境中测试产品/服务的详细指南，请参阅此[链接](marketplace-publishing-vm-image-test-in-staging.md)
8. 在过渡环境中测试你的产品/服务后，导航到发布门户中的“发布”选项卡，并单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布你的产品/服务。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6 更新支持信息
按照如下步骤操作，可更新支持信息并重新发布你的产品/服务：

1. 登录[发布门户](https://publish.windowsazure.com)
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧菜单中，单击“支持”选项卡。
4. 在“支持”选项卡的“工程联系人”部分下，可以更新联系人详细信息。 这些详细信息仅用于合作伙伴和 Microsoft 之间的内部通信。
5. 在“支持”选项卡的“客户支持”部分下，可以更新支持联系人详细信息，如“姓名、电子邮件、电话”和“支持 URL”。 这些详细信息仅用于合作伙伴和 Microsoft 之间的内部通信。
   
   > [!NOTE]
   > 如果只想提供电子邮件支持，在“客户支持”部分下提供虚拟电话号码。 在此情况下，将改用你提供的电子邮件。
   > 
   > 
6. 进行更改后，导航到“发布”选项卡并单击“推送到过渡”按钮。 有关在过渡环境中测试产品/服务的详细指南，请参阅此[链接](marketplace-publishing-vm-image-test-in-staging.md)
7. 在过渡环境中测试你的产品/服务后，导航到发布门户中的“发布”选项卡，并单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布你的产品/服务。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7 更新类别
按照如下步骤操作，可更新产品/服务的类别部分并重新发布你的产品/服务：

1. 登录[发布门户](https://publish.windowsazure.com)
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧菜单中，单击“类别”选项卡。
4. 在“类别”部分下，可以更新产品/服务类别并保存所做的更改。 你可以为 Azure 应用商店库选择最多五个类别。
5. 进行更改后，导航到“发布”选项卡并单击“推送到过渡”按钮。 有关在过渡环境中测试产品/服务的详细指南，请参阅此[链接](marketplace-publishing-vm-image-test-in-staging.md)
6. 在过渡环境中测试你的产品/服务后，导航到发布门户中的“发布”选项卡，并单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布你的产品/服务。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>3.如何在已列出的产品/服务下添加新 SKU
按照如下步骤操作，可在实时产品/服务下添加新 SKU：

1. 登录[发布门户](https://publish.windowsazure.com)。
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧菜单中，单击“SKU”选项卡。 然后，单击“添加 SKU”按钮。  将打开一个新对话框。 以小写形式输入 SKU 标识符。 如果想要使用 BYOL 计费模型发布新 SKU，请选中自带计费模型 (BYOL) 复选框。 否则，取消选中 BYOL 复选框。 然后，单击对话框中的勾选标记以创建新 SKU。 如果未为新 SKU 选择 BYOL 计费模型，则新 SKU 的计费模型将自动设置为“每小时”。 如果你想要为每小时计费模型启用 30 天免费试用，针对“是否提供免费试用？”单击“一个月”选项。 否则，选择“无试用”。 [注意：仅在 创建新 SKU 时未在对话框中选择 BYOL 的情况下，才显示选项“是否提供免费试用？”。]
   
   > [!IMPORTANT]
   > 仅在批准在 Azure 应用商店中发布解决方案模板产品/服务的情况下，才可将“针对应用商店隐藏此 SKU，因为它始终应通过解决方案模板购买”选项标记为“是”。 否则，此选项应始终标记为“否”。
   > 
   > 
4. 现在在左侧菜单中，单击“VM 映像”选项卡，并找到已创建的新 SKU。
5. 若要设置新 SKU，请参阅此[链接](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image)中的步骤 5 以获得指南。
6. 若要添加新 SKU 的市场营销材料，请参阅部分步骤 1：提供应用商店市场营销内容 -> 详细信息 -> 此[链接](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)中的第 2 条到第 5 条。
7. 若要添加新 SKU 的定价信息，请参阅部分 2.1. 设置 VM 价格（位于此[链接](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)中）
8. 进行更改后，导航到“发布”选项卡并单击“推送到过渡”按钮。 有关在过渡环境中测试产品/服务的详细指南，请参阅此[链接](marketplace-publishing-vm-image-test-in-staging.md)
9. 在过渡环境中测试你的产品/服务后，导航到发布门户中的“发布”选项卡，并单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布你的产品/服务。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4.如何更改已列出的 SKU 的数据磁盘计数
无法递增/递减已列出的 SKU 的数据磁盘计数。 在此情况下，需要创建新的 SKU。 有关详细指南，请参阅部分 [3.如何在实时产品/服务下添加新的 SKU](#3-how-to-add-a-new-sku-under-a-live-offer)。

## <a name="5-how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5.  如何从 Azure 应用商店中删除已列出的产品/服务
请求删除实时产品/服务时，需要注意以下方面。 请按照以下步骤从支持团队获取有关从 Azure 应用商店中删除已列出的产品/服务的指南：

1. 使用此[链接](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681)生成支持票证
2. 请选择“管理产品/服务”作为问题类型，并选择“修改已在生产中的产品/服务和/或 SKU”作为类别
3. 提交请求

支持团队将指导你完成产品/服务和 SKU 删除过程。

> [!NOTE]
> 你始终可以通过单击“历史记录”选项卡下的“放弃草稿”按钮，删除处于“草稿”状态下（即未在“过渡”或“生产”状态下）的产品/服务。
> 
> 

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6.如何从 Azure 应用商店中删除已列出的 SKU
按照如下步骤操作，可从 Azure 应用商店中删除已列出的 SKU：

1. 登录[发布门户](https://publish.windowsazure.com)。
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧窗格中，单击“SKU”选项卡。
4. 选择想要删除的 SKU，然后针对该 SKU 单击删除按钮。
5. 完成后，导航到发布门户中的“发布”选项卡并单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布产品/服务。
6. 在 Azure 应用商店中重新发布产品/服务后，SKU 将从 Azure 应用商店和 Azure 门户中删除。

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7.如何从 Azure 应用商店中删除已列出的 SKU 的当前版本
按照如下步骤操作，可从 Azure 应用商店中删除已列出的 SKU 的当前版本。 完成该过程后，SKU 将回滚到其以前的版本。

1. 登录[发布门户](https://publish.windowsazure.com)。
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧窗格中，单击“VM 映像”选项卡。
4. 选择想要删除其当前版本的 SKU，然后针对该版本单击删除按钮。
5. 完成后，导航到发布门户中的“发布”选项卡并单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布产品/服务。
6. 在 Azure 应用商店中重新发布产品/服务后，已列出的 SKU 的当前版本将从 Azure 应用商店和 Azure 门户中删除。 SKU 将回滚到其以前的版本。

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8.如何将上市价格还原为生产值
我已更改已列出的 SKU 的定价（或已删除已列出的 SKU 的计费区域）。 由于该价格在 Azure 应用商店中不受支持，因此我想要将我所做的更改还原到生产值。 如何实现此目的？

请按照如下步骤操作：

1. 登录[发布门户](https://publish.windowsazure.com)。
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧菜单中，单击“定价”选项卡。
4. 在“定价”选项卡下，选择你想要重置其定价的区域。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. 对于采用每小时计费模型的 SKU，可以重置所有核心的价格，因为它们针对所选区域处于生产状态下。 对于采用 BYOL 计费模型的 SKU，通过针对“外部许可 (BYOL) SKU 可用性”部分下的 SKU 选中相应复选框，以使 SKU 在该区域中可用（请参阅下面的屏幕截图）。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. 现在，单击“根据美国价格对其他市场自动定价”按钮。
   
   > [!NOTE]
   > 按钮的标签可能因你选择的区域有所不同。 由于在创建此文档时，我们选择了美国，因此在下面的屏幕截图中按钮标记为“根据美国价格对其他市场自动定价”。
   > 
   > 
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. 自动定价向导将打开。 第一页显示基本市场的选择。 进行选择，并通过单击“->”按钮移动到下一页。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. 用于选择核心和计划的选项将显示在第 2 页上。 选择所需的计划和核心，然后单击“->”按钮。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. 第 3 页显示市场/区域。 单击“全部切换”按钮选择所有区域，也可以手动选中区域对应的框。 单击“->”按钮转到下一页。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. 第 4 页显示汇率。 单击“完成”按钮完成步骤。 向导将根据你的选择重置定价。
11. 现在导航到“定价”选项卡，然后单击“查看摘要和更改”按钮。
    选择“查看版本”部分中的“草稿”和“比较”部分中的“生产”（请参阅下面的屏幕截图）。 如果看不到任何定价区别，则表示定价已成功还原到生产值。
    
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. 进行更改后，导航到“发布”选项卡并单击“推送到过渡”按钮。 有关在过渡环境中测试产品/服务的详细指南，请参阅此[链接](marketplace-publishing-vm-image-test-in-staging.md)
13. 在过渡环境中测试你的产品/服务后，导航到发布门户中的“发布”选项卡并单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布你的产品/服务。

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9.如何将计费模型还原为生产值
我已更改已列出的 SKU 的计费模型。 由于该价格在 Azure 应用商店中不受支持，因此我想要将我所做的更改还原到生产值。 如何实现此目的？

请执行以下步骤：

1. 登录[发布门户](https://publish.windowsazure.com)。
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧菜单中，单击“SKU”选项卡。
4. 单击“编辑”按钮以还原计费模型。 将打开一个窗口。 相应地选中或取消选中“计费和许可从 Azure 外部完成(也称为自带许可)”复选框。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. 完成后，请参考本文档中问题 8 的回答，以还原回定价。
6. 然后，导航到发布门户的“发布”选项卡，并将产品/服务推送到过渡环境以进行测试。 完成产品/服务测试后，单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布你的产品/服务。

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10.如何将已列出的 SKU 的可见性设置还原为生产值
请执行以下步骤：

1. 登录[发布门户](https://publish.windowsazure.com)。
2. 导航到“虚拟机”选项卡并选择你的产品/服务。
3. 在左侧菜单中，单击“SKU”选项卡。
4. 选择 SKU，并将该 SKU 的可见性设置还原为生产值。
   
    ![绘制](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. 完成更改后，单击“请求批准推送到生产”按钮，以在 Azure 应用商店中重新发布你的产品/服务。

## <a name="see-also"></a>另请参阅
* [入门教程：如何将供应项发布到 Azure 应用商店](marketplace-publishing-getting-started.md)
* [了解卖方洞察报告](marketplace-publishing-report-seller-insights.md)
* [了解付款报告](marketplace-publishing-report-payout.md)
* [如何更改云解决方案提供商经销商激励](marketplace-publishing-csp-incentive.md)
* [应用商店中的常见发布问题疑难解答](marketplace-publishing-support-common-issues.md)
* [作为发布者获取支持](marketplace-publishing-get-publisher-support.md)
* [本地创建 VM 映像](marketplace-publishing-vm-image-creation-on-premise.md)
* [在 Azure 预览门户中创建运行 Windows 的虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Nov16_HO3-->


