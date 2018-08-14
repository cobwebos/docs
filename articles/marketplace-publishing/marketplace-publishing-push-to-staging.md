---
title: 准备和测试产品/服务以供部署到 Azure 市场 | Microsoft Docs
description: 有关在部署到 Azure 市场之前，提供市场营销内容、配置定价计划和测试套餐的详细说明。
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: hascipio
ms.openlocfilehash: 7db86716cdf8f9eb921c3c1813970acae7a3016b
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714953"
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>使用市场营销内容完成产品/服务创建
在发布过程的此步骤中，需要提供某些市场营销内容以及有关 Azure Marketplace 中产品/服务和/或 SKU 的详细信息。 例如，你将提供产品描述、公司徽标、价格计划、计划详细信息以及将套餐和/或 SKU 推送到过渡环境所需的其他信息。 此信息用作 Azure 门户中的市场营销内容。 将在[发布门户][link-pubportal]中开始此过程。

## <a name="step-1-provide-marketplace-marketing-content"></a>步骤 1：提供 Marketplace 市场营销内容
**英语是默认语言和唯一受支持的语言。** 请确保使用英语在字段中提供所有信息。 在推送到过渡环境之前，可以随时编辑所有信息。

1. 登录发布门户 [https://publish.windowsazure.com](https://publish.windowsazure.com)。
2. 在左侧菜单中，单击“市场营销”选项卡。
3. 在主面板中，单击“英语(美国)”按钮。
   
   > [!IMPORTANT]
   > 所有字段都必须进行输入（包括图像），以便能够推送到过渡环境。
   > 
   > 

### <a name="details-and-plans"></a>详细信息和计划
1. 在“详细信息”选项卡下，输入套餐标题（最多 50 个字符）、套餐摘要（最多 100 个字符）、套餐详细摘要（最多 256 个字符）、套餐描述（最多 1300 个字符）、徽标
2. 在“计划”选项卡下，输入计划标题（最多 50 个字符）、计划摘要（最多 100 个字符）、计划描述（最多 2000 个字符）。
   
   > [!NOTE]
   > 可以使用以下 HTML 标记，设置套餐和计划的摘要、详细摘要以及描述的格式。 允许的 HTML 标记是 h1、h2、h3、h4、h5、p、ol、ul、li、a[target|href]、strong、em、b、i。
   > 
   > 
3. 不要在套餐和计划描述下输入重复文本。
4. 不要在计划标题和套餐详细摘要下输入重复文本。
5. 不要在计划标题和套餐摘要下输入重复文本。
6. 不要为包含多个计划的某个套餐输入相同的计划标题。
7. 采用 PNG 格式根据所需规范（如发布门户中所述）上传图像，规范因大小而异。
8. 确保徽标遵守下面所述的 Azure 市场徽标准则。
   
   ![绘制](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)


  **Azure 市场徽标准则**

上传到发布门户中的所有徽标都应遵循以下准则：

* Azure 设计具有简单的调色板。 保持徽标上的主要和辅助颜色数较低。
* Azure 门户的主题颜色为白色和黑色。 因此，应避免将这些颜色用作徽标的背景色。 使用一些可使徽标在 Azure 门户中突出显示的颜色。 建议使用简单的主颜色。 **如果使用的是透明背景，请确保徽标/文本不使用白色、黑色或蓝色。**
* 不要在徽标上使用渐变背景。
* 避免在徽标上放置文本，即使是公司或品牌名称也不可以。 徽标的外观应“平整”，并且应避免渐变。
* 徽标不应拉伸。
* 小型徽标的大小应为 40 X 40 px
* 中等徽标的大小应为 90 X 90 px
* 大型徽标的大小应为 115 X 115 px
* 宽型徽标的大小应为 255 X 115 px
* 特大徽标的大小应为 815 X 290 px

> [!NOTE]
> 特大徽标是可选的。 发布者可以选择不上传特大徽标。 但是，上传特大图标后，无法将其从发布门户中删除。 此时，合作伙伴必须遵循特大图标的 Azure 市场准则。
> 
> 

  ![绘制](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**特大徽标图标的其他准则（可选）**

* 特大徽标是可选的。 发布者可以选择不上载特大徽标。 **但是，在上传特大图标后，将无法将其从发布门户中删除。此时，合作伙伴必须遵循特大图标的 Azure 市场准则，否则产品/服务将不批准用于生产。**
* 发布者显示名称、计划标题和套餐详细摘要以白色字体颜色显示。 因此，应避免在特大图标的背景中保留任何浅色。 特大图标不允许使用黑色、白色和透明背景。
* 在套餐推出后，发布者显示名称、计划标题、套餐详细摘要和创建按钮以编程方式嵌入在特大徽标内。 因此，在设计特大徽标时，不应输入任何文本。 请仅在右侧保留空白区域，因为我们将以编程方式将文本（即，发布者显示名称、计划标题、套餐长摘要）放置在那里。 右侧文本的空白区域应为 415x100（并按 370px 从左侧偏移）。
  
  ![绘制](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>链接
在左侧栏上的“链接”选项卡中，输入包含可能帮助客户的信息的任何链接。 为每个链接输入名称和 URL。

![绘制](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>示例图像（可选）
> [!NOTE]
> 包括示例图像是一个可选步骤。
> 即使可在发布门户中上传多张示例图像，但只有一张图像（由系统随机选择）会显示在 Azure 门户中。 因此，我们建议最多上传一张示例图像。
> 
> 

在左侧菜单的“示例图像”边栏选项卡上，通过单击“上传新图像”上传新图像。 如果已有图像并想要替换它，请单击“替换图像”。

![绘制](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>法律
在“法律”选项卡上，提供指向策略/使用条款的链接。 在较大的“使用条款”框中输入或粘贴这些条款。 法律使用条款的字符数限制为 1,000,000 个字符。

![绘制](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)


  **注意：** 对于虚拟机产品/服务，在产品/服务和 SKU 暂存在 Azure 门户中后，无法更改下面的给定字段：

* 
  **产品/服务标识符：**[发布门户 -&amp;gt; 虚拟机 -&amp;gt; 产品/服务 -&amp;gt; VM 映像选项卡 -&amp;gt; 产品/服务标识符]
* 
  **SKU 标识符：** [发布门户-&gt; 虚拟机-&gt; 选择优惠-&gt; SKU选项卡-&gt; 添加 SKU]
* **发布者命名空间：**[发布门户 -&gt; 虚拟机 -&gt; 演练选项卡 -&gt; 告诉我们公司的情况（可在“步骤 2 注册公司”下找到）-&gt; 发布者命名空间 -&gt; 命名空间]

对于虚拟机产品/服务，在产品/服务和 SKU 在 Azure 市场中列出后，无法更改下面的给定字段：

* 
  **产品/服务标识符：**[发布门户 -&amp;gt; 虚拟机 -&amp;gt; 选择产品/服务 -&amp;gt; VM 映像 -&amp;gt; 产品/服务标识符]
* 
  **SKU 标识符：** [发布门户-&gt; 虚拟机-&gt; 选择优惠-&gt; SKU选项卡-&gt; 添加 SKU]
* **发布者命名空间：**[发布门户 -&gt; 虚拟机 -&gt; 演练选项卡 -&gt; 告诉我们公司的情况（可在“步骤 2 注册”下找到）-&gt; 发布者命名空间 -&gt; 命名空间]
* 
  **端口：**[发布门户 -&amp;gt; 虚拟机 -&amp;gt; 产品/服务 -&amp;gt; VM 映像选项卡 -&amp;gt; 打开端口]
* **已列出的 SKU 的定价更改**
* **已列出的 SKU 的计费模型更改**
* **已列出的 SKU 的计费区域删除**
* **已列出的 SKU 的数据磁盘计数变更**

## <a name="step-2-set-your-prices"></a>步骤 2：设置价格
### <a name="pricing-models"></a>定价模型
| 定价模型 | Description |
| --- | --- |
| 基本 |在购买时支付的包月费用；例如 10 美元/月。 |
| 消耗量（也称为 使用量、计量） |按使用量付费，由产品/服务的发布者定义。 无法按席位、按用户等定义超额，因为不会对一小部分用户或功能执行按比例分配。 合作伙伴每小时报告一次使用量。 客户在每月计费周期付费，而不是像每月计划那样提前付费。 |
| 免费试用 |客户可在限定时间内免费使用，并在试用期结束后支付正常费率。 |
| 免费层 |计划始终免费。 |
| 计划迁移（也称为 转换或升级/降级） |是指用户从其当前计划移动到其他可接受的计划；由合作伙伴定义。 |


  **按产品/服务类型提供的定价模型**

> [!IMPORTANT]
> 某些定价模型的提供情况因产品/服务类型而异。 请参阅下表。
> 
> 

|  | 仅基本 | 仅消耗量 | 基本 + 消耗量 |
| --- | --- | --- | --- |
| 虚拟机映像 |否 |是 |否 |
| 开发人员服务 |是 |是 |是 |

### <a name="21-set-your-vm-prices"></a>2.1. 设置 VM 价格
当前，对于虚拟机，我们有以下 **3 种类型的计费模型：**

* **每小时：** 根据发布者按 VM 大小设置的费率，以每小时为单位向客户收取费用。 如果使用 SKU 的**每小时计费**模型，总价格将为发布者收取的软件成本和 Microsoft 收取的基础结构成本的总和。 此总成本以每小时和每月价格形式显示给考虑购买的客户（请参阅下面的屏幕截图）。 **发布者获得他们所收取的 80% 的软件成本。** 因此，请在设置 SKU 价格之前，进行相应的计算。
  
    ![绘制](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **免费试用：** 这是另一种形式的每小时模型。 此模型在部署 VM 后的前 30 天内不会向客户收取软件成本（免费）。 30 天后，根据发布者在每小时模型中设置的费率，以每小时为单位向他们收取费用。
* **自带许可 (BYOL)：** 发布者管理在 VM 上运行的软件的许可。


  **重要提示：** 产品/服务和 SKU 在 Azure 市场中列出后，将无法更改下面给定的字段。

* **已列出的 SKU 的定价更改**
* **已列出的 SKU 的计费模型更改**
* **已列出的 SKU 的计费区域删除**
* **已列出的 SKU 的数据磁盘计数变更**
* 
  **产品/服务标识符：**[发布门户 -&amp;gt; 虚拟机 -&amp;gt; 选择产品/服务 -&amp;gt; VM 映像 -&amp;gt; 产品/服务标识符]
* 
  **SKU 标识符：** [发布门户-&gt; 虚拟机-&gt; 选择优惠-&gt; SKU选项卡-&gt; 添加 SKU]
* **发布者命名空间：**[发布门户 -&gt; 虚拟机 -&gt; 演练选项卡 -&gt; 告诉我们公司的情况（可在“步骤 2 注册”下找到）-&gt; 发布者命名空间 -&gt; 命名空间]
* 
  **端口：**[发布门户 -&amp;gt; 虚拟机 -&amp;gt; 产品/服务 -&amp;gt; VM 映像选项卡 -&amp;gt; 打开端口]

### <a name="sell-to-countries-of-the-sku"></a>SKU 的“销往”国家/地区
需要仔细考虑要提供 SKU 的国家/地区。 某些国家/地区归类为“Microsoft 免税”，其他国家/地区归类为“ISV 免税”。

* 在“Microsoft 免税”国家/地区中，Microsoft 向客户收取税款并向政府支付（汇寄）税款。
* 在“ISV 免税”国家/地区中，合作伙伴负责向客户收取税款并向政府支付税款。 如果选择在“ISV 免税”国家/地区中销售，必须能够在所选国家/地区中计算和支付税款。

> [!NOTE]
> SKU 在这些国家/地区中不可用，除非在[发布门户](https://publish.windowsazure.com)中设置了其定价。 下面提供了设置每小时和 BYOL SKU 的定价的指南。
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 如何为 SKU 设置每小时定价模型
请按如下步骤为 SKU 设置每小时定价模型：

1. 登录[发布门户](https://publish.windowsazure.com)。
2. 导航到“虚拟机”选项卡并选择优惠。
3. 在左侧菜单中，单击“SKU”选项卡。
4. 确保 SKU 标记为“每小时计费模型”。 如果未标记，单击“编辑”按钮还原该计费模型。 将打开一个窗口。 取消选中“计费和许可从 Azure 外部完成(也称为自带许可)”复选框并保存更改。
5. 如果想要启用在 SKU 部署的前 30 天免费试用，针对“是否提供免费试用？”问题选择“一个月”选项 否则，请选择“无试用”选项。 现在，按照如下步骤操作。
6. 在左侧菜单中，单击“定价”选项卡。
7. 选择基本区域。
   
   ![绘制](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. 为所有核心设置价格。 **即使 SKU 不支持，也必须为 SKU 的所有核心提供价格。**
   
    ![绘制](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. 手动为其他区域设置价格，或者可以使用“自动定价”向导根据基本区域设置其他区域的价格。 若要使用“自动定价”向导，单击“根据美国价格对其他市场自动定价”按钮。 注意：按钮的标签可能因选择的区域而有所不同。 由于在创建此文档时，我们选择了美国，因此在下面的屏幕截图中按钮标记为“根据美国价格对其他市场自动定价”。
   
   ![绘制](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. 自动定价向导将打开。 第一页显示基本市场的选择。 进行选择，并通过单击“->”按钮移动到下一页。
    
    ![绘制](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. 用于选择核心和计划的选项会显示在第 2 页上。 选择所需的计划，并单击“->”按钮。 单击“全部切换”按钮选择所有“服务计划”和“计量”，也可以手动选中这些复选框。 **即使 SKU 不支持，也必须为 SKU 的所有核心提供价格。** 因此，请确保选中所有核心大小。
    
    ![绘制](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. 第 3 页显示市场/区域。 单击“全部切换”按钮选择所有区域，也可以手动选中区域对应的框。 单击“->”按钮转到下一页。 **注意：** Microsoft 免税的国家/地区由像建筑物的符号表示。 有关更多详细信息，请参考此页面的 SKU 的“销往”国家/地区部分。
    
    ![绘制](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. 第 4 页显示汇率。 单击“完成”按钮完成步骤。

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 如何为 SKU 设置 BYOL 定价模型
请按照如下步骤为 SKU 设置 BYOL 定价模型：

1. 登录[发布门户](https://publish.windowsazure.com)。
2. 导航到“虚拟机”选项卡并选择优惠。
3. 在左侧菜单中，单击“SKU”选项卡。
4. 确保 SKU 标记为“自带许可 SKU”。 如果未标记，单击“编辑”按钮还原该计费模型。 将打开一个窗口。 选中“计费和许可从 Azure 外部完成(也称为自带许可)”复选框并保存更改。
   
   ![绘制](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. 在左侧菜单中，单击“定价”选项卡。
6. 选择基本区域，通过针对“外部许可 (BYOL) SKU 可用性”部分下的 SKU 选中相应复选框，以使 SKU 在该区域中可用（请参阅下面的屏幕截图）。
   
   ![绘制](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. 手动使 SKU 在其他区域可用，也可以使用“自动定价”向导实现此目的。 请参阅此页面的 **“2.1.1 如何为 SKU 设置每小时定价模型”** 部分中的第 9 条至第 13 条（解释了如何使用“自动定价”向导）。

### <a name="22-set-your-developer-service-prices"></a>2.2. 设置开发人员服务价格
计划可以是基本 + 消耗量的任意组合，其中基本是每月价格，超额是按使用量付费价格。 （有关更多详细信息，请参见下文。）


  **示例：** Contoso 开发人员服务产品

| 计划 | 价格 | 包括 | 迁移路径 |
| --- | --- | --- | --- |
| 免费 |0 美元/月 |基本功能。 |可以迁移到任何其他计划 |
| Bronze |10 美元/月 |基本功能和功能 X 的 1,000 配额。 |可以迁移到 Bronze Plus、Silver 和 Gold 计划 |
| Bronze Plus |免费试用期：0 美元/月 + 0 美元/meter01 |基本功能和功能 X 的 10,000 配额。使用完功能 X 配额后，客户通过 meter01 按使用量付费。 |可以迁移到 Silver Plus 和 Gold 计划 |
| Bronze Plus |付费时段（也称为 免费试用已过期）：10 美元/月 + 0.05 美元/meter01 |基本功能和功能 X 的 10,000 配额。使用完功能 X 配额后，客户可通过 meter01 按使用量付费。 |可以迁移到 Silver Plus 和 Gold 计划 |
| Silver |0.15 美元/meter01 |客户可以通过 meter01 按使用量付费，即针对功能 X 付费。 |可以迁移到 Bronze 和 Gold 计划 |
| Silver Plus |20 美元/月 + 0.15 美元/meter01 + 0.01 美元/meter02 |基本功能和功能 X 的 10,000 配额，再加上功能 Y 的 100 配额。使用完功能 X 配额后，客户可通过 meter01 按使用量付费。  使用完功能 Y 配额后，客户可通过 meter02 按使用量付费。 |可以迁移到 Bronze Plus 和 Gold 计划 |
| Gold |1,000 美元/月 |功能 X 的 10,000 配额、功能 Y 的 1,000 配额，以及不受限制的功能 Z。 |可以迁移到免费计划之外的所有计划 |

## <a name="step-3-provide-support-information"></a>步骤 3：提供支持信息
联系人详细信息仅用于合作伙伴和 Microsoft 之间的内部通信。 支持 URL 将提供给最终客户。

1. 转到发布门户左侧的“支持”标题。
2. 在“工程联系人”下输入信息。
3. 在“客户支持”下输入信息。 如果仅提供电子邮件支持，请输入虚拟电话号码，将改用你提供的电子邮件。
4. 输入支持 URL。

## <a name="step-4-choose-azure-marketplace-categories"></a>步骤 4：选择 Azure 市场类别
“类别”选项卡提供大量选项。 产品/服务可以归入这些类别，可以选择最多五个类别。

## <a name="how-your-marketing-will-appear"></a>市场营销内容的显示方式
下面详细介绍了产品/服务市场营销信息在 [Azure Marketplace 网站](https://azure.microsoft.com/marketplace/)上和 [Azure 门户](https://portal.azure.com)中的使用方式。

### <a name="azure-marketplace-website"></a>Azure 市场网站
![绘制](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![绘制](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)


  *Azure 市场网站上列出的产品/服务*

![绘制](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)


  *Azure 市场网站上的产品/服务说明详细信息*

![绘制](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)


  *Azure 市场网站上的产品/服务说明定价详细信息*

### <a name="azure-portal"></a>Azure 门户
![绘制](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)


  *Azure 门户中的产品/服务列表*

![绘制](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)


  *Azure 门户中的产品/服务描述详细信息*

## <a name="next-steps"></a>后续步骤
现在，市场内容已加载，让我们继续执行测试处于过渡环境中的产品/服务的操作。 但是，必须从下面的列表中选择相应的产品/服务类型，相关步骤因产品/服务类型而异。

* 
  [在过渡环境中测试 VM 产品/服务](marketplace-publishing-vm-image-test-in-staging.md)
* 
  [在过渡环境中测试解决方案模板产品/服务](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>另请参阅
* 
  [入门：如何将产品/服务发布到 Azure 市场](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
