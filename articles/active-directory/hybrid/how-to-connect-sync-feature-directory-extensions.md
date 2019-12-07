---
title: Azure AD Connect 同步：目录扩展 | Microsoft Docs
description: 本主题介绍 Azure AD Connect 中的目录扩展功能。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 138ca9bf3352c46b8ac495b58a2fd6d7bafeb658
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889791"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 同步：目录扩展
通过目录扩展，可以使用本地 Active Directory 中的属性扩展 Azure AD 中的架构。 此功能允许使用继续在本地管理的属性来构建 LOB 应用。 可以通过 [Azure AD Graph API 目录扩展](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)或 [Microsoft Graph](https://developer.microsoft.com/graph/) 使用这些属性。 可以使用 [Azure AD Graph 资源管理器](https://graphexplorer.azurewebsites.net/)和 [Microsoft Graph 资源管理器](https://developer.microsoft.com/graph/graph-explorer)查看可用属性。 你还可以使用此功能在 Azure AD 中创建动态组。

目前，没有任何 Office 365 工作负荷使用这些属性。

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>自定义要与 Azure AD 同步的属性

在安装向导的自定义设置路径中配置要同步的其他属性。

>[!NOTE]
>“可用属性”框区分大小写。

![架构扩展向导](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

安装显示以下属性，它们是有效的候选项：

* “用户”和“组”对象类型
* 单值属性：String、Boolean、Integer、Binary
* 多值属性：String、Binary


>[!NOTE]
> 虽然 Azure AD Connect 支持将多值 Active Directory 属性作为多值目录扩展同步到 Azure AD，但目前无法检索/使用在多值目录扩展属性中上传的数据。

属性列表是从安装 Azure AD Connect 期间创建的架构缓存中读取的。 如果已使用附加属性扩展了 Active Directory 架构，则必须[刷新架构](how-to-connect-installation-wizard.md#refresh-directory-schema)，然后这些新属性才可见。

Azure AD 中的对象最多可以有 100 个目录扩展属性。 最大长度为 250 个字符。 如果属性值更长，则同步引擎会将其截断。

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>向导 Azure AD 所做的配置更改

在安装 Azure AD Connect 期间，会注册可以使用这些属性的应用程序。 可以在 Azure 门户中看到此应用程序。 其名称始终是**租户架构扩展应用程序**。

![架构扩展应用](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

请确保选择 "**所有应用程序**" 以查看此应用。

属性以**扩展名 \_{ApplicationId}\_** 为前缀。 ApplicationId 的 Azure AD 租户中的所有属性都具有相同的值。 对于本主题中的所有其他方案，你将需要此值。

## <a name="viewing-attributes-using-graph"></a>使用图形查看属性

现在可以通过 Azure AD Graph API 使用这些属性。 可以使用 [Azure AD Graph 资源管理器](https://graphexplorer.azurewebsites.net/)查询它们。

![Azure AD Graph 资源管理器](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

另外，也可以使用 [Microsoft Graph 资源管理器](https://developer.microsoft.com/graph/graph-explorer#)通过 Microsoft Graph API 查询属性。

>[!NOTE]
> 在 Microsoft Graph 中，需要请求返回属性。 按如下所示显式选择属性： https\://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com？ $select = extension_9d98ed114c4840d298fad781915f27e4_employeeID，extension_9d98ed114c4840d298fad781915f27e4_division。
>
> 有关详细信息，请参阅 [Microsoft Graph: Use query parameters](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter)（Microsoft Graph：使用查询参数）。

## <a name="use-the-attributes-in-dynamic-groups"></a>使用动态组中的属性

一个更有用的方案是在动态安全或 Office 365 组中使用这些属性。

1. 在 Azure AD 中创建一个新组。 为其提供一个良好的名称，并确保**成员身份类型**为 "**动态用户**"。

   ![使用新组的屏幕截图](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. 选择以**添加动态查询**。 如果查看属性，则不会看到这些扩展属性。 需要首先添加它们。 单击 "**获取自定义扩展属性**"，输入应用程序 ID，然后单击 "**刷新属性**"。

   ![添加了目录扩展的屏幕截图](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. 打开属性下拉，请注意，添加的属性现在可见。

   ![新属性显示在 UI 中的屏幕截图](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   完成表达式以满足你的要求。 在我们的示例中，将规则设置为 **（user. extension_9d98ed114c4840d298fad781915f27e4_division-eq "销售和营销"）** 。

4. 创建组后，为 Azure AD 提供一段时间来填充成员，然后查看成员。

   ![包含动态组中成员的屏幕截图](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>后续步骤
了解有关 [Azure AD Connect 同步](how-to-connect-sync-whatis.md)配置的详细信息。

了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
