---
title: 创建虚拟机产品/服务 | Microsoft Docs
description: 将虚拟机发布到 Azure 市场。
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
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 519867b1e0607a769948c86af263c172e810d107
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54078110"
---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>将虚拟机发布到 Azure 市场

本文提供了向 Azure 市场发布虚拟机产品/服务所需的步骤。

## <a name="prerequisites"></a>先决条件

以下技术和非技术先决条件适用于在 Azure 市场上发布虚拟机

### <a name="technical"></a>技术方面

-   [为 Azure 市场创建虚拟机映像的技术先决条件](../cloud-partner-portal/virtual-machine/cpp-create-technical-assets.md)
-   [创建并上传 Linux VHD](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
-   [从映像创建并测试 Linux VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)
-   [创建并上传 Windows VHD ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)
-   [从映像创建并测试 Windows VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)
-   [如何对在 VHD 创建期间遇到的常见问题进行故障排除](../cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues.md)
-   [适用于 Azure 市场映像的安全建议](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)


### <a name="non-technical-business-requirements"></a>非技术方面（业务要求）

 -   公司（或其子公司）位于 Azure 市场支持的销售国家/地区
-   产品必须以兼容 Azure 市场支持的计费模型的方式获得许可
-   你需要以商业上合理的方式负责向客户提供技术支持。 支持形式可以为免费、付费或社区支持。
-   负责获得自己的软件和任何第三方软件依赖项的许可。
-   提供的内容必须满足在 Azure 市场上和 Azure 门户中列出的产品/服务的标准。
-   同意 Azure 市场参与政策与发布者协议的条款。
-   同意遵守[使用条款](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft 隐私声明](https://www.microsoft.com/privacystatement/default.aspx)和 [Microsoft Azure 认证计划协议](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)。

## <a name="before-you-begin"></a>开始之前

满足所有先决条件后，可以开始创作解决方案模板产品/服务。 在开始之前，请查看以下套餐和 SKU 信息。

**产品**

Azure 应用程序套餐对应于发布者提供的一类产品套餐。 如果你有想要在 Azure 市场中提供的新型解决方案/应用程序，最佳方法是创建新的套餐。 产品/服务是 SKU 的集合。 每个产品/服务在 Azure 市场中以自有实体的方式显示。

**SKU**

SKU 是可购买的产品/服务的最小单位。 在相同的产品类（套餐）内，通过 SKU 能够区分支持的不同功能。 例如，套餐是托管的或非托管的，并支持不同的计费模型。

对于以下情况，请添加多个 SKU：
- 要支持不同的计费模型，例如自带许可 (BYOL) 或即用即付 (PAYG)。
- 每个 SKU 支持不同的功能集，每个功能集的定价方式不同。

SKU 显示在 Azure 市场的父套餐下，并在 Azure 门户中以可购买的自有实体的方式显示。

## <a name="to-create-a-new-offer"></a>创建新套餐

1.  登录[云合作伙伴门户](http://cloudpartner.azure.com/)。

2.  在左侧导航栏中，依次选择“+ 新建产品/服务”和“虚拟机”。

    ![虚拟机的新建产品/服务](./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png)

3.  在“新建套餐”下，选择“编辑器”。

![“新建产品/服务”编辑器](./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png)

4.  在“编辑器”下面的以下视图中提供信息：
    - 产品/服务设置
    - SKU
    - 市场
    - “支持每个”视图包含一组要填写的字段。必填字段标有红色星号 (\*)。

## <a name="to-configure-offer-settings"></a>配置“套餐设置”

1. 在“套餐设置”中配置以下“套餐标识”字段。

    **套餐 ID**

     发布者配置文件中套餐的唯一标识符。 此 ID 显示在产品 URL、Azure 资源管理器模板和计费报表中。 只能使用小写字母数字字符或短划线 (-)。 ID 不得以破折号结尾，且长度不得超过 50 个字符。 例如，如果发布者 Contoso 发布了产品/服务 ID 为 sample-vm 的产品/服务，该产品/服务将以“https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview”形式显示在 Azure 市场中  
    >[!Note]
    >此字段在套餐推出后处于锁定状态。

    **发布者 ID**

    发布者配置文件的下拉列表。 选择要发布的套餐所在的配置文件。 
    >[!Note]
    >此字段在套餐推出后处于锁定状态。

    **名称**

    产品/服务的显示名称。 此名称将显示在 Azure 市场和 Azure 门户中。 该名称不能超过 50 个字符。 参考以下指导指定套餐名称：
    -  其中包含产品的可识别品牌名称。 
    - 除非有套餐营销方面的需要，否则不要在此处包含公司名称。
    - 如果你在自己的网站上营销此套餐，请确保此名称与网站上的名称相同。

2. 选择“保存”以完成套餐的

## <a name="to-create-a-sku"></a>创建 SKU

1. 选择“SKU”。 
2. 选择“添加 SKU”以输入 SKU ID。 SKU ID 是 SKU 在产品/服务内的唯一标识符。 此 ID 显示在产品 URL、Azure 资源管理器模板和计费报表中。 SKU ID：
    - 不能超过 50 个字符。
    - 只能由小写字母数字字符或短划线 (-) 组成。
    - ID 不能以短划线结尾。

    ![添加 SKU](./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png)


## <a name="configure-sku-details"></a>配置 SKU 详细信息

添加 SKU 后，它会显示在 SKU 视图中的 SKU 列表内。 若要查看 SKU 详细信息，请选择 SKU 名称。 可使用详细信息视图在以下字段中添加信息。

### <a name="hide-this-sku"></a>隐藏此 SKU

使用此设置可管理 SKU 可见性。 如果“隐藏此 SKU”处于关闭状态，则客户可以在 [Azure 市场](https://azuremarketplace.microsoft.com)和 [Azure 门户](https://portal.azure.com/)中看见 SKU。 在只想让此 SKU 通过解决方案模板可用，而不能用于单独购买的情况下，用户可能想要隐藏 SKU。

### <a name="cloud-availability"></a>云可用性

通过此字段，可以定义 SKU 在不同 Azure 云中的可用性。

**公共 Azure**

可在集成了市场的所有公共 Azure 区域中向客户部署此 SKU。

**Azure 政府云**

可在 Azure 政府云中部署此 SKU。 在发布到 [Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)之前，建议发布者测试并验证他们的解决方案是否按预期工作。 若要暂存和测试，请[请求试用帐户](https://azure.microsoft.com/offers/ms-azr-usgov-0044p)。 

>[!Note]
>Microsoft Azure 政府是政府社区云，美国联邦、州、地方或部落客户和有资格服务于这些实体的合作伙伴对政府社区云具有受控访问权限。

### <a name="countryregion-availability"></a>国家/区域可用性

此字段可确定能够购买 SKU 的区域。 需要仔细考虑要提供 SKU 的国家/地区。 一些国家/地区归类为“Microsoft 免税国家/地区”。

-   在“Microsoft 免税国家/地区”中，Microsoft 向客户收取税款并向政府支付（汇寄）税款。

-   在其他国家/地区中，合作伙伴负责向客户收取税款并向政府支付税款。 如果选择在这些国家/地区销售，必须具备计算和支付税款的能力。

![选择国家/区域可用性](./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png)

### <a name="pricing"></a>定价

目前支持两种定价模型。

#### <a name="bring-your-own-license-byol"></a>自带许可 (BYOL)

管理在 VM 上运行的软件的许可。 Microsoft 仅收取基础结构成本。

#### <a name="usage-based-monthly-billed-sku"></a>基于使用情况的按月计费的 SKU

根据发布者按 VM 大小设置的费率，以每小时为单位向客户收取费用。 如果使用**每小时计费**模型，总价格将是发布者收取的软件成本和 Microsoft 收取的基础结构成本的总和。 当客户考虑购买时，此总成本以每小时和每月价格形式显示给客户。 在这种情况下，计费按月计算。

在基于使用情况的模型内，用户还要求提供其他设置。

**免费试用**

可以指定是否要为客户提供免费试用版。
此模型在部署 VM 后的前 30/90 天内不会向客户收取软件成本（取决于所选内容）。 免费试用期结束后，根据发布者在每小时模型中设置的费率，以每小时为单位向他们收取费用。

**每核心定价**

可以为 SKU 设置每核心定价。 对于此选项，只需要为单个核心输入基本价格，即可自动计算剩余核心的价格。 在门户中输入价格（以美元为单位），我们会自动计算其他区域的价格。 可以通过使用**导出定价数据**验证其他区域的价格

![每核心定价](./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png)


**离散定价**

如果要分别为每个核心定价，可以单独为每组核心设置定价。

![离散定价](./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png)

**导出/导入定价**

可以灵活导出通过门户配置的定价，以通过 Excel 界面进行更改。 此选项还允许验证每个区域的定价以及以本地货币表示的定价。
单击“导出定价”将下载预填充了定价详细信息的 Excel 文件。 可以在电子表格中编辑这些详细信息，并使用“导入定价”导入更改后的定价。
导入的定价也会反映在门户中。

在此定价 Excel 内，不同区域的价格以当地货币的形式列出。 使用的汇率每天都在刷新。

![导出/导入定价与汇率示例](./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png)

>[!IMPORTANT]
>-   产品/服务推出后，无法更改价格。 但是，仍然能够添加或删除支持的区域。
>-   除  [Azure 的虚拟机定价](https://aka.ms/vmpricingdetails)外，还会以此价格向客户收费。
>-   使用设置价格时的实时汇率为所有区域设置价格（使用本地货币）。
>-   若要单独设置或查看每个区域的价格，请导出定价电子表格并使用自定义定价导入。

## <a name="vm-images"></a>VM 映像

要完成的下一部分将是 VM 映像部分。 在转到此部分前，需要准备好要发布的 VHD。 下面的一些链接有助于创建 VHD：

-   [为 Azure 市场创建虚拟机映像的技术先决条件](../cloud-partner-portal/virtual-machine/cpp-prerequisites.md)
-   [创建并上传 Linux VHD](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
-   [从映像创建并测试 Linux VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)
-   [创建并上传 Windows VHD ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)
-   [从映像创建并测试 Windows VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)
-   [如何对在 VHD 创建期间遇到的常见问题进行故障排除](../cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues.md)

VHD 准备就绪后，可以开始填写此部分。  下面是某些字段的一些详细信息。

### <a name="recommended-vm-sizes"></a>建议的 VM 大小

选择最多六个建议的虚拟机大小。 在客户决定购买和部署映像时，这些建议会在 Azure 市场与 Azure 门户的“定价层”边栏选项卡中显示给客户。 *这些大小仅仅是建议。客户能够选择可容纳在映像中指定的磁盘的任何 VM 大小。*  以下屏幕截图显示了客户将在 Azure 门户中看到的建议的 VM 大小。


![建议的 VM 大小](./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png)


### <a name="open-ports"></a>打开端口

指定想要打开和可用的端口。 这些端口在此 VM 部署期间打开。

### <a name="adding-vm-images"></a>添加 VM 映像

下一步是为 SKU 添加 VM 映像。 每个 SKU 最多可以添加八个磁盘版本。 只有特定 SKU 的最高磁盘版本号将显示在 Azure 市场中。 其他版本号将通过 API 显示。

在“磁盘版本”下，选择“+ 新建版本”。 此选项将显示需要填写的以下字段。

#### <a name="vm-image-version"></a>VM 映像版本

此 VM 映像版本需要遵循[语义版本](http://semver.org/)格式。 版本格式应为 X.Y.Z，X、Y 和 Z 是整数。 不同 SKU 中的映像拥有不同的主版本和次版本。 SKU 中的版本应仅为增量更改，即提升修补程序版本（X.Y.Z 中的 Z）。

#### <a name="os-vhd-url"></a>OS VHD URL

输入为操作系统 VHD 创建的[共享访问签名 URI](../cloud-partner-portal/virtual-machine/cpp-get-sas-uri.md)。

如果存在与此 SKU 关联的数据磁盘，则可以通过选择“ + 新建数据磁盘”链接来选择添加这些磁盘。 此操作会显示可供填写的其他字段。

#### <a name="lun-vhd-url"></a>LUN VHD URL

为数据磁盘输入[共享访问签名 URI](../cloud-partner-portal/virtual-machine/cpp-get-sas-uri.md)。

#### <a name="lun-number"></a>LUN 编号

为此 LUN 分配一个编号。 此数字将针对该数据磁盘保留在此 SKU 中。

>[!Note]
>使用给定的 VM 版本和数据磁盘发布 SKU 后，这些内容就会处于锁定状态且无法更改。 对于已添加到 SKU 的任何其他 VM 版本，需要支持的数据磁盘数量无法更改。

#### <a name="common-sas-url-issues--fixes"></a>常见的 SAS URL 问题和修补程序

| 问题                                                                 | 消息                                                                           | 解决方法                                                           |  文档链接                                                                                |
|---------------------------------------------------------------------  |-------------------------------------------------------------------------------    |-----------------------------------------------------------    |---------------------------------------------------------------------------------------------------    |
| 复制映像时失败 - 在 SAS url 中找不到“?”                | 失败：复制映像。 无法使用所提供的 SAS Uri 下载 blob。       | 使用建议的工具更新 SAS Url                    | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| 复制映像时失败 -“st”和“se”参数不在 SAS url 中   | 失败：复制映像。 无法使用所提供的 SAS Uri 下载 blob。        | 使用其开始和结束日期更新 SAS Url             | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| 复制映像时失败 - "sp = rl" 不在 SAS url 中                    | 失败：复制映像。 无法使用所提供的 SAS Uri 下载 blob         | 使用设置为“读取”和“列出”的权限更新 SAS Url     | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| 复制映像时失败 - SAS url 的 vhd 名称中包含空格     | 失败：复制映像。 无法使用所提供的 SAS Uri 下载 blob。        | 将 SAS Url 更新为不包含空格                       | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |
| 复制映像时失败 - SAS Url 授权错误               | 失败：复制映像。 由于授权错误，无法下载 blob     | 重新生成 SAS Url                                        | https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/     |


## <a name="to-configure-the-marketplace"></a>配置市场

使用“市场”视图配置 [Azure 市场](https://azuremarketplace.microsoft.com)和 [Azure 门户](https://portal.azure.com/)中显示的套餐字段。

### <a name="preview-subscription-ids"></a>预览订阅 ID

发布套餐后你希望有权访问的 Azure 订阅 ID 列表。 通过这些列入允许列表的订阅，可以在推出套餐前测试预览的套餐。 通过合作伙伴门户，可以将多达 100 个订阅列入允许列表。

### <a name="suggested-categories"></a>建议的类别

从提供的列表中选择最多五个与产品/服务最相关的类别。 所选的类别用于将产品/服务映射到在 [Azure 市场](https://azuremarketplace.microsoft.com)和 [Azure 门户](https://portal.azure.com/)中提供的产品类别。

以下示例显示 Azure 市场和 Azure 门户中的市场信息。

**Azure 市场**


![publishvm10](./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png)


**Azure 门户**


![publishvm12](./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png)


### <a name="logo-guidelines"></a>徽标准则

遵循以下准则将徽标上传到云合作伙伴门户：

-   Azure 设计具有简单的调色板。 保持徽标上的主要和辅助颜色数较低。

-   Azure 门户的主题颜色为白色和黑色。 请避免将这两种颜色用作徽标的背景色。 使用可使徽标在 Azure 门户中突出显示的颜色。 建议使用简单的主颜色。

    >[!Note] 
    >如果使用透明背景，请确保徽标/文本不使用白色、黑色或蓝色。

-   不要在徽标上使用渐变背景。

-   避免在徽标上放置文本，即使是公司或品牌名称也不可以。  徽标的外观应平整，应避免渐变。

-   不应拉伸徽标。

#### <a name="hero-logo"></a>特大徽标

特大徽标是可选的。 发布者可以选择不上载特大徽标。 但是，上传徽标后无法将其删除。 合作伙伴必须遵循主图标的 Azure 市场准则。

#### <a name="guidelines-for-the-hero-logo-icon"></a>特大徽标图标的准则

-   发布者显示名称、计划标题和套餐详细摘要使用白色字体显示。 避免在背景中使用任何亮色。 主图标不允许使用黑色、白色和透明背景。

-   列出产品/服务后，发布者显示名称、计划标题、套餐详细摘要和创建按钮以编程方式嵌入在主徽标内。 设计主徽标时，请勿输入任何文本。 在徽标右侧保留空白空间。 此空间应为 415 x 100 像素，并与左侧保留 370 像素的间距。

![特大徽标示例](./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png)

### <a name="lead-management"></a>线索管理

若要配置产品/服务的潜在顾客管理设置，请遵循[这些说明](./cloud-partner-portal-get-customer-leads.md)。

## <a name="to-configure-support"></a>配置支持

使用“支持”视图提供以下信息：

- 公司的支持联系人，例如工程人员。
- 客户支持联系人。

## <a name="to-publish-the-offer"></a>发布套餐

最后一步是发布套餐。 请按照[这些说明以推出产品/服务](./cloud-partner-portal-make-offer-live-on-azure-marketplace.md)。
