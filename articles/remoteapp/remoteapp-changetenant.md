---
title: "在 Azure RemoteApp 中更改 Azure Active Directory 租户 | Microsoft Docs"
description: "了解如何更改与 Azure RemoteApp 关联的 Azure Active Directory 租户"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 20faf169-6e48-428a-8bdd-f231daff19fa
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 5326c779d766e075a54f5ec43f92b6c5c973be8b


---
# <a name="change-the-azure-active-directory-tenant-in-azure-remoteapp"></a>在 Azure RemoteApp 中更改 Azure Active Directory 租户
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

Azure RemoteApp 使用 Azure Active Directory (Azure AD) 来允许用户访问。 你可以在 Azure RemoteApp 中使用的唯一 Azure AD 租户是与 Azure 订阅相关联的租户。 你可以在门户中的“**设置**”页面上查看关联的订阅。 查看“**订阅**”选项卡上的“**目录**”列。

> [!NOTE]
> 要使此更改成功，首先从所有 Azure RemoteApp 集合中的现有 Azure Active Directory 租户中删除所有用户。 要这样做，请转到 Azure 门户，转到“**Azure RemoteApp**”选项卡，然后打开每个 Azure RemoteApp 集合。 转到“**用户**”选项卡并删除属于你的当前 Azure Active Directory 租户的用户。 对所有现有 Azure RemoteApp 集合重复此操作。 不执行此操作，你就无法创建或修补集合。
> 
> 

如果你想要使用一个不同的租户，请按照以下步骤更改与订阅的关联︰

1. 在门户中，移除你已向其授予对 Azure RemoteApp 集合访问权限的任何 Azure AD 用户。 （请参阅上面关于如何执行此操作的步骤说明。）
2. 将 Microsoft 帐户（以前称为 Live ID）设置为服务管理员。 （是否不知道您已经是服务管理员？ 你可以通过单击“**设置 -> 管理员**”来查明。）现在，下面会介绍如何进行更改：
   
   1. 单击右上角的用户，然后单击“**查看我的帐单**”。
   2. 单击订阅。 然后，在新页面上，向下滚动并单击位于右侧的“**编辑订阅详细信息**”。 （可以试试在靠右下方的中间位置查找。）
   3. 键入用户的 Microsoft 帐户，该帐户应该是服务管理员。
3. 现在，从该门户中注销，然后使用你在上一步中指定的 Microsoft 帐户重新登录。
4. 单击“**新建 -> 应用程序服务 -> Active Directory-> 目录-> 自定义创建**”。
5. 在“**目录**”下，选择“**使用现有目录**”。 现在我们将需要将你从门户注销，因此请选择“**我已准备好立即注销**”。
6. 以你想要添加的目录的全局管理员身份重新登录门户。 （如果你还不是全局管理员，那么在经过一轮的登录和然后注销之后，你将成为全局管理员。）
7. 当你登录时，系统将询问你是否要使用订阅的现有 AD 租户。 单击“**继续**”，然后单击“**立即注销**”。
8. 重新登录，并返回到“**设置 -> 订阅**”。 选择你的订阅，然后单击“**编辑目录**”。 选择你想要使用的 Azure AD 租户。

你可以立即使用新的 Azure AD 租户来控制对 Azure 订阅的访问，并在 Azure RemoteApp 中配置用户访问权限。




<!--HONumber=Dec16_HO2-->


