---
title: Azure Active Directory B2C 自定义属性 | Microsoft Docs
description: 如何使用 Azure Active Directory B2C 中的自定义属性来收集有关使用者的信息。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 893dfbae96d2cfea01b1f281f888e9281bf582f9
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441910"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure Active Directory B2C：使用自定义属性来收集有关使用者的信息
Azure Active Directory (Azure AD) B2C 目录附带了一组内置信息（属性）：名、姓、市、邮编和其他属性。 但是，每个面向使用者的应用程序对于收集使用者的哪些属性具有独特的要求。 在 Azure AD B2C 中，可以扩展存储在每个使用者帐户上的属性集。 可以在 [Azure 门户](https://portal.azure.com/)中创建自定义属性，并将其用于注册策略，如下所示。 还可以使用 [Azure AD 图形 API](active-directory-b2c-devquickstarts-graph-dotnet.md) 读取和写入这些属性。

> [!NOTE]
> 自定义属性使用 [Azure AD 图形 API 目录架构扩展](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions)。
> 
> 

## <a name="create-a-custom-attribute"></a>创建自定义属性
1. [请按照以下步骤导航到 Azure 门户上的 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. 单击“用户属性”。
3. 单击边栏选项卡顶部的“+ 添加”。
4. 提供自定义属性的“名称”（例如，“ShoeSize”）和“说明”（可选） 。 单击“创建”。
   
   > [!NOTE]
   > 当前可用“数据类型”仅包括“String”、“Boolean”和“Int”。
   > 
   > 

现在自定义属性在“用户属性”列表中显示，以便用于注册策略。

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>在注册策略中使用自定义属性
1. [请按照以下步骤导航到 Azure 门户上的 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. 单击“注册策略”。
3. 单击注册策略（例如，“B2C_1_SiUp”）将其打开。 单击边栏选项卡顶部的“编辑”。
4. 单击“注册属性”，并选择自定义属性（例如，“ShoeSize”）。 单击“确定”。
5. 单击“应用程序声明”，并选择自定义属性。 单击“确定”。
6. 单击边栏选项卡顶部的“保存”。

可以使用策略上的“立即运行”功能来验证用户体验。 现在可以在使用者注册期间收集的属性的列表中看到“ShoeSize”，并在发送回应用程序的令牌中看到它。

## <a name="notes"></a>说明
* 除了注册策略，自定义属性还可用于登录策略和概况文件编辑策略。
* 自定义属性具有一个已知的限制。 它只有在任何策略中第一次使用时才创建，而不是将其添加到“用户属性”列表时。

