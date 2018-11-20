---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: e18d0a6a01a86f844edc213fc95003cf4f4b46c9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164715"
---
* 使用远程桌面连接连接到进程服务器虚拟机。
* 可通过单击桌面上可用的快捷方式启动 cspsconfigtool.exe。 （如果这是第一次登录到进程服务器，该工具则会自动启动）。
  - 配置服务器的完全限定名称 (FQDN) 或 IP 地址
  - 配置服务器侦听时所在的端口。 值应为 443
  - 连接到配置服务器所需的连接通行短语。
  - 要为该进程服务器配置的数据传输端口。 保留原来的默认值，除非已在环境中将其更改为其他端口号。

    ![注册进程服务器](./media/site-recovery-vmware-register-process-server/register-ps.png)
* 单击保存按钮以保存配置并注册进程服务器。
