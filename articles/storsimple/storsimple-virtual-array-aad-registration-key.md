---
title: "适用于 StorSimple 虚拟阵列的新身份验证 | Microsoft Docs"
description: "说明如何将基于 AAD 的身份验证用于服务、生成新注册密钥以及执行设备的手动注册。"
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 8d033cc09de8e115324067d7bbdf052751730d63
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>将新身份验证用于 StorSimple

## <a name="overview"></a>概述

StorSimple Device Manager 服务在 Microsoft Azure 中运行并连接到多个 StorSimple 虚拟阵列。 迄今为止，StorSimple 设备管理器服务使用访问控制服务 (ACS) 面向 StorSimple 设备对服务进行身份验证。 ACS 机制即将弃用，会被替换为 Azure Active Directory (AAD) 身份验证。

本文中包含的信息仅适用于 StorSimple 1200 系列虚拟阵列。 本指南介绍 AAD 身份验证和关联的新服务注册密钥以及适用于 StorSimple 设备的防火墙规则修改的详细信息。

AAD 身份验证在运行 Update 1 或更高版本的 StorSimple 虚拟阵列（型号：1200）中进行。

由于引入了 AAD 身份验证，因此以下方面发生了更改：

- 防火墙规则的 URL 模式。
- 服务注册密钥。

以下部分详细讨论了这些更改。

## <a name="url-changes-for-aad-authentication"></a>AAD 身份验证的 URL 更改

若要确保服务使用基于 AAD 的身份验证，所有用户都必须在其防火墙规则中包含新身份验证 URL。

如果使用 StorSimple 虚拟阵列，请确保防火墙规则中包含以下 URL：

| URL 模式                         | 云 | 组件/功能         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |AAD 身份验证服务      |
| `https://login.microsoftonline.us` | 美国政府 |AAD 身份验证服务      |

有关适用于 StorSimple 虚拟阵列的 URL 模式的完整列表，请转到[防火墙规则的 URL 模式](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules)。

如果在弃用日期过后未将身份验证 URL 包含在防火墙规则中，用户会看到一条关键警报，指出其 StorSimple 设备无法通过服务进行身份验证。 该服务将无法与设备通信。 如果用户看到此警报，则需要包含新的身份验证 URL。 有关警报的详细信息，请转到[使用警报监视 StorSimple 设备](storsimple-virtual-array-manage-alerts.md#networking-alerts)。

## <a name="device-version-and-authentication-changes"></a>设备版本和身份验证更改

如果使用 StorSimple 虚拟阵列，请使用下表根据所运行的设备软件版本来确定需要执行的操作。

| 如果设备在运行  | 执行以下操作                                    |
|----------------------------|--------------------------------------------------------------|
| Update 1.0 或更高版本，且处于脱机状态。 <br> 会看到一个警报，指出 URL 不在允许列表中。| 修改防火墙规则，使之包含身份验证 URL。 请参阅[身份验证 URL](#url-changes-for-aad-authentication)。 |
| Update 1.0 或更高版本，且设备处于联机状态。| 不需要执行任何操作。                                       |
| Update 0.6 或更低版本，且设备处于脱机状态。 | [通过目录服务器下载 Update 1.0](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix)。<br>[通过本地 Web UI 应用 Update 1.0](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix)。 <br> [从服务获取 AAD 注册密钥](#aad-based-registration-keys)。 <br> 执行步骤 1-5，[连接到虚拟阵列的 Windows PowerShell 界面](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor)。<br> 使用 `Invoke-HcsReRegister` cmdlet 通过 Windows PowerShell 注册设备。 提供在上一步获取的密钥。|
| Update 0.6 或更低版本，且设备处于联机状态 | 修改防火墙规则，使之包含身份验证 URL。<br> 通过 Azure 门户安装 Update 1.0。 |

## <a name="aad-based-registration-keys"></a>基于 AAD 的注册密钥

对于 StorSimple 虚拟阵列，从 Update 1.0 开始，使用新的基于 AAD 的注册密钥。 可使用注册密钥向设备注册 StorSimple 设备管理器服务。

如果使用的 StorSimple 虚拟阵列运行 Update 0.6 或更低版本，则不能使用新的 AAD 服务注册密钥。 需重新生成服务注册密钥。 重新生成密钥之后，新密钥用于注册所有后续设备。 旧密钥不再有效。

- 新的 AAD 注册密钥会在 3 天后过期。
- AAD 注册密钥仅适用于运行 Update 1 或更高版本的 StorSimple 1200 系列虚拟阵列。 来自 StorSimple 8000 系列设备的 AAD 注册密钥将无效。
- AAD 注册密钥比对应的 ACS 注册密钥更长。

执行以下步骤，生成 AAD 服务注册密钥。

#### <a name="to-generate-the-aad-service-registration-key"></a>生成 AAD 服务注册密钥的步骤

1. 在“StorSimple 设备管理器”中，转到“管理”**&gt;**“密钥”。
    
    ![转到“密钥”](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. 单击“生成密钥”。

    ![单击“重新生成”](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. 复制新密钥。 旧密钥不再有效。

    ![确认重新生成](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>后续步骤

* 详细了解如何部署 [StorSimple 虚拟阵列](storsimple-virtual-array-deploy1-portal-prep.md)
