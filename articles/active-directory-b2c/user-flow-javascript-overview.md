---
title: JavaScript 和页协定版本-Azure Active Directory B2C |Microsoft Docs
description: 了解如何启用 JavaScript 和 Azure Active Directory B2C 中使用页协定版本。
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
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511975"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的 JavaScript 和页协定版本

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C 提供一组包含 HTML、 CSS 和 JavaScript 的用户流和自定义策略中的用户界面元素的打包内容。 若要为应用程序中启用 JavaScript，您必须将元素添加到您[自定义策略](active-directory-b2c-overview-custom.md)或启用的门户中的用户流，选择页面协定，再使用[b2clogin.com](b2clogin.md)在请求中。

如果你想要启用[JavaScript](javascript-samples.md)客户端代码中，您将希望确保您正在基于 JavaScript 的元素是固定不变。 否则，任何更改可能导致意外的行为上用户页。 若要防止这些问题，可以强制使用页协定并指定页协定版本。 这样做可确保您已基于 JavaScript 的所有内容定义是不可变。 即使不打算启用 JavaScript，可为您的页面来指定页协定版本。

## <a name="user-flows"></a>用户流

在用户流属性中，可以启用 JavaScript，这样也会强制使用页面协定。 然后就可以设置页面协定版本，如下一部分所述。

![启用 JavaScript 设置](media/user-flow-javascript-overview/javascript-settings.png)

不管是否在用户流的属性中启用 JavaScript，都可以为用户流页面指定页面协定版本。 打开用户流，然后选择“页面布局”  。 在“布局名称”下选择一个用户流页面，然后选择“页面协定版本”。  

![启用 JavaScript 设置](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>自定义策略

若要自定义策略中启用 JavaScript，您将添加**ScriptExecution**元素**RelyingParty**自定义策略文件中的元素。 有关详细信息，请参阅[以便在 Azure Active Directory B2C 中使用 JavaScript 示例](javascript-samples.md)。

自定义策略中启用 JavaScript，您可以为您的页面指定页协定版本。 有关指定页协定的详细信息，请参阅[选择使用自定义策略的 Azure Active Directory B2C 中的页协定](page-contract.md)。

## <a name="next-steps"></a>后续步骤

请参阅[用于 Azure Active Directory B2C 中的 JavaScript 示例](javascript-samples.md)。
