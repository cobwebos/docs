---
title: JavaScript 和页面协定版本 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 中启用 JavaScript 和使用页面协定版本。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ef474bec71a9015209b5748b6947816002bd4a5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66511975"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的 JavaScript 和页面协定版本

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C 提供一组打包的内容，其中包含的 HTML、CSS 和 JavaScript 适用于用户流中的用户界面元素和自定义策略。 若要为应用程序启用 JavaScript，必须向[自定义策略](active-directory-b2c-overview-custom.md)添加一个元素，或者在门户中为用户流启用它，选择页面协定，并在请求中使用 [b2clogin.com](b2clogin.md)。

如果你打算启用 [JavaScript](javascript-samples.md) 客户端代码，则需要确保 JavaScript 所基于的元素是不可变的。 否则，任何更改都可能会导致用户页上出现意外行为。 为了防止这些问题，可以强制使用页面协定，并指定页面协定版本。 这样做可以确保 JavaScript 所基于的所有内容定义不可变。 即使不打算启用 JavaScript，也可为页面指定页面协定版本。

## <a name="user-flows"></a>用户流

在用户流属性中，可以启用 JavaScript，这样也会强制使用页面协定。 然后就可以设置页面协定版本，如下一部分所述。

![启用 JavaScript 设置](media/user-flow-javascript-overview/javascript-settings.png)

不管是否在用户流的属性中启用 JavaScript，都可以为用户流页面指定页面协定版本。 打开用户流，然后选择“页面布局”  。 在“布局名称”下选择一个用户流页面，然后选择“页面协定版本”。  

![启用 JavaScript 设置](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>自定义策略

若要在自定义策略中启用 JavaScript，请将 **ScriptExecution** 元素添加到自定义策略文件中的 **RelyingParty** 元素。 有关详细信息，请参阅[在 Azure Active Directory B2C 中使用的 JavaScript 示例](javascript-samples.md)。

不管是否在自定义策略中启用 JavaScript，都可以为页面指定页面协定版本。 有关指定页面协定的详细信息，请参阅[使用自定义策略在 Azure Active Directory B2C 中选择页面协定](page-contract.md)。

## <a name="next-steps"></a>后续步骤

请参阅[用于 Azure Active Directory B2C 中的 JavaScript 示例](javascript-samples.md)。
