---
title: Azure AD Connect：预览版功能 | Microsoft Docs
description: 本主题详细介绍 Azure AD Connect 中以预览版形式提供的功能。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: afbd48b8793e8b16597efb1e3996058b7450e03e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497088"
---
# <a name="more-details-about-features-in-preview"></a>有关预览版功能的详细信息
本主题介绍如何使用当前以预览版形式提供的功能。

## <a name="group-writeback"></a>组写回
可选功能中的组写回选项允许用户将“Office 365 组”写回到装有 Exchange 的林。 这是永远在云中受控制的组。 如果有本地 Exchange，则可以将这些组写回到本地，使具有本地 Exchange 邮箱的用户可以从这些组发送和接收电子邮件。

可在[此处](https://aka.ms/O365g)找到有关 Office 365 组及其用法的详细信息。

Office 365 组会在本地 AD DS 中表示为分发组。 本地 Exchange 服务器必须是 Exchange 2013 累积更新 8（2015 年 3 月发行）或 Exchange 2016，才能识别这个新的组类型。

**预览期注意事项**

* 预览版中当前不会填充通讯簿属性。 如果没有这个属性，组就不会显示在 GAL 中。 若要填充此属性，最简单的方法是使用 Exchange PowerShell cmdlet `update-recipient`。
* 只有使用 Exchange 架构的林才是组的有效目标。 如果没有检测到 Exchange，则无法启用组写回功能。
* 目前仅支持单林 Exchange 组织部署。 如果本地环境中有多个 Exchange 组织，则需有一个本地 GALSync 解决方案才能让这些组显示在其他林中。
* 组写回功能不处理安全组或分发组。

> [!NOTE]
> 组写回需要 Azure AD Premium 订阅。
> 
>

## <a name="user-writeback"></a>用户写回
> [!IMPORTANT]
> Azure AD Connect 的 2015 年 8 月更新版中删除了用户写回预览版功能。 如果已启用此功能，现在应将它禁用。
>
>

## <a name="next-steps"></a>后续步骤
配置 [Azure AD Connect 的自定义安装](how-to-connect-install-custom.md)。

了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
