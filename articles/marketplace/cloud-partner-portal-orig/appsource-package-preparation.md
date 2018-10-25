---
title: AppSource 包准备 | Microsoft Docs
description: 介绍如何准备和生成 AppSource 包。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 0b24c5d2f174c9a656e81d0c85e12b589d7d7799
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805749"
---
# <a name="appsource-package-preparation"></a>AppSource 包准备

除了 solution.zip 文件以外，还需要一个 **AppSource 包**。 此包是一个 .zip 文件，其中包含用于自动在客户 CRM 环境中部署解决方案的所有资产。 **AppSource 包**包含以下组件

* Package Deployer 的包
* 包含所用资产的 **Content_Types.xml** 文件
* 包含应用特定数据的 xml 文件
* 与列表一起显示在管理中心的 32x32 徽标
* 许可条款和隐私政策

以下步骤可帮助你创建 AppSource 包。

## <a name="a-create-a-package-for-the-package-deployer"></a>a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 创建 Package Deployer 的包

Package Deployer 的包是 AppSource 包的一部分。

若要创建 Package Deployer 的包，请参考以下说明：[https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx)。 完成后，该包将包括以下资产：

1. 包文件夹：包含所有解决方案、配置数据、平面文件和包的内容。 _注意：在以下示例中，我们假设包文件夹名为“PkgFolder”_
2. dll：该程序集包含包的自定义代码。 _注意：在以下示例中，我们假设此文件名为“MicrosoftSample.dll”。_

现在，需要创建名为 **Content_Types.xml** 的文件。此文件将列出包中的所有资产扩展。 下面是该文件的示例代码。

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

最后一步是将以下内容压缩到一个文件。 请此文件命名为 **package.zip**。 其中包含

1. PkgFolder（包括该文件夹中的所有内容）
2. dll
3. **Content_Types.xml**

创建 package.zip 的步骤：

1. 将包文件夹、**Content_Types.xml** 文件和 PackageName.dll 放到一个目录中。

![CRMScreenShot2](media/CRMScreenShot2.png)

2. 选择该文件夹中的所有项，单击右键并选择“发送到压缩(zip)文件夹”

![CRMScreenShot3](media/CRMScreenShot3.png)

3. 将名称更改为 package.zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. 创建 AppSource 包

AppSource 包需要几个附加的文件。

1. jpg（32x32 分辨率）
2. html（HTML 格式文件）
3. **Content_Types.xml**（同上）
4. xml

下面是 input.xml 的示例代码。 请参阅下表中的定义。

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>http://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**其中：**

|字段|详细信息|
|---|---|
|ProviderName|该解决方案由谁提供。 如果由 Microsoft 团队提供，则值应是 Microsoft。|
|PackageFile |与 content\_types.xml 文件压缩在一起的 Package Deployer 资产。 此 zip 文件应包含 Package Deployer 程序集，以及 Package Deployer 资产所在的文件夹。 即 package.zip|
|SolutionAnchorName |Package Deployer 中用于显示名称和解决方案资产说明的解决方案 zip 文件的名称。|
| StartDate| 这是解决方案包可供使用的日期。 格式为 MM/DD/YYYY|
|EndDate|这是解决方案包不再可供使用的日期。 格式为 MM/DD/YYYY |
|SupportedCountries |这是可以看到此包的国家/地区的逗号分隔列表。 请联系联机服务部门获取所有最新国家/地区代码的列表。 在撰写本文时，该列表为：AE,AL,AM,AO,AR,AT,AU,AZ,BA,BB,BD,BE,BG,BH,BM,BN,BO,BR,BY,CA,CH,CI,CL,CM,CO,CR,CV,CW,CY,CZ,DE,DK,DO,DZ,EC,EE,EG,ES,FI,FR,GB,GE,GH,GR,GT,HK,HN,HR,HU,ID,IE,IL,IN,IQ,IS,IT,JM,JO,JP,KE,KG,KN,KR,KW,KY,KZ,LB,LK,LT,LU,LV,LY,MA,MC,MD,ME,MK,MN,MO,MT,MU,MX,MY,NG,NI,NL,NO,NZ,OM,PA,PE,PH,PK,PL,PR,PS,PT,PY,QA,RO,RS,RU,RW,SA,SE,SG,SI,SK,SN,SV,TH,TM,TN,TR,TT,TW,UA,US,UY,UZ,VE,VI,VN,ZA,ZW |
|LearnMoreLink | 此包的详细信息页的 URL。 |
|Locales|此节点的一个实例，适用于你要在首选解决方案 UX 中支持的每种 UX 语言。 此节点包含描述每种语言的区域设置、徽标和条款的子级|
|区域设置：PackageLocale.Code|此节点的语言 LCID。 示例：“美国英语”为 1033|
|区域设置：PackageLocale.IsDefault|指示这是默认语言。 如果客户选择的 UX 语言不可用，则此语言将用作回退语言。|
|区域设置：徽标|这是要用于此包的徽标。 图标大小为 32x32。 允许的格式为 PNG 和 JPG|
|区域设置：条款：PackageTerm.File|这是包含许可条款的 HTML 文档的文件名。|

下面是徽标的显示位置：

![CRMScreenShot5](media/CRMScreenShot5.png)

最后一步是将以下内容压缩到一个文件。

1. zip（前面已创建）
2. **Content_Types.xml**
3. xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

将文件重命名为适合应用的名称。 最好是包括公司名称和应用名称。 例如：**_Microsoft_SamplePackage.zip**。
