---
title: Azure 多重身份验证服务器入门 | Microsoft 文档
description: 本地 Azure MFA 服务器分步入门
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: bd1b0f21162978496750886d32e7166c3a90922d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868290"
---
# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Azure 多重身份验证服务器入门

<center>![本地 MFA](./media/howto-mfaserver-deploy/server2.png)</center>

决定使用本地多重身份验证服务器后，让我们继续下一步。 本页介绍如何全新安装服务器，以及在本地 Active Directory 上对它进行设置。 如果已安装 MFA 服务器，但需进行升级，请参阅[升级到最新的 Azure 多重身份验证服务器](howto-mfaserver-deploy-upgrade.md)。 若要了解如何只安装 Web 服务，请参阅[部署 Azure 多重身份验证服务器移动应用 Web 服务](howto-mfaserver-deploy-mobileapp.md)。

## <a name="plan-your-deployment"></a>规划部署

在下载 Azure 多重身份验证服务器之前，请考虑一下你的负载和高可用性要求是什么。 使用该信息来决定部署方法和位置。

所需内存量可以根据平常需进行身份验证的用户数来确定。

| 用户 | RAM |
| ----- | --- |
| 1-10,000 | 4 GB |
| 10,001-50,000 | 8 GB |
| 50,001-100,000 | 12 GB |
| 100,000-200,001 | 16 GB |
| 200,001+ | 32 GB |

是否需设置多个服务器以确保实现高可用性或负载均衡？ 可以通过多种方式为 Azure MFA 服务器设置此配置。 在安装你的第一个 Azure MFA 服务器时，该服务器就成为主服务器。 任何其他服务器都将成为从属服务器，并自动与主服务器同步用户和配置。 然后，你可以配置一个主服务器，让其他服务器充当备份，也可以在所有服务器中设置负载均衡。

当主 Azure MFA 服务器脱机时，从属服务器仍可处理双重验证请求。 但是，在主服务器重新联机或从属服务器获得提升之前，不能添加新用户，现有用户也不能更新其设置。

### <a name="prepare-your-environment"></a>准备环境

请确保用于 Azure 多重身份验证的服务器满足以下要求：

| Azure 多重身份验证服务器要求 | 说明 |
|:--- |:--- |
| 硬件 |<li>200 MB 硬盘空间</li><li>有 x32 或 x64 功能的处理器</li><li>1 GB 或更大的 RAM</li> |
| 软件 |<li>Windows Server 2016</li><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008 R2</li><li>Windows Server 2008、SP1、SP2</li><li>Windows Server 2003 R2</li><li>Windows Server 2003、SP1、SP2</li><li>Windows 10</li><li>Windows 8.1，所有版本</li><li>Windows 8，所有版本</li><li>Windows 7，所有版本</li><li>Windows Vista，所有版本、SP1、SP2</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 或更高版本（如果要安装用户门户或 Web 服务 SDK）</li> |

### <a name="azure-mfa-server-components"></a>Azure MFA 服务器组件

Azure MFA 服务器由三个 Web 组件组成：

* Web 服务 SDK - 用于与其他组件通信，安装在 Azure MFA 应用程序服务器上
* 用户门户 - 一个 IIS 网站，允许用户注册 Azure 多重身份验证 (MFA) 以及维护其帐户。
* 移动应用 Web 服务 - 允许将 Microsoft Authenticator 应用之类的移动应用用于双重验证。

如果服务器是面向 Internet 的，则可将所有三个组件安装在同一服务器上。 如果将组件分开，则可将 Web 服务 SDK 安装在 Azure MFA 应用程序服务器上，将用户门户和移动应用 Web 服务安装在面向 Internet 的服务器上。

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Azure 多重身份验证服务器防火墙要求

每个 MFA 服务器都必须能够通过端口 443 与以下地址进行出站通信：

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

如果端口 443 上限制了出站防火墙，请打开以下 IP 地址范围：

| IP 子网 | 网络掩码 | IP 范围 |
|:---: |:---: |:---: |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254 |

如果不使用“事件确认”功能，并且用户也不在企业网络中的设备上使用移动应用进行验证，则只需以下范围：

| IP 子网 | 网络掩码 | IP 范围 |
|:---: |:---: |:---: |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79 |
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79 |
| 70.37.154.200/29 |255.255.255.248 |70.37.154.201 – 70.37.154.206 |

## <a name="download-the-mfa-server"></a>下载 MFA 服务器

执行以下步骤，从 Azure 门户下载 Azure 多重身份验证服务器：

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” > “MFA 服务器”。
3. 选择“服务器设置”。
4. 选择“下载”，按下载页上的说明保存安装程序。 

   ![下载 MFA 服务器](./media/howto-mfaserver-deploy/downloadportal.png)

5. 让此页保持打开状态，因为我们在运行安装程序后需要用到它。

## <a name="install-and-configure-the-mfa-server"></a>安装和配置 MFA 服务器

下载服务器后，可以进行安装和配置。 请确保用于安装的服务器符合规划部分列出的要求。

1. 双击可执行文件。
2. 在“选择安装文件夹”屏幕中，确保文件夹正确，并单击“下一步”。
3. 安装完成后，单击“完成”。  此时会启动配置向导。
4. 在配置向导欢迎屏幕上，选中“跳过使用身份验证配置向导”，并单击“下一步”。  此时向导将关闭，服务器会启动。

   ![云](./media/howto-mfaserver-deploy/skip2.png)

5. 返回下载服务器的页面，单击“生成激活凭据”按钮。 在提供的框中，将此信息复制到“Azure MFA 服务器”，然后单击“激活”。

## <a name="send-users-an-email"></a>向用户发送电子邮件

