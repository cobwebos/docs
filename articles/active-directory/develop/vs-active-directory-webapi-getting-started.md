---
title: "Visual Studio WebApi 项目中的 Azure AD 入门 | Microsoft 文档"
description: "通过 Visual Studio 连接服务连接到或创建 Azure AD 之后，如何在 WebApi 项目中开始使用 Azure Active Directory"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: kraigb
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 333db54fe01aad42cfcd050995b64f3725b31ae9
ms.contentlocale: zh-cn
ms.lasthandoff: 03/21/2017

---
# <a name="get-started-with-azure-active-directory-and-visual-studio-connected-services-webapi-projects"></a>开始使用 Azure Active Directory 和 Visual Studio 连接服务（WebApi 项目）
> [!div class="op_single_selector"]
> * [入门](vs-active-directory-webapi-getting-started.md)
> * [发生了什么情况](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="requiring-authentication-to-access-controllers"></a>访问控制器需要身份验证
你项目中的所有控制器均带有 **Authorize** 属性。 此属性要求用户先进行身份验证，然后才能访问由这些控制器定义的 API。 若要允许匿名访问控制器，请从控制器删除此属性。 如果您想要更详细地设置这些权限，请将该属性应用到需要身份验证的每个方法，而不是将它应用到控制器类。

## <a name="next-steps"></a>后续步骤
[详细了解 Azure Active Directory](https://azure.microsoft.com/services/active-directory/)


