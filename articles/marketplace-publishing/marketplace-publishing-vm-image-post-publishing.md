---
title: "管理 Azure Marketplace 中的虚拟机映像 | Microsoft Docs"
description: "有关如何在初始发布后管理 Azure Marketplace 中的虚拟机映像的详细指南"
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
ms.translationtype: HT
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: e1f90650e71345957c2d353774cb8bef62c1868b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017

---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>有关 Azure Marketplace 中的虚拟机产品/服务的发布后指南
本文介绍如何在 Azure Marketplace 中更新实时虚拟机产品。 其中将指导你完成将一个或多个新 SKU 添加到现有产品的过程。 此外，还将引导你完成删除从 Marketplace 的实时虚拟机产品或 SKU 的过程。

在产品和 SKU 暂存在 [Azure 门户](http://portal.azure.com)中后，无法更改以下文本框：

* **产品标识符：**在发布门户中，转到“虚拟机”并选择产品。 然后单击“VM 映像” > “产品标识符”。
* **SKU 标识符：**在发布门户中，转到“虚拟机”并选择产品。 然后单击“SKU” > “添加 SKU”。
* **发布者命名空间：**在发布门户中，转到“虚拟机” > “演练” > “告诉我们你公司的情况”（可在“步骤 2 注册你的公司”下找到）>“发布者命名空间” > “命名空间”。

在产品和 SKU 列出 [Marketplace](http://azure.microsoft.com/marketplace) 后，无法更改以下文本框：

* **产品标识符：**在发布门户中，转到“虚拟机”并选择产品。 然后单击“VM 映像” > “产品标识符”。
* **SKU 标识符：**在发布门户中，转到“虚拟机”并选择产品。 然后单击“SKU” > “添加 SKU”。
* **发布者命名空间：**在发布门户中，转到“虚拟机” > “演练” > “告诉我们你公司的情况”（可在“步骤 2 注册”下找到）>“发布者命名空间” > “命名空间”。
* **端口：**在发布门户中，转到“虚拟机”并选择产品。 然后单击“VM 映像” > “打开端口”。
* **已列出的 SKU 的定价更改**
* **已列出的 SKU 的计费模型更改**
* **已列出的 SKU 的计费区域删除**
* **已列出的 SKU 的数据磁盘计数变更**

## <a name="update-the-technical-details-of-a-sku"></a>更新 SKU 的技术详细信息
若要将新版本添加到已列出的 SKU 并重新发布产品，请执行以下步骤：

1. 登录到[发布门户](https://publish.windowsazure.com)。
2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧菜单中，单击“VM 映像”选项卡。
4. 在“SKU”部分中，找到要更新的 SKU。
5. 添加新的 SKU 版本号并单击 **+** 按钮。 新版本格式应为 X.Y.Z，其中 X、Y、Z 为整数。 版本只应进行增量更改。
6. 在“OS VHD URL”框中，输入为操作系统 VHD 创建的共享访问签名 URI 并保存所做的更改。

   > [!IMPORTANT]
   > 无法递增/递减已列出的 SKU 的数据磁盘计数。 在此情况下，需要创建新的 SKU。 有关详细指导，请参阅[在已列出的产品下添加新 SKU](#add-a-new-sku-under-a-listed-offer) 部分。
   >
   >
7. 转到“发布”选项卡，然后单击“推送到过渡环境”。 有关在过渡环境中测试产品的详细指导，请参阅[测试 Marketplace 的 VM 产品](marketplace-publishing-vm-image-test-in-staging.md)。
8. 在过渡环境中测试产品后，导航到发布门户中的“发布”选项卡。 单击“请求批准推送到生产”按钮，在 Marketplace 中重新发布产品。

    ![VM 映像](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>更新产品或 SKU 的非技术详细信息
可以更新 Marketplace 中实时产品或 SKU 的非技术（市场营销、法律、支持、类别）详细信息。

### <a name="update-the-offer-description-and-logos"></a>更新产品说明和徽标
若要更新产品详细信息并重新发布产品，请执行以下步骤：

1. 登录到[发布门户](https://publish.windowsazure.com)。
2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧菜单中，单击“市场营销”选项卡。
4. 单击“英语(美国)”。
5. 单击“详细信息”选项卡。在“说明”部分中，更新产品的“标题”、“摘要”和“详细摘要”，然后保存更改。

   > [!NOTE]
   > 更新 SKU 详细信息时，请注意以下限制： 
   * 不要在产品说明和 SKU 说明下输入重复文本。
   * 不要在 SKU 标题和产品详细摘要下输入重复文本。 
   * 不要在 SKU 标题和产品摘要下输入重复文本。
   >
   >

    ![详细信息](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. 在“详细信息”选项卡的“徽标”部分中更新徽标。 确保徽标遵守 [Azure Marketplace 准则](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)。

   > [!NOTE]
   > 特大图标是可选的。 可以选择不上传特大图标。 但是，上传特大图标后，无法通过任何预配将其从发布门户中删除。 请遵守[特大图标准则](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)。
   >
   >
7. 转到“发布”选项卡，然后单击“推送到过渡环境”。 有关在过渡环境中测试产品的详细指导，请参阅[测试 Marketplace 的 VM 产品](marketplace-publishing-vm-image-test-in-staging.md)。
8. 在过渡环境中测试产品后，导航到发布门户中的“发布”选项卡。 单击“请求批准推送到生产”按钮，在 Marketplace 中重新发布产品。

    ![徽标](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>更新 SKU 说明
若要更新 SKU 详细信息并重新发布产品，请执行以下步骤：

1. 登录到[发布门户](https://publish.windowsazure.com)。
2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧菜单中，单击“市场营销”选项卡。
4. 单击“英语(美国)”。
5. 单击“计划”选项卡。在“SKU”部分中，更新 SKU 的“标题”、“摘要”和“说明”，然后保存更改。

   > [!NOTE]
   > 更新 SKU 详细信息时，请注意以下限制： 
   * 不要在产品说明和 SKU 说明下输入重复文本。 
   * 不要在 SKU 标题和产品详细摘要下输入重复文本。 
   * 不要在 SKU 标题和产品摘要下输入重复文本。
   >
   >
6. 转到“发布”选项卡，然后单击“推送到过渡环境”。 有关在过渡环境中测试产品的详细指导，请参阅[测试 Marketplace 的 VM 产品](marketplace-publishing-vm-image-test-in-staging.md)。
7. 在过渡环境中测试产品后，导航到发布门户中的“发布”选项卡。 单击“请求批准推送到生产”按钮，在 Marketplace 中重新发布产品。

    ![计划](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>更改现有链接或添加新链接
若要更改现有链接或添加新链接，然后重新发布产品，请执行以下步骤：

1. 登录到[发布门户](https://publish.windowsazure.com)。
2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧菜单中，单击“市场营销”选项卡。
4. 单击“英语(美国)”。
5. 单击“链接”选项卡。
6. 若要添加新链接，请在“链接”部分中单击“+ 添加链接”。 在“添加链接”对话框中，输入链接的“标题”和“URL”，然后保存更改。 可以输入任何包含可帮助客户的信息的链接。
7. 若要更新或删除现有链接，请选择该链接，然后单击“编辑”按钮或“删除”按钮。

   > [!NOTE]
   > 确保已在本部分中输入的链接可工作正常，因为在生产请求过程中，将对这些链接进行验证。
   >
   >
8. 转到“发布”选项卡，然后单击“推送到过渡环境”。 有关在过渡环境中测试产品的详细指导，请参阅[测试 Marketplace 的 VM 产品](marketplace-publishing-vm-image-test-in-staging.md)。
9. 在过渡环境中测试产品后，导航到发布门户中的“发布”选项卡。 单击“请求批准推送到生产”按钮，在 Marketplace 中重新发布产品。

    ![链接](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![添加链接](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>更改现有示例图像或添加新示例图像
若要更改现有示例图像或添加新示例图像，然后重新发布产品，请执行以下步骤：

> [!NOTE]
> 仅在 [Azure 门户](https://portal.azure.com)中显示一张示例图像。
>
>

1. 登录到[发布门户](https://publish.windowsazure.com)。
2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧菜单中，单击“市场营销”选项卡。
4. 单击“英语(美国)”。
5. 单击“示例图像”选项卡。
6. 若要添加新的示例图像，请在“示例图像”部分中单击“上传新图像”按钮，然后保存更改。

   > [!NOTE]
   > 包括示例图像是一个可选步骤。
   >
7. 转到“发布”选项卡，然后单击“推送到过渡环境”。 有关在过渡环境中测试产品的详细指导，请参阅[测试 Marketplace 的 VM 产品](marketplace-publishing-vm-image-test-in-staging.md)。
8. 在过渡环境中测试产品后，导航到发布门户中的“发布”选项卡。 单击“请求批准推送到生产”按钮，在 Marketplace 中重新发布产品。

    ![示例图像](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>更新法律内容
若要更新法律内容并重新发布产品，请执行以下步骤：

1. 登录到[发布门户](https://publish.windowsazure.com)。
2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧菜单中，单击“市场营销”选项卡。
4. 单击“英语(美国)”。
5. 单击“法律”选项卡。在“法律”部分中，更新策略/使用条款。 在“使用条款”文本框中输入或粘贴策略/条款并保存更改。
6. 法律使用条款的字符数限制为 100 万个字符。
7. 转到“发布”选项卡，然后单击“推送到过渡环境”。 有关在过渡环境中测试产品的详细指导，请参阅[测试 Marketplace 的 VM 产品](marketplace-publishing-vm-image-test-in-staging.md)。
8. 在过渡环境中测试产品后，导航到发布门户中的“发布”选项卡。 单击“请求批准推送到生产”按钮，在 Marketplace 中重新发布产品。

    ![合法](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>更新支持信息
若要更新支持信息并重新发布产品，请执行以下步骤：

1. 登录到[发布门户](https://publish.windowsazure.com)。
2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧菜单中，单击“支持”选项卡。
4. 在“工程联系人”部分中，更新工程联系人详细信息。 这些详细信息仅用于合作伙伴和 Microsoft 之间的内部通信。
5. 在“客户支持”部分中，更新支持联系人详细信息和“支持 URL”。 这些详细信息仅用于合作伙伴和 Microsoft 之间的内部通信。

   > [!NOTE]
   > 如果只想提供电子邮件支持，请在“客户支持”部分中输入虚构的电话号码。 在此情况下，将改用提供的电子邮件。
   >
   >
6. 转到“发布”选项卡，然后单击“推送到过渡环境”。 有关在过渡环境中测试产品的详细指导，请参阅[测试 Marketplace 的 VM 产品](marketplace-publishing-vm-image-test-in-staging.md)。
7. 在过渡环境中测试产品后，导航到发布门户中的“发布”选项卡。 单击“请求批准推送到生产”按钮，在 Marketplace 中重新发布产品。

    ![支持](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>更新类别
若要更新产品的类别部分并重新发布产品，请执行以下步骤：

1. 登录到[发布门户](https://publish.windowsazure.com)。
2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧菜单中，单击“类别”选项卡。
4. 在“类别”部分中，更新产品类别并保存更改。 可以为 Azure Marketplace 库选择最多五个类别。
5. 转到“发布”选项卡，然后单击“推送到过渡环境”。 有关在过渡环境中测试产品的详细指导，请参阅[测试 Marketplace 的 VM 产品](marketplace-publishing-vm-image-test-in-staging.md)。
6. 在过渡环境中测试产品后，导航到发布门户中的“发布”选项卡。 单击“请求批准推送到生产”按钮，在 Marketplace 中重新发布产品。

    ![Categories](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>在已列出的产品下添加新 SKU
若要在实时产品中添加新 SKU，请执行以下步骤：

1. 登录到[发布门户](https://publish.windowsazure.com)。
2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧菜单中，单击“SKU”选项卡。然后单击“添加 SKU”。 
4. 在对话框中，以小写形式输入“SKU 标识符”。 若要使用 BYOL 计费模型发布新 SKU，请选中“自带许可(BYOL)模型”复选框。 否则，请清除该复选框。 单击勾选标记创建新 SKU。 如果未选择 BYOL 计费模型，则计费模型将自动设置为“每小时”。 如果想要为每小时计费模型启用 30 天免费试用，请针对“是否提供免费试用?”单击“一个月”。 否则，选择“无试用”。 （仅当创建新 SKU 时未选择 BYOL 时，才显示选项“是否提供免费试用?”。）

   > [!IMPORTANT]
   > 仅在批准发布解决方案模板的情况下，才可将“针对 Marketplace 隐藏此 SKU，因为它始终应通过解决方案模板购买”选项标记为“是”。 否则，此选项应始终为“否”。
   >
   >
4. 在左侧菜单中单击“VM 映像”选项卡，并找到已创建的新 SKU。
5. 若要设置新 SKU，请参阅[获取 VM 映像的认证](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image)获得指南。
6. 若要添加新 SKU 的市场营销材料，请参阅[提供 Marketplace 市场营销内容](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)。
7. 若要添加新 SKU 的定价信息，请参阅[设置价格](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)。
8. 转到“发布”选项卡，然后单击“推送到过渡环境”。 有关在过渡环境中测试产品的详细指导，请参阅[测试 Marketplace 的 VM 产品](marketplace-publishing-vm-image-test-in-staging.md)。
9. 在过渡环境中测试产品后，导航到发布门户中的“发布”选项卡。 单击“请求批准推送到生产”按钮，在 Marketplace 中重新发布产品。

    ![SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![添加 SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>更改已列出 SKU 的数据磁盘计数
无法递增/递减已列出的 SKU 的数据磁盘计数。 在此情况下，需要创建新的 SKU。 有关详细指导，请参阅[在已列出的产品下添加新 SKU](#add-a-new-sku-under-a-listed-offer) 部分。

## <a name="delete-a-listed-offer-from-the-marketplace"></a>从 Marketplace 中删除已列出的产品
请求删除实时产品时，需要注意多个方面。 若要从支持团队获取有关从 Marketplace 中删除已列出的产品的指南，请执行以下步骤：

1. 在 [创建事件](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681) 页中提出支持票证。

2. 选择“管理产品”作为问题类型，并选择“修改已在生产中的产品和/或 SKU”作为类别。
3. 提交请求。

支持团队将指导你完成产品和 SKU 删除过程。

> [!NOTE]
> 当产品处于“草稿”状态（而不是“过渡”或“生产”）时，始终可以将其删除。 在“历史记录”选项卡上单击“丢弃草稿”。
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>从 Marketplace 中删除已列出的 SKU
若要从 Marketplace 中删除已列出的 SKU，请执行以下步骤：

1. 登录到[发布门户](https://publish.windowsazure.com)。

2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧窗格中，单击“SKU”选项卡。
4. 选择要删除的 SKU，然后单击“删除”按钮。
5. 转到发布门户中的“发布”选项卡。 单击“请求批准推送到生产”按钮，在 Azure Marketplace 中重新发布产品。
6. 在 Marketplace 中重新发布产品后，该 SKU 将从 Marketplace 和 Azure 门户中删除。

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>从 Marketplace 中删除已列出的 SKU 的当前版本
若要从 Marketplace 中删除已列出的 SKU 的当前版本，请执行以下步骤： 

1. 登录到[发布门户](https://publish.windowsazure.com)。

2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧菜单中，单击“VM 映像”选项卡。
4. 选择要删除其当前版本的 SKU，然后单击“删除”按钮。
5. 转到发布门户中的“发布”选项卡。 单击“请求批准推送到生产”按钮，在 Azure Marketplace 中重新发布产品。
6. 在 Marketplace 中重新发布产品后，已列出的 SKU 的当前版本将从 Marketplace 和 Azure 门户中删除。 然后，SKU 将回滚到其以前的版本。

## <a name="revert-the-listing-price-to-production-values"></a>将定价还原为生产值
若要将定价还原为生产值，请执行以下步骤：

1. 登录到[发布门户](https://publish.windowsazure.com)。
2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧菜单中，单击“定价”选项卡。
4. 选择要重置其定价的区域。

    ![定价区域](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. 对于采用每小时计费模型的 SKU，可以重置所有核心的价格，因为它们针对所选区域处于生产状态下。 对于采用 BYOL 计费模型的 SKU，通过针对“外部许可(BYOL) SKU 可用性”中的 SKU 选中相应复选框，使 SKU 在该区域中可用。

    ![定价模型](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. 单击“根据美国价格对其他市场自动定价”按钮。

   > [!NOTE]
   > 按钮的标签可能因选择的区域而有所不同。 由于创建此文档时选择了“美国”，因此以下屏幕截图中的按钮标记为“根据美国价格对其他市场自动定价”。
   >
   >

    ![自动定价](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. 在“自动定价”向导的第 1 页选择基本市场，然后单击**箭头**按钮。

    ![基本市场](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. 在第 2 页选择服务计划和计量（核心），然后单击**箭头**按钮。

    ![服务计划和计量](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. 在第 3 页单击“全部切换”选择所有区域。 也可以手动选中特定区域对应的复选框。 单击**箭头**按钮。

    ![选择市场](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. 在第 4 页查看汇率，然后单击“完成”。 向导将根据选择重置定价。

11. 在“定价”选项卡上，单击“查看摘要和更改”。
    对于“查看版本”，请选择“草稿”；对于“比较”，请选择“生产”。 如果看不到任何定价差别，则表示定价已成功还原为生产值。

    ![查看摘要和更改](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. 转到“发布”选项卡，然后单击“推送到过渡环境”。 有关在过渡环境中测试产品的详细指导，请参阅[测试 Marketplace 的 VM 产品](marketplace-publishing-vm-image-test-in-staging.md)。
13. 在过渡环境中测试产品后，导航到发布门户中的“发布”选项卡。 单击“请求批准推送到生产”按钮，在 Marketplace 中重新发布产品。

## <a name="revert-the-billing-model-to-production-values"></a>将计费模型还原为生产值
若要将计费模型还原为生产值，请执行以下步骤：

1. 登录到[发布门户](https://publish.windowsazure.com)。

2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧菜单中，单击“SKU”选项卡。
4. 单击“编辑”按钮还原计费模型。 在打开的窗口中，选中或清除“计费和许可从 Azure 外部完成(也称为自带许可)”复选框。

    ![编辑计费](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. 请遵循本文“将定价还原为生产值”中的步骤。
6. 转到“发布”选项卡，然后单击“推送到过渡环境”。 有关在过渡环境中测试产品的详细指导，请参阅[测试 Marketplace 的 VM 产品](marketplace-publishing-vm-image-test-in-staging.md)。
7. 在过渡环境中测试产品后，导航到发布门户中的“发布”选项卡。 单击“请求批准推送到生产”按钮，在 Marketplace 中重新发布产品。

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>将列出的 SKU 的可见性设置还原为生产值
若要将列出的 SKU 的可见性设置还原为生产值，请执行以下步骤：

1. 登录到[发布门户](https://publish.windowsazure.com)。

2. 转到“虚拟机”选项卡并选择你的产品。
3. 在左侧菜单中，单击“SKU”选项卡。
4. 选择 SKU，并将该 SKU 的可见性设置还原为生产值。

    ![可见性](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. 完成更改后，请单击“请求批准推送到生产”按钮，在 Marketplace 中重新发布产品。

## <a name="see-also"></a>另请参阅
* [入门：将产品发布到 Azure Marketplace ](marketplace-publishing-getting-started.md)
* [了解付款报告](marketplace-publishing-report-payout.md)
* [更改云解决方案提供商经销商激励](marketplace-publishing-csp-incentive.md)
* [排查 Marketplace 中的常见发布问题](marketplace-publishing-support-common-issues.md)
* [作为发布者获取支持](marketplace-publishing-get-publisher-support.md)
* [创建本地 VM 映像](marketplace-publishing-vm-image-creation-on-premise.md)
* [在 Azure 预览门户中创建运行 Windows 的虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

