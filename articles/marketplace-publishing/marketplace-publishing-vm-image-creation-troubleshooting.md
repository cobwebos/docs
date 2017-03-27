---
title: "如何在创建 VHD 期间对常见问题进行故障排除 | Microsoft Docs"
description: "创建 VHD 时常见疑难解答问题和难题的答案。"
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: 
editor: 
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: hascipio; v-divte
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3acef7399d5e8757ebad3d6178b22eb2c5c68660


---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>如何对 VHD 创建期间遇到的常见问题进行故障排除
提供本文的目的是在发布或管理虚拟机解决方案时，帮助可能遇到问题或有常见问题的 Azure 应用商店发布者和/或联合管理员。

1. 如何更改主机名称？
   
    创建 VM 后，用户无法更新主机名称。
2. 如何重置远程桌面服务或其登录密码？
   
   * [用于 Windows VM 的参考](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [用于 Linux VM 的参考](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. 如何生成新 ssh 证书？
   
   请参阅以下链接：[https://azure.microsoft.com/zh-cn/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. 如何配置开放式的 VPN 证书？
   
   请参阅以下链接：[https://azure.microsoft.com/zh-cn/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/en-us/documentation/articles/vpn-gateway-point-to-site-create/)
5. 在 Microsoft Azure 虚拟机环境中运行 Microsoft 服务器软件（基础结构即服务）的支持策略是什么？
   
   请参阅以下链接：[https://support.microsoft.com/zh-cn/kb/2721672](https://support.microsoft.com/en-us/kb/2721672)
6. 虚拟机有唯一标识符吗？
   
   Azure 在每台 VM 中为 Azure VM 唯一 ID 编码。 请参阅此博客和文档中的详细信息。
7. 在 VM 中如何管理启动任务中的自定义脚本扩展？
   
   请参阅以下链接：[https://azure.microsoft.com/zh-cn/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. 如何使用上传到高级存储的 VHD 在 Azure 门户中创建 VM？
   
   我们尚不支持此功能。
9. Azure 应用商店支持 32 位应用吗？
   
   有关支持策略的详细信息，请参阅以下链接：[https://support.microsoft.com/zh-cn/kb/2721672](https://support.microsoft.com/en-us/kb/2721672)
10. 每当我尝试从 VHD 中创建图像时，都会在 PowerShell 中收到错误“VHD 已通过图像存储库注册为资源”。 我之前没有创建任何图像，也没在 Azure 中找到任何带有此名称的图像。 如何解决此问题？
    
    如果用户从此 VHD 预配 VM，而该 VHD 没有锁定，通常会发生此情况。 请查看是否有 VM 分配自此 VHD。 如果错误仍然存在，请使用此链接或从有关的发布门户提出支持票证（详细信息在问题 11 的答案中提供）。
11. 如何为我的问题提出支持票证？
    
    请遵循以下步骤。
    
    * 提出支持票证前，请确保你是 Azure 认证的合作伙伴。 如果不是，请使用此[链接](https://azure.microsoft.com/en-us/documentation/articles/marketplace-publishing-azure-certification/)向 Azure 认证计划申请。
    * 如果是批准的 Azure 认证合作伙伴，请使用 Microsoft 帐户登录[发布门户](https://publish.windowsazure.com/)。如果没有 Microsoft 帐户，可使用此[链接](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)创建一个。
    * 单击用户名下菜单的“获取支持”链接（请参看以下屏幕截图）。
      
      ![绘制](media/marketplace-publishing-vm-image-creation-troubleshooting/img11_01.png)
    * 选择问题类型“VM 映像认证”和类别“为 Azure VM 应用商店准备 VHD”，然后单击按钮“启动请求”。
    * 填写详细信息，然后提交票证。 支持团队将帮助你解决问题。




<!--HONumber=Nov16_HO3-->


