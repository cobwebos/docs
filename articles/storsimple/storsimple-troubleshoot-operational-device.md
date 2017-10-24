---
title: "对部署的 StorSimple 设备进行故障排除 | Microsoft Docs"
description: "介绍如何诊断和解决目前已部署并正常运行的 StorSimple 设备上发生的错误。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/16/2016
ms.author: v-sharos
ms.openlocfilehash: 8d1b4905d0a24c8df9eb2c986459286909fd20dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>对正常运行的 StorSimple 设备进行故障排除
## <a name="overview"></a>概述
本文将提供有用的故障排除指南，用于解决你在 StorSimple 设备部署并正常运行之后可能遇到的配置问题。 本文介绍一些常见问题、可能的原因和建议的步骤，帮助你解决在运行 Microsoft Azure StorSimple 时可能遇到的问题。 此信息适用于 StorSimple 本地物理设备和 StorSimple 虚拟设备。

在这篇文章的结尾，可以找到在 Microsoft Azure StorSimple 运行过程中可能遇到的错误代码列表，以及可用于解决这些错误的步骤。 

## <a name="setup-wizard-process-for-operational-devices"></a>操作设备的安装向导过程
使用安装向导 ([Invoke-HcsSetupWizard][1]) 检查设备配置，并在必要时采取纠正措施。

在以前配置并可正常运行的设备上运行安装向导时，处理流程是不同的。 只能更改以下条目：

* IP 地址、子网掩码和网关
* 主 DNS 服务器
* 主 NTP 服务器
* 可选的 Web 代理配置

安装向导不执行与密码收集和设备注册相关的操作。

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>在安装向导的后续运行过程中发生的错误
下表介绍了在操作设备上运行安装向导时可能遇到的错误、发生错误的可能原因以及解决它们的建议的操作。 

| 否。 | 错误消息或条件 | 可能的原因 | 建议的操作 |
|:--- |:--- |:--- |:--- |
| 1 |错误 350032：此设备已被停用。 |如果在停用的设备上运行安装向导，会看到此错误。 |[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)以了解后续步骤。 停用的设备无法提供服务。 可能需要先恢复出厂设置，才可以重新激活设备。 |
| #N/A |Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION(Exception from HRESULT: 0x80070001) |DNS 服务器更新失败。 DNS 设置是全局设置，并应用于所有启用的网络接口。 |启用界面并再次应用 DNS 设置。 由于这些设置是全局的，可能会中断其他已启用接口的网络。 |
| 3 |设备在 StorSimple Manager 服务门户中似乎处于联机状态，但尝试完成最低安装并保存配置时，操作会失败。 |在初始安装过程中，即使已存在实际的代理服务器，也不会配置 Web 代理。 |使用 [Test-HcsmConnection cmdlet][2] 查找错误。 如果不能更正此问题，请 [联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)。 |
| 4 |Invoke-HcsSetupWizard：值不在预期范围内。 |错误的子网掩码导致此错误。 可能的原因有： <ul><li> 子网掩码缺失或为空。</li><li>Ipv6 前缀格式不正确。</li><li>该接口已启用云，但网关缺失或不正确。</li></ul>请注意，如果通过安装向导配置，则 DATA 0 自动启用云。 |要确定该问题，请使用子网 0.0.0.0 或 256.256.256.256，再看输出。 请根据需要输入正确的子网掩码、网关和 Ipv6 前缀值。 |

## <a name="error-codes"></a>错误代码
按数字顺序列出错误。

| 错误号 | 错误文本或说明 | 建议的用户操作 |
|:--- |:--- |:--- |
| 10502 |访问存储帐户时出错。 |请等待几分钟，并重试。 如果此错误仍然存在，请联系 Microsoft 支持部门以了解后续步骤。 |
| 40017 |备份操作已失败，因为未在设备上找到备份策略中指定的卷。 |请重试备份操作，如果错误仍然存在，请联系 Microsoft 支持部门。 后续步骤。 |
| 40018 |备份操作已失败，因为未在设备上找到任何备份策略中指定的卷。 |请重试备份操作，如果错误仍然存在，请联系 Microsoft 支持部门。 后续步骤。 |
| 390061 |系统正忙或不可用。 |请等待几分钟，并重试。 如果此错误仍然存在，请联系 Microsoft 支持部门以了解后续步骤。 |
| 390143 |发生错误，错误代码为 390143。 （未知错误。） |如果此错误仍然存在，请联系 Microsoft 支持部门以了解后续步骤。 |

## <a name="next-steps"></a>后续步骤
如果无法解决问题，请 [联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md) 以获得协助。 

[1]: https://technet.microsoft.com/en-us/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/en-us/%5Clibrary/Dn715782(v=WPS.630).aspx
