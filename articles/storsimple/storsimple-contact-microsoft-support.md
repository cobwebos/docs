---
title: "记录 StorSimple 8000 系列的支持票证 | Microsoft 文档"
description: "了解如何创建支持请求，以及如何在 StorSimple 设备上启动支持会话。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2ebc20fe-f490-4749-8e43-c9fac86f1676
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2016
ms.author: alkohli;anbacker
translationtype: Human Translation
ms.sourcegitcommit: ce19be3454c08bbc041b5f6929c4ad56ffe9b7ea
ms.openlocfilehash: 5f8124371391f7e2e59bbea474bef1da77686e0c


---
# <a name="contact-microsoft-support"></a>联系 Microsoft 支持部门
若在使用 Microsoft Azure StorSimple 解决方案时遇到问题，可以创建服务请求以获取技术支持。 在与支持工程师进行联机会话时，可能还需在 StorSimple 设备上启动支持会话。 本文介绍：

* 如何创建支持请求。
* 如何在 StorSimple 设备的 Windows PowerShell 界面中启动支持会话。

在创建支持请求之前查看 [StorSimple 8000 系列支持 SLA 和信息](https://msdn.microsoft.com/library/mt433077.aspx)。

## <a name="create-a-support-request"></a>创建支持请求
可以执行以下步骤来创建支持请求：

#### <a name="to-create-a-support-request"></a>创建支持请求
1. 在“Azure 经典门户”的右上角单击帐户名称，然后单击“联系 Microsoft 支持部门”。[](https://manage.windowsazure.com/)
   
    ![通过管理门户联系 MS 支持部门](./media/storsimple-contact-microsoft-support/Ibiza1.png)
2. 系统会将用户重定向到新的 Azure 门户 (portal.azure.com)。 单击“新建支持请求”磁贴。
   
    ![通过新门户联系 MS 支持部门](./media/storsimple-contact-microsoft-support/Ibiza2.png)
   
    在屏幕右侧会显示“新建支持请求”窗格。 
   
    ![“新建支持请求”窗格](./media/storsimple-contact-microsoft-support/Ibiza3a.png)
3. 在“基本信息”对话框中，完成以下操作：                                
   
   1. 从“颁发类型”下拉列表中，选择“技术”。
   2. 从下拉列表中选择“订阅”。
   3. 从“服务”下拉列表中，选择“StorSimple”。 
   4. 从下拉列表中选择“支持计划”。 需要付费的支持计划才能启用技术支持。
4. 单击“资源组名称” 的 Azure 数据工厂。 此时会显示“问题”对话框。
   
    ![“新建支持请求”窗格](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 
5. 在“问题”对话框中，完成以下操作：
   
   1. 从下拉列表中选择“严重性”。
   2. 从下拉列表中选择“问题类型”。
   3. 从下拉列表中选择“类别”。 
   4. 在“详细信息”框中简要描述问题。
   5. 在“时间范围”框中，指示与问题的最新匹配项对应的日期、时间和时区。
   6. 在“文件上载”下单击文件夹图标，浏览到支持包。
   7. 选择“共享诊断信息”复选框。
6. 单击“下一步”。 此时会出现“联系信息”对话框。
   
    ![“新建支持请求”窗格](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 
7. 输入联系人信息并选择联系方式（电话或电子邮件）。 
8. 选中“保存联系人更改以便将其用于将来的支持请求”复选框。
9. 单击“创建” 。

提交请求后，支持工程师会与你尽早联系，以便处理你的请求。

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
> 
> 




<!--HONumber=Jan17_HO4-->


