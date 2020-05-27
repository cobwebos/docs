---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/27/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0d5c3b55d20be19d4aeb92b82d6e44d417259a7b
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "67172776"
---
1. 右键单击“命令提示符”  并选择“以管理员身份运行”  ，使用提升的权限打开命令提示符。
2. 请在命令提示符处运行以下命令：

   ```
   reg add HKLM\SYSTEM\CurrentControlSet\Services\RasMan\PPP\EAP\13 /v TlsVersion /t REG_DWORD /d 0xfc0
   reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DefaultSecureProtocols /t REG_DWORD /d 0xaa0
   if %PROCESSOR_ARCHITECTURE% EQU AMD64 reg add "HKLM\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp" /v DefaultSecureProtocols /t REG_DWORD /d 0xaa0
   ```

3. 安装以下更新：
  
   * [KB3140245](https://www.catalog.update.microsoft.com/search.aspx?q=kb3140245)
   * [KB2977292](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2977292)

4. 重新启动计算机。
5. 连接到 VPN。

> [!NOTE]
> 如果运行的是旧版本的 Windows 10 (10240)，则必须设置上述注册表项。
>
