---
title: 获取语音设备 SDK
description: 了解如何获取语音设备 SDK 的访问权限。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: f70b41cd7e3a7a6eddf32ae6ad024fa9ac040f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281776"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>获取认知服务语音设备 SDK

## <a name="requesting-access"></a>请求访问权限

语音设备 SDK 提供受限预览版，要求注册该计划。 目前，Microsoft 首选大型公司作为访问此产品的候选人。

若要获取对语音设备 SDK 的访问权限，请执行下列步骤：

1. 转到 Microsoft 语音设备 SDK [注册表单](https://aka.ms/sdsdk-signup)。
1. 阅读[许可协议](speech-devices-sdk-license.md)。
1. 如果同意此许可协议的条款，请选择“我同意”。
1. 回答表单中的问题。
1. 提交表单。 
1. 如果你的电子邮件地址不在 Azure Active Directory 中，则会在批准后收到邀请电子邮件，如下所示。 如果电子邮件地址已在 Azure Active Directory 中，批准后将收到 Microsoft 语音团队的电子邮件，可直接跳到[下载语音设备 SDK](#download-the-speech-devices-sdk)。

## <a name="approval-e-mail"></a>批准电子邮件

```
From: Microsoft Speech Team from Microsoft (via Microsoft) <invites@microsoft.com> 
Subject: You're invited to the Microsoft organization 
```

![电子邮件](media/speech-devices-sdk/get-sdk-1.png)

## <a name="accept-access"></a>接受访问
执行以下步骤，使用注册期间提供的电子邮件地址加入 Azure Active Directory。 此过程可授予访问语音设备 SDK [下载站点](https://shares.datatransfer.microsoft.com/)的权限。

1. 在收到的电子邮件中单击“开始”。 如果你所在组织已是 Office 365 客户，系统会提示登录并且可直接跳到步骤 8。

2. 在启动的浏览器窗口中单击“下一步”。

    ![身份验证窗口](media/speech-devices-sdk/get-sdk-2.png)

3. 创建 Microsoft 帐户（如果还没有帐户）。 输入电子邮件地址，与上面步骤 6 收到邀请的电子邮件相同。

    ![创建 Microsoft 帐户](media/speech-devices-sdk/get-sdk-3.png)

4. 单击“下一步”创建密码。

5. 系统提示验证电子邮件时，请返回电子邮件收件箱获取收到的验证码。
 
7. 在对话框中粘贴或键入电子邮件中的安全代码。 此示例中为“8406”。 然后单击“下一步”。

    ![验证电子邮件](media/speech-devices-sdk/get-sdk-6.png)
 
8. 在浏览器窗口中看到“访问面板应用程序”时，则可确认电子邮件地址（步骤 6 的电子邮件地址）已在 Azure Active Directory 中。 现已获得语音设备 SDK 下载站点的访问权限。

## <a name="download-the-speech-devices-sdk"></a>下载语音设备 SDK

转到[语音设备 SDK 下载站点](https://shares.datatransfer.microsoft.com/)，使用前面创建的 Microsoft 帐户登录。 通过执行以下步骤，现在可以下载语音设备 SDK、相关示例代码和参考材料。

![SDK 下载站点](media/speech-devices-sdk/get-sdk-7.png)

1. 按照浏览器的提示，下载并安装 Aspera Connect 工具。

    ![下载 Aspera Connect](media/speech-devices-sdk/get-sdk-8.png)
 
1. 单击“是”切换到 Aspera Connect。

    ![切换到 Aspera Connect](media/speech-devices-sdk/get-sdk-9.png)
 
1. 单击“允许”，确认使用 Aspera Connect 下载文件。

    ![使用 Aspera Connect 下载](media/speech-devices-sdk/get-sdk-10.png)
 
1. 下载文件后，关闭 Aspera Connect 的传输窗口。

    ![Aspera Connect 的传输窗口](media/speech-devices-sdk/get-sdk-11.png)
 
默认情况下，文件会下载到“下载”文件夹。 现在可以退出此站点。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [语音设备 SDK 入门](speech-devices-sdk-qsg.md)
