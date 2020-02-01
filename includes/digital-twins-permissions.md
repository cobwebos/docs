---
title: include 文件
description: include 文件
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/23/2020
ms.custom: include file
ms.openlocfilehash: a1576e4a97af5de0b936c662de636aae542a19b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748748"
---
>[!NOTE]
>本部分提供有关 [Azure AD 应用注册](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)的说明。

1. 在 [Azure 门户](https://portal.azure.com)中，从可展开的左菜单打开“Azure Active Directory”  ，然后打开“应用注册”窗格  。 

    [![选择“Azure Active Directory”窗格](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. 选择“+ 新建注册”  按钮。

    [![选择“新建注册”按钮](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. 在“名称”  框中，为此应用注册提供一个友好名称。 在“重定向 URI (可选)”  部分下，在左侧下拉菜单中选择“公共客户端/本机(移动和桌面)”  ，然后在右侧文本框中输入 `https://microsoft.com`。 选择“注册”  。

    [![“创建”窗格](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. 若要确保[将应用注册为“公共客户端”](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)  ，请打开用于应用注册的“身份验证”  窗格，然后在该窗格中向下滚动。 在“默认客户端类型”部分中，为“将应用程序视为公共客户端”选择“是”，然后点击“保存”     。

    1. “重定向 URI”  必须与身份验证请求所提供的地址相匹配：

        * 对于本地开发环境中托管的应用，请选择“公共客户端(移动和桌面)”  。 确保将“默认客户端类型”设置为“是”  。
        * 对于 Azure 应用服务上托管的单页应用，请选择“Web”  。

        选择“公共客户端(移动和桌面)”，然后输入 `http://localhost:8080/`。 

        [![配置重定向 URI](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    1. 勾选“访问令牌”  ，在资源的**清单** JSON 中将 **oauth2AllowImplicitFlow** 设置配置为 `true`。

        [![公共客户端配置设置](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1.  打开已注册的应用的“概述”  窗格，然后将以下实体的值复制到临时文件。 在以下部分中，将使用这些值配置示例应用程序。

    - **应用程序(客户端) ID**
    - **目录(租户) ID**

    [![Azure Active Directory 应用程序 ID](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. 打开用于应用注册的“API 权限”窗格  。 选择“+ 添加权限”按钮  。 在“请求 API 权限”  窗格中，选择“我的组织使用的 API”  选项卡，然后搜索以下任意一项：
    
    1. `Azure Digital Twins` 列中的一个值匹配。 选择 **Azure 数字孪生** API。

        [![搜索“API”或“Azure 数字孪生”](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. 或者，搜索 `Azure Smart Spaces Service`。 选择“Azure 智能空间服务”  API。

        [![搜索 Azure 智能空间的 API](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > 将显示的 Azure AD API 名称和 ID 取决于租户：
    > * 测试租户和客户帐户应搜索 `Azure Digital Twins`。
    > * 其他 Microsoft 帐户应搜索 `Azure Smart Spaces Service`。

1. 选择后，任一 API 会在同一个“请求 API 权限”窗格中显示为“Azure 数字孪生”   。 选择“读取”下拉选项  ，然后选中“Read.Write”复选框  。 选择“添加权限”按钮  。

    [![添加 API 权限](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. 根据组织的设置，可能需要执行其他步骤才能授予对此 API 的管理员访问权限。 请联系管理员以了解详细信息。 在该管理员访问权限得到批准后，“API 权限”窗格中的“需要管理员同意”列将显示你的权限   。 

    [![管理员同意审批](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    验证是否显示“Azure 数字孪生”  。