为了方便推出，请允许 MFA 服务器与用户通信。 MFA 服务器可以向用户发送一封电子邮件，告知他们已经为其注册了双重验证。

应该根据为用户配置双重验证的方式来确定所发送电子邮件的内容。 例如，如果可以从公司目录导入电话号码，则电子邮件中应该包含默认电话号码，使用户知道下一步会发生什么。 如果未导入电话号码，或者用户要使用移动应用，则发送的电子邮件会指导用户完成其帐户注册。 电子邮件中包含指向 Azure 多重身份验证用户门户的超链接。

此外，电子邮件的内容根据为用户设置的验证方法（电话呼叫、短信或移动应用）的不同而异。  例如，如果要求用户在身份验证时使用 PIN 码，则该电子邮件将告诉用户其初始 PIN 码的设置。  要求用户在首次验证期间更改其 PIN 码。

### <a name="configure-email-and-email-templates"></a>配置电子邮件和电子邮件模板

单击左侧的电子邮件图标，设置用于发送这些电子邮件的设置。 可以在此页中输入邮件服务器的 SMTP 信息，并通过选中“向用户发送电子邮件”复选框来发送电子邮件。

![MFA 服务器电子邮件配置](./media/howto-mfaserver-deploy/email1.png)

在“电子邮件内容”选项卡中，可以看到可供选择的电子邮件模板。 根据为用户配置的执行双步验证的方式，选择最合适的模板。

![MFA 服务器电子邮件模板](./media/howto-mfaserver-deploy/email2.png)

## <a name="import-users-from-active-directory"></a>从 Active Directory 导入用户

安装服务器以后，需添加用户。 可以选择手动创建用户、从 Active Directory 导入用户，或者配置与 Active Directory 的自动同步。

### <a name="manual-import-from-active-directory"></a>从 Active Directory 手动导入

1. 在“Azure MFA 服务器”的左侧选择“用户” 。
2. 在底部选择“从 Active Directory 导入” 。
3. 现在，可以搜索单个用户，或在 AD 中搜索包含用户的 OU（组织单位）。  在本例中，我们将指定用户 OU。
4. 突出显示右侧的所有用户，**并单击“导入**。  此时应会显示一个弹出窗口，指出操作已成功。  关闭导入窗口。

   ![MFA 服务器用户导入](./media/howto-mfaserver-deploy/import2.png)

### <a name="automated-synchronization-with-active-directory"></a>与 Active Directory 的自动同步

1. 在“Azure MFA 服务器”的左侧选择“目录集成”。
2. 导航到“同步”选项卡。
3. 在底部选择“添加”
4. 在显示的“添加同步项”框中，为此同步任务选择域、OU 或安全组、设置、方法默认设置、语言默认设置，然后单击“添加”。
5. 选中标为“启用与 Active Directory 的同步”的框，然后选择“同步时间间隔”（1 分钟到 24 小时）。

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Azure 多重身份验证服务器如何处理用户数据

如果你在本地使用多重身份验证 (MFA) 服务器，用户的数据将存储在本地服务器中。 云中不会持久存储任何用户数据。 当用户执行双步验证时，MFA 服务器会将数据发送到 Azure MFA 云服务，以执行验证。 将这些身份验证请求发送到云服务时，会在请求和日志中发送以下字段，以便在客户的身份验证/使用情况报告中使用。 某些字段是可选的，可以在多重身份验证服务器中启用或禁用。 从 MFA 服务器到 MFA 云服务的通信使用基于出站端口 443 的 SSL/TLS。 这些字段是：

* 唯一 ID - 用户名或内部 MFA 服务器 ID
* 名字和姓名（可选）
* 电子邮件地址（可选）
* 电话号码 - 用于语音通话或短信身份验证
* 设备令牌 - 用于移动应用身份验证
* 身份验证模式
* 身份验证结果
* MFA 服务器名称
* MFA 服务器 IP
* 客户端 IP – 如果可用

除了上述字段，验证结果（成功/拒绝）和任何拒绝的原因还与身份验证数据一起存储，可通过身份验证/使用情况报告获取。

## <a name="back-up-and-restore-azure-mfa-server"></a>备份和还原 Azure MFA 服务器

不管什么系统，都必须确保备份正常，这是很重要的一步。

若要备份 Azure MFA 服务器，请确保复制 C:\Program Files\Multi-Factor Authentication Server\Data 文件夹（包括 PhoneFactor.pfdata 文件）。 

如果需要还原，请完成以下步骤：

1. 在新服务器上重新安装 Azure MFA 服务器。
2. 激活新的 Azure MFA 服务器。
3. 停止 MultiFactorAuth 服务。
4. 使用备份的副本覆盖 PhoneFactor.pfdata。
5. 启动 MultiFactorAuth 服务。

新服务器现已使用原始的已备份配置和用户数据启动并运行。

## <a name="next-steps"></a>后续步骤

- 为用户的自助服务设置和配置[用户门户](howto-mfaserver-deploy-userportal.md)。
- 为 Azure MFA 服务器设置和配置 [Active Directory 联合身份验证服务](multi-factor-authentication-get-started-adfs.md)、[RADIUS 身份验证](howto-mfaserver-dir-radius.md)或 [LDAP 身份验证](howto-mfaserver-dir-ldap.md)。
- 设置和配置[使用 RADIUS 的远程桌面网关和 Azure 多重身份验证服务器](howto-mfaserver-nps-rdg.md)。
- [部署 Azure 多重身份验证服务器移动应用 Web 服务](howto-mfaserver-deploy-mobileapp.md)。
- [使用 Azure 多重身份验证与第三方 VPN 的高级方案](howto-mfaserver-nps-vpn.md)。
