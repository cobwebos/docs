---
title: include 文件
description: include 文件
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2019
ms.custom: include file
ms.openlocfilehash: 832c0e6080b82f3c38beaf051669fbdacd37081c
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74100706"
---
>[!NOTE]
>本部分提供有关 [Azure AD 应用注册](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)的说明。

1. 在 [Azure 门户](https://portal.azure.com)中，从可展开的左菜单打开“Azure Active Directory”  ，然后打开“应用注册”窗格  。 

    [![选择“Azure Active Directory”窗格](./media/digital-twins-permissions/select-aad-pane.png)](./media/digital-twins-permissions/select-aad-pane.png#lightbox)

1. 选择“+ 新建注册”  按钮。

    [![选择“新建注册”按钮](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. 在“名称”  框中，为此应用注册提供一个友好名称。 在“重定向 URI (可选)”  部分下，在左侧下拉菜单中选择“公共客户端/本机(移动和桌面)”  ，然后在右侧文本框中输入 `https://microsoft.com`。 选择“注册”  。

    [![“创建”窗格](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. 若要确保[将应用注册为“公共客户端”](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)  ，请打开用于应用注册的“身份验证”  窗格，然后在该窗格中向下滚动。 在“默认客户端类型”部分中，为“将应用程序视为公共客户端”选择“是”，然后点击“保存”     。

    选中“访问令牌”以启用 Manifest.json 中的“oauth2AllowImplicitFlow”设置   。

    [![公共客户端配置设置](./media/digital-twins-permissions/aad-public-client.png)](./media/digital-twins-permissions/aad-public-client.png#lightbox)

1.  打开已注册的应用的“概述”  窗格，然后将以下实体的值复制到临时文件。 在以下部分中，将使用这些值配置示例应用程序。

    - **应用程序(客户端) ID**
    - **目录(租户) ID**

    [![Azure Active Directory 应用程序 ID](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. 打开用于应用注册的“API 权限”窗格  。 选择“+ 添加权限”按钮  。 在“请求 API 权限”  窗格中，选择“我的组织使用的 API”  选项卡，然后搜索以下任意一项：
    
    1. `Azure Digital Twins`。 选择 **Azure 数字孪生** API。

        [![搜索“API”或“Azure 数字孪生”](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. 或者，搜索 `Azure Smart Spaces Service`。 选择“Azure 智能空间服务”  API。

        [![搜索 Azure 智能空间的 API](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > 将显示的 Azure AD API 名称和 ID 取决于租户：
    > * 测试租户和客户帐户应搜索 `Azure Digital Twins`。
    > * 其他 Microsoft 帐户应搜索 `Azure Smart Spaces Service`。

1. 所选的 API 在同一个“请求 API 权限”窗格中显示为“Azure 数字孪生”   。 选择“读取”下拉选项  ，然后选中“Read.Write”复选框  。 选择“添加权限”按钮  。

    [![添加 API 权限](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. 根据组织的设置，可能需要执行其他步骤才能授予对此 API 的管理员访问权限。 请联系管理员以了解详细信息。 在该管理员访问权限得到批准后，“API 权限”窗格中的“需要管理员同意”列将显示你的权限   。 

    [![管理员同意审批](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    验证是否显示“Azure 数字孪生”  。
