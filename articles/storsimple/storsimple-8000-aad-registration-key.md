---
title: 将新身份验证用于 Azure 中的 StorSimple 8000 设备管理器服务 | Microsoft Docs
description: 说明如何将基于 AAD 的身份验证用于你的服务、生成新注册密钥以及执行设备的手动注册。
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: e6e792c31f9856bcaf1d777e534dcac8d8be3dd3
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113521"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>将新身份验证用于 StorSimple

## <a name="overview"></a>概述

StorSimple Device Manager 服务在 Microsoft Azure 中运行并连接到多个 StorSimple 设备。 迄今为止，StorSimple 设备管理器服务使用访问控制服务 (ACS) 面向 StorSimple 设备对服务进行身份验证。 ACS 机制即将弃用，会替换为 Azure Active Directory (AAD) 身份验证。 有关详细信息，请转到有关 ACS 弃用以及使用 AAD 身份验证的以下公告。

- [Azure ACS 的未来是 Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [对 Microsoft 访问控制服务即将进行的更改](https://azure.microsoft.com/en-in/blog/acs-access-control-service-namespace-creation-restriction/)

本指南介绍 AAD 身份验证和关联的新服务注册密钥以及适用于 StorSimple 设备的防火墙规则修改的详细信息。 本文中包含的信息仅适用于 StorSimple 8000 系列设备。

AAD 身份验证在运行 Update 5 或更高版本的 StorSimple 8000 系列设备上进行。 由于引入了 AAD 身份验证，因此以下方面发生了更改：

- 防火墙规则的 URL 模式。
- 服务注册密钥。

以下部分详细讨论了这些更改。

## <a name="url-changes-for-aad-authentication"></a>AAD 身份验证的 URL 更改

若要确保服务使用基于 AAD 的身份验证，所有用户都必须在其防火墙规则中包含新身份验证 URL。

如果使用 StorSimple 8000 系列，则确保防火墙规则中包含以下 URL：

| URL 模式                         | 云 | 组件/功能         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |AAD 身份验证服务      |
| `https://login.microsoftonline.us` | 美国政府 |AAD 身份验证服务      |

有关适用于 StorSimple 8000 系列设备的 URL 模式的完整列表，请转到[防火墙规则的 URL 模式](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules)。

如果身份验证 URL 在超过弃用日期之后未包含在防火墙规则中，并且设备在运行 Update 5，则用户会看到 URL 警报。 用户需要包含新身份验证 URL。 如果设备在运行 Update 5 之前的版本，则用户会看到检测信号警报。 在每种情况下，StorSimple 设备都无法向服务进行身份验证，并且服务无法与设备进行通信。

## <a name="device-version-and-authentication-changes"></a>设备版本和身份验证更改

如果使用 StorSimple 8000 系列设备，请使用下表根据所运行的设备软件版本来确定需要执行的操作。

| 如果设备在运行| 执行以下操作                                    |
|--------------------------|------------------------|--------------------|--------------------------------------------------------------|
| Update 5 或更高版本并且设备处于脱机状态。 <br> 会看到指出 URL 不在允许列表中的警报。|1.修改防火墙规则，使之包含身份验证 URL。 请参阅[身份验证 URL](#url-changes-for-aad-authentication)。<br>2.[从服务获取 AAD 注册密钥](#aad-based-registration-keys)。<br>3.[连接到 StorSimple 8000 系列设备的 Windows PowerShell 界面](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)。<br>4.使用 `Redo-DeviceRegistration` cmdlet 通过 Windows PowerShell 注册设备。 提供在上一步获取的密钥。|
| Update 5 或更高版本并且设备处于联机状态。| 不需要执行任何操作。                                       |
| Update 4 或较早版本并且设备处于脱机状态。 |1.修改防火墙规则，使之包含身份验证 URL。<br>2.[通过目录服务器下载 Update 5](storsimple-8000-install-update-5.md#download-updates-for-your-device)。<br>3.[通过修补程序方法应用 Update 5](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix)。<br>4.[从服务获取 AAD 注册密钥](#aad-based-registration-keys)。<br>5.[连接到 StorSimple 8000 系列设备的 Windows PowerShell 界面](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)。 <br>6.使用 `Redo-DeviceRegistration` cmdlet 通过 Windows PowerShell 注册设备。 提供在上一步中获取的密钥。|
| Update 4 或较早版本并且设备处于联机状态。 |修改防火墙规则以包含身份验证 URL。<br> 通过 Azure 门户安装 Update 5。              |
| 恢复出厂设置为 Update 5 之前的版本。      |在设备运行较早软件时，门户会显示基于 AAD 的注册密钥。 按照上面适用于设备运行 Update 4 或较早版本时的方案中的步骤执行。              |

## <a name="aad-based-registration-keys"></a>基于 AAD 的注册密钥

对于 StorSimple 8000 系列设备，从 Update 5 开始，使用新的基于 AAD 的注册密钥。 可使用注册密钥向设备注册 StorSimple 设备管理器服务。

如果在使用运行 Update 4 或较早版本的 StorSimple 8000 系列设备（包括现在正在激活的较旧设备），则无法使用新的 AAD 服务注册密钥。
在此情况下，需要重新生成服务注册密钥。 重新生成密钥之后，新密钥用于注册所有后续设备。 旧密钥不再有效。

- 新的 AAD 注册密钥会在 3 天后过期。
- AAD 注册密钥仅适用于运行 Update 5 或更高版本的 StorSimple 8000 系列设备。
- AAD 注册密钥比对应的 ACS 注册密钥更长。

执行以下步骤，生成 AAD 服务注册密钥。

#### <a name="to-generate-the-aad-service-registration-key"></a>生成 AAD 服务注册密钥的步骤

1. 在“StorSimple 设备管理器”中，转到“管理”**&gt;**“密钥”。 还可以使用搜索栏搜索密钥。
    
2. 单击“生成密钥”。

    ![单击“重新生成”](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. 复制新密钥。 旧密钥将不再有效。

    ![确认重新生成](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > 如果在注册到 StorSimple 8000 系列设备的服务上创建 StorSimple 云设备，请勿在创建正在进行时生成注册密钥。 等待创建完成，然后生成注册密钥。

## <a name="next-steps"></a>后续步骤

* 了解有关如何部署 [StorSimple 8000 系列设备](storsimple-8000-deployment-walkthrough-u2.md)的详细信息。

