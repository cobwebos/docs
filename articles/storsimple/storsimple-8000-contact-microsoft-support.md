---
title: "为 StorSimple 8000 系列创建支持票证或案例 | Microsoft Docs"
description: "了解如何记录支持请求，以及如何在 StorSimple 8000 系列设备上启动支持会话。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: alkohli;
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 4b5a14237ce79100f980b2186b2c3c887abaa296
ms.contentlocale: zh-cn
ms.lasthandoff: 07/26/2017

---
# <a name="contact-microsoft-support"></a>联系 Microsoft 支持部门

StorSimple Device Manager 在“服务摘要”边栏选项卡中提供**记录新支持请求**的功能。 若在使用 StorSimple 解决方案时遇到问题，可以创建服务请求以获取技术支持。 在与支持工程师进行联机会话时，可能还需在 StorSimple 设备上启动支持会话。 本文介绍：

* 如何创建支持请求。
* 如何从门户内部管理支持请求生命周期。
* 如何在 StorSimple 设备的 Windows PowerShell 界面中启动支持会话。

在创建支持请求之前查看 [StorSimple 8000 系列支持 SLA 和信息](https://msdn.microsoft.com/library/mt433077.aspx)。

## <a name="create-a-support-request"></a>创建支持请求

根据[支持计划](https://azure.microsoft.com/support/plans/)，可以直接从 StorSimple 设备管理器服务摘要边栏选项卡针对 StorSimple 设备上的问题创建支持票证。 可以执行以下步骤来创建支持请求：

#### <a name="to-create-a-support-request"></a>创建支持请求

1. 转到 StorSimple Device Manager 服务。 在服务摘要边栏选项卡设置中，转到“支持 + 疑难解答”，然后单击“新建支持请求”。
     
    ![通过新门户联系 MS 支持部门](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. 在“新建支持请求”边栏选项卡上，选择“基本”。 在“基本”边栏选项卡中执行以下步骤：
   1. 从“颁发类型”下拉列表中，选择“技术”。
   2. 将自动选择当前**订阅**、**服务**类型和**资源**（StorSimple Device Manager 服务）。 
   3. 如果有多个计划与订阅相关联，请从下拉列表中选择一个**支持计划**。 需要付费的支持计划才能启用技术支持。
   4. 单击“下一步”。

       ![通过新门户联系 MS 支持部门](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. 在“新建支持请求”边栏选项卡中，选择“步骤 2: 问题”。 在“问题”边栏选项卡中执行以下步骤：
    
    1. 选择“严重性”。
    2. 指定问题是否与设备或 StorSimple 设备管理器服务相关。
    3. 选择此问题的**类别**，并提供有关此问题的更多**详细信息**。
    4. 提供问题的开始日期和时间。
    5. 在“文件上传”中单击文件夹图标，浏览到支持包。
    6. 选中“共享诊断信息”。
    7. 单击“下一步”。

       ![通过新门户联系 MS 支持部门](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. 在“新建支持请求”边栏选项卡中，单击“步骤 3: 联系人信息”。 在“联系人信息”边栏选项卡中，执行以下步骤：

    1. 在“联系人选项”中，提供首选的联系方式（电话或电子邮件）和语言。 系统会根据订阅计划自动选择响应时间。
    2. 在“联系人信息”中提供姓名、电子邮件、可选联系人和国家/地区。 选中“保存联系人更改以便将其用于将来的支持请求”复选框。
    3. 单击“创建” 。
   
        ![通过新门户联系 MS 支持部门](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

    Microsoft 支持将使用此信息与你联系，以获得进一步信息、诊断和解决方法。
提交请求后，支持工程师会与你尽早联系，以便处理你的请求。

## <a name="manage-a-support-request"></a>管理支持请求

创建支持请求之后，您可以在门户内管理请求的生命周期。

#### <a name="to-manage-your-support-requests"></a>管理支持请求

1. 若要访问“帮助和支持”页，请导航到“浏览”>“帮助 + 支持”。

    ![管理支持请求](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. “帮助 + 支持”边栏选项卡中会显示“所有支持请求”的表格列表。

    ![管理支持请求](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. 选择并单击某个支持请求。 可以查看此请求的状态和详细信息。 如果想要跟进此请求，请单击“+ 新建消息”。

    ![管理支持请求](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>在 Windows PowerShell for StorSimple 中启动支持会话

若要解决在 StorSimple 设备上可能会遇到的问题，需要与 Microsoft 支持团队合作。 Microsoft 支持团队可能需要使用支持会话登录到你的设备。

可以执行以下步骤来启动支持会话：

#### <a name="to-start-a-support-session"></a>启动支持会话

1. 直接使用串行控制台或来自远程计算机的 Telnet 会话访问设备。 为此，请按[使用 PuTTY 连接到设备串行控制台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)中的步骤操作。
2. 在打开的会话中，按 **Enter** 键启动命令提示符。
3. 在串行控制台菜单中，选择选项 1“使用完整访问权限登录”。
4. 在提示符处键入以下密码：
   
    `Password1`
5. 在提示符处键入以下命令：
   
    `Enable-HcsSupportAccess`
6. 此时会显示加密的字符串。 将此字符串复制到文本编辑器（如记事本）中。
7. 保存此字符串，通过电子邮件将其发送给 Microsoft 技术支持部门。

> [!IMPORTANT]
> 运行 `Disable-HcsSupportAccess` 即可禁用支持访问。 StorSimple 设备也会在启动会话 8 小时后尝试禁用支持访问。 最好是在启动支持会话后更改 StorSimple 设备凭据。


## <a name="next-steps"></a>后续步骤

了解如何[诊断并解决与 StorSimple 8000 系列设备相关的问题](storsimple-troubleshoot-deployment.md)

