---
title: 更改双因素验证方法和设置 - Azure 活动目录
description: 了解如何从"其他安全验证"页更改工作或学校帐户的安全验证方法和设置。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: curtand
ms.openlocfilehash: 5949f04ecc28a88e340a9c2de530031793f193a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253242"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>更改双因素验证方法和设置

为工作或学校帐户设置安全验证方法后，您可以更新任何相关详细信息，包括：

- 默认安全验证方法

- 安全验证方法详细信息，如电话号码

- 身份验证器应用设置或从身份验证器应用中删除设备

## <a name="using-the-additional-security-verification-page"></a>使用"其他安全验证"页

如果您的组织提供了有关如何打开和管理双因素验证的具体步骤，则应首先按照这些说明操作。 否则，您可以通过["其他安全验证](https://docs.microsoft.com/azure/active-directory/user-help/multi-factor-authentication-end-user-first-time)"页访问安全验证方法设置。

>[!Note]
>如果您在屏幕上看到的内容与本文中介绍的内容不匹配，则意味着管理员已打开**安全信息（预览）** 体验，或者您的组织提供了您自己的自定义门户。 有关新的安全信息体验的详细信息，请参阅[安全信息（预览）概述](user-help-security-info-overview.md)。 有关组织自定义门户的详细信息，您必须联系组织的帮助台。

### <a name="to-get-to-the-additional-security-verification-page"></a>访问"其他安全验证"页

您可以按照此链接访问["其他安全验证"页](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)。

![其他安全验证页面，包含可用安全验证方法详细信息](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

您还可以按照以下步骤访问 **"其他安全验证**"页：

1. 登录到[https://myapps.microsoft.com](https://myapps.microsoft.com)。

1. 在右上方选择帐户名称，并选择“配置文件”****。

1. 选择**其他安全验证**。  

    ![我的应用链接到"其他安全验证"页](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>有关使用**附加安全验证**页**的应用密码**部分的信息，请参阅[管理应用密码进行双重验证](multi-factor-authentication-end-user-app-passwords.md)。 应用密码应仅用于不支持双重验证的应用。

## <a name="change-your-default-security-verification-method"></a>更改默认安全验证方法

使用用户名和密码登录到工作或学校帐户后，将自动显示您选择的安全验证方法。 根据组织的要求，这可以是通过身份验证器应用、短信或电话呼叫的通知或验证码。

如果您决定要更改正在使用的默认安全验证方法，可以在此处执行此操作。

### <a name="to-change-your-default-security-verification-method"></a>更改默认安全验证方法

1. 在 **"其他安全验证**"页中，从 **"首选选项列表"** 中选择要使用的方法。 您将看到所有选项，但只能选择组织为您提供的选项。

    - **通过应用程序通知我**：您将通过您的验证器应用程序通知您，您有等待验证提示。

    - **拨打我的身份验证电话**：您将在移动设备上接到一个电话，要求您验证您的信息。

    - **文本代码到我的身份验证电话**：你会得到一个验证码作为短信的一部分在您的移动设备上。 您必须在工作或学校帐户的验证提示中输入此代码。

    - **打电话给我的办公室电话**：你会接到你的办公室电话，要求您核实你的信息。

    - **使用来自应用的验证码**：您将使用身份验证器应用获取验证码，您将从工作或学校帐户中键入提示。

2. 选择“保存”。****

## <a name="add-or-change-your-phone-number"></a>添加或更改电话号码

您可以在 **"其他安全验证**"页添加新电话号码或更新现有号码。

>[!Important]
>我们强烈建议您添加辅助电话号码，以帮助防止在主电话丢失或被盗或新手机丢失或被盗时被锁定在帐户之外，或者如果您收到新手机，并且不再有原始主电话号码。

### <a name="to-change-your-phone-numbers"></a>更改电话号码

1. 从"您希望**如何响应？"** **"附加安全验证**"页面部分中，更新**身份验证电话**（主要移动设备）和**Office 电话**的电话号码信息。

1. 选择 **"备用身份验证电话"** 选项旁边的框，然后键入辅助电话号码，如果无法访问主设备，可以接收短信或电话呼叫。

1. 选择“保存”。****

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>向 Microsoft 身份验证器应用添加新帐户

您可以在[适用于 Android](https://play.google.com/store/apps/details?id=com.azure.authenticator)或[iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)的 Microsoft 身份验证器应用程序中设置您的工作或学校帐户。

如果您已在 Microsoft 身份验证器应用中设置了工作或学校帐户，则无需再次执行此操作。

1. 在"**您希望如何响应"** 部分"**附加安全验证**"页面中，选择 **"设置身份验证器应用**"。

    ![在 Microsoft 身份验证器应用中设置您的工作或学校帐户](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. 按照屏幕上的说明操作，包括使用移动设备扫描 QR 码，然后选择 **"下一步**"。

    系统将要求您通过 Microsoft 身份验证器应用批准通知，以验证您的信息。

1. 选择“保存”。****

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>从 Microsoft 身份验证器应用中删除您的帐户或设备

可以从 Microsoft 身份验证器应用中删除您的帐户，也可以从工作或学校帐户中删除设备。 通常，您删除设备以永久删除帐户中丢失、被盗或旧设备，然后删除帐户以尝试修复某些连接问题或解决帐户更改（如新用户名）。

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>从工作或学校帐户中删除您的设备

1. 在"要**如何响应"** 部分"**附加安全验证**"部分中，选择"**设置身份验证器应用**"按钮。

1. 选择“保存”。****

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>从 Microsoft 身份验证器应用中删除您的帐户

从 Microsoft 身份验证器应用中，选择要删除的设备旁边的 **"删除**"按钮。

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>在受信任的设备上打开双因素验证提示

根据组织的设置，您可能会看到一个复选框，该复选框显示，当您在浏览器上执行双重验证时 **，不要再次询问 X 天**。 如果您选择了此选项来阻止双因素验证提示，然后您丢失了设备或您的设备可能受到威胁，则应重新打开双因素验证提示以帮助保护您的帐户。 您必须同时打开所有设备的提示。 遗憾的是，您无法仅为特定设备重新打开提示。

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>要为设备打开双因素验证提示

在[**"附加安全验证**"页](#to-get-to-the-additional-security-verification-page)中，选择 **"还原以前受信任的设备上的多重身份验证**"。 下次在任何设备上登录时，系统将提示您执行双重验证。

## <a name="next-steps"></a>后续步骤

添加或更新双因素验证设置后，可以管理应用密码、登录或获取有关某些常见的双因素验证相关问题的帮助。

- [管理应用密码，以便](multi-factor-authentication-end-user-app-passwords.md)对不支持双因素验证的任何应用进行双重验证。

- [如何使用双因素验证登录](multi-factor-authentication-end-user-signin.md)

- [通过双因素验证解决常见问题](multi-factor-authentication-end-user-troubleshoot.md)
