---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9d9790c9b3dbe3b130be999dd76092ae64f7b52c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66169956"
---
|参数名称| Type | 描述| 可能的值|
|-|-|-|-|
| /ServerMode|强制|指定是要同时安装配置服务器和进程服务器，还是只安装进程服务器|CS<br>PS|
|/InstallLocation|强制|用于安装组件的文件夹| 计算机上的任意文件夹|
|/MySQLCredsFilePath|强制|MySQL 服务器凭据存储到的文件路径|文件应采用以下指定格式|
|/VaultCredsFilePath|强制|保管库凭据文件的路径|有效的文件路径|
|/EnvType|强制|要保护的环境类型 |VMware<br>NonVMware|
|/PSIP|必需|要用于复制数据传输的 NIC 的 IP 地址| 任何有效的 IP 地址|
|/CSIP|强制|配置服务器侦听时所在的 NIC 的 IP 地址| 任何有效的 IP 地址|
|/PassphraseFilePath|强制|通行短语文件位置的完整路径|有效的文件路径|
|/BypassProxy|可选|指定配置服务器不使用代理连接到 Azure|若要从 Venu 获取此值|
|/ProxySettingsFilePath|可选|代理设置（默认代理需要身份验证，或自定义代理）|文件应采用以下指定格式|
|DataTransferSecurePort|可选|PSIP 上用于复制数据的端口号| 有效端口号（默认值为 9433）|
|/SkipSpaceCheck|可选|跳过缓存磁盘的空间检查| |
|/AcceptThirdpartyEULA|必需|该标志表示接受第三方 EULA| |
|/ShowThirdpartyEULA|可选|显示第三方 EULA。 如果作为输入提供，将忽略所有其他参数| |
