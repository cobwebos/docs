---
title: 解决数据管理网关问题 | Microsoft Docs
description: 提供解决数据管理网关相关问题的提示。
services: data-factory
author: nabhishek
manager: craigg
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: d82592bfda3eaf44d28f67b8bb6599aa4ae4bae7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>解决数据管理网关的使用问题
本文提供有关解决数据管理网关使用问题的信息。

> [!NOTE]
> 本文适用于 Azure 数据工厂版本 1（即正式版 (GA)）。 如果使用数据工厂服务版本 2（即预览版），请参阅[数据工厂版本 2 中的自托管集成运行时](../create-self-hosted-integration-runtime.md)。

有关网关的详细信息，请参阅[数据管理网关](data-factory-data-management-gateway.md)一文。 请参阅[在本地与云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文，了解通过使用网关将数据从本地 SQL Server 数据库移动到 Microsoft Azure Blob 存储的演练。

## <a name="failed-to-install-or-register-gateway"></a>无法安装或注册网关
### <a name="1-problem"></a>1.问题
在安装和注册网关时，尤其是在下载网关安装文件时，会出现此错误消息。

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>原因
由于网络问题，要尝试在其上安装网关的计算机无法从下载中心下载最新的网关安装文件。

#### <a name="resolution"></a>解决方法
请检查防火墙代理服务器设置，查看是否是设置阻止了从计算机到[下载中心](https://download.microsoft.com/)的网络连接，并相应地更新设置。

也可在其他可以访问下载中心的计算机上从[下载中心](https://www.microsoft.com/download/details.aspx?id=39717)下载最新网关的安装文件。 然后即可将安装程序文件复制到网关主机并以手动方式运行，以便安装和更新网关。

### <a name="2-problem"></a>2.问题
通过在 Azure 门户中单击“直接安装在此计算机上”来尝试安装网关时会出现此错误。

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>原因
计算机上已安装有网关。

#### <a name="resolution"></a>解决方法
卸载计算机上的现有网关，并再次单击“直接安装在此计算机上”链接。

### <a name="3-problem"></a>3.问题
注册新网关时可能会出现此错误。

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>原因
出现此消息可能是由于以下原因之一：

* 网关密钥的格式无效。
* 网关密钥已失效。
* 已在门户中重新生成了网关密钥。  

#### <a name="resolution"></a>解决方法
在门户中验证是否使用的是正确的网关密钥。 如果需要，可重新生成密钥并使用该密钥注册网关。

### <a name="4-problem"></a>4.问题
注册网关时，可能会出现下面的错误消息。

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![密钥内容或格式无效](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>原因
输入网关密钥的内容或格式不正确。 其中一个原因可能是仅从门户复制了密钥的一部分，或者使用了无效密钥。

#### <a name="resolution"></a>解决方法
在门户中生成网关密钥，并使用复制按钮复制整个密钥。 然后将其粘贴在此窗口中，以便注册网关。

### <a name="5-problem"></a>5.问题
注册网关时，可能会出现下面的错误消息。

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![网关密钥无效或为空](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>原因
网关密钥已重新生成或已在 Azure 门户中删除了网关。 如果数据管理网关安装程序不是最新版本，也可能会发生此问题。

#### <a name="resolution"></a>解决方法
检查数据管理网关安装程序是否是最新版本，可以在 Microsoft [下载中心](https://go.microsoft.com/fwlink/p/?LinkId=271260)找到最新版本。

如果安装程序是最新的且门户上的网关仍然存在，请在 Azure 门户中重新生成网关密钥，并使用复制按钮复制整个密钥，然后将其粘贴到此窗口中以注册网关。 否则，重新创建网关，然后重新开始。

### <a name="6-problem"></a>6.问题
注册网关时，可能会出现下面的错误消息。

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![网关密钥无效或为空](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>原因
发生此错误的原因可能是已删除了网关或已重新生成了关联的网关密钥。

#### <a name="resolution"></a>解决方法
如果网关已被删除，请在门户中重新创建网关，单击“注册”，从门户中复制并粘贴密钥，尝试注册网关。

如果网关仍然存在但已重新生成其密钥，则使用新的密钥注册网关。 如果没有密钥，请在门户中再次重新生成密钥。

### <a name="7-problem"></a>7.问题
注册网关时，可能需要输入证书的路径和密码。

![指定证书](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>原因
之前已在其他计算机上注册了网关。 在网关初始注册期间，网关已关联有加密证书。 证书可由网关自身生成或由用户提供。  此证书用于加密数据存储（链接服务）的凭据。  

![导出证书](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

在其他主机计算机上还原网关时，注册向导会请求此证书解密先前使用此证书加密的凭据。  如果没有此证书，新网关便无法解密凭据，且与此新网关相关联的后续复制活动执行会失败。  

#### <a name="resolution"></a>解决方法
如果已通过使用“数据管理网关配置管理器”的“设置”选项卡上的“导出”按钮，从原始网关计算机中导出了凭据证书，则请在此处使用该证书。

恢复网关时不能跳过此阶段。 如果证书丢失，则需从门户中删除网关，并重新创建新的网关。  此外，需要通过重新输入凭据来更新所有与网关相关的关联服务。

### <a name="8-problem"></a>8.问题
可能会出现以下错误消息。

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>原因
网关所在环境需要 HTTP 代理来访问 Internet 资源，或代理的身份验证密码已更改但网关中并未相应进行更新时，会发生此错误。

#### <a name="resolution"></a>解决方法
按照本文[代理服务器注意事项](#proxy-server-considerations)部分的说明，通过数据管理网关配置管理器配置代理设置。

## <a name="gateway-is-online-with-limited-functionality"></a>网关联机但功能受限
### <a name="1-problem"></a>1.问题
网关的状态显示为“联机但功能受限”。

#### <a name="cause"></a>原因
网关状态显示为“联机但功能受限”是由于以下原因之一：

* 网关无法通过 Azure 服务总线连接到云服务。
* 云服务无法通过服务总线连接到网关。

网关联机但功能受限时，可能无法使用数据工厂复制向导创建用于向/从本地数据存储复制数据的数据管道。 解决办法是使用门户、Visual Studio 或 Azure PowerShell 中的数据工厂编辑器。

#### <a name="resolution"></a>解决方法
此问题（联机但功能受限）的解决方法取决于网关是无法连接到云服务还是云服务无法连接到网关。 以下部分提供相关解决方法。

### <a name="2-problem"></a>2.问题
出现以下错误。

`Error: Gateway cannot connect to cloud service through service bus`

![网关无法连接到云服务](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>原因
网关无法通过服务总线连接到云服务。

#### <a name="resolution"></a>解决方法
请按照这些步骤使网关重新处于联机状态：

1. 允许网关计算机和企业防火墙上的 IP 地址出站规则。 可通过 Windows 事件日志查找 IP 地址 (ID == 401)：尝试了以其访问权限 XX.XX.XX.XX:9350 禁止的方式访问套接字。
* 在网关上配置代理设置。 有关详细信息，请参阅[代理服务器注意事项](#proxy-server-considerations)部分。
* 对网关计算机和企业防火墙上的 Windows 防火墙启用出站端口 5671 和 9350-9354。 有关详细信息，请参阅[端口和防火墙](#ports-and-firewall)部分。 此步骤虽为可选步骤，但出于性能方面的考虑，建议执行该步骤。

### <a name="3-problem"></a>3.问题
出现以下错误。

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>原因
网络连接暂时性错误。

#### <a name="resolution"></a>解决方法
请按照这些步骤使网关重新处于联机状态：

1. 稍等几分钟，连接会在错误消失后自动恢复。
* 如果错误持续存在，请重新启动网关服务。

## <a name="failed-to-author-linked-service"></a>无法创作链接服务
### <a name="problem"></a>问题
尝试使用门户中的凭据管理器输入新关联服务的凭据或更新现有关联服务的凭据时，可能会出现此错误。

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

出现此错误时，数据管理网关配置管理器的设置页可能如以下屏幕快照所示。

![无法访问数据库](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>原因
网关计算机上的 SSL 证书可能已丢失。 网关计算机无法加载当前用于 SSL 加密的证书。 可能还会在事件日志中看到类似以下消息的错误消息。

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>解决方法
请按照下列步骤来解决此问题：

1. 启动数据管理网关配置管理器。
2. 切换到“设置”选项卡。  
3. 单击“更改”按钮更改 SSL 证书。

   ![更改证书按钮](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. 选择一个新证书作为 SSL 证书。 可使用自己或任何组织生成的任何 SSL 证书。

   ![指定证书](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>复制活动失败
### <a name="problem"></a>问题
在门户中设置管道以后，可能会出现以下“UserErrorFailedToConnectToSqlserver”故障。

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>原因
出现此问题的原因有多种，相应地，缓解方法也会不同。

#### <a name="resolution"></a>解决方法
连接到 SQL 数据库前，在数据管理网关客户端中允许端口 TCP/1433 上的出站 TCP 连接。

如果目标数据库为 Azure SQL 数据库，请一并检查 Azure 的 SQL Server 防火墙设置。

请参阅以下部分，了解如何测试本地数据存储连接。

## <a name="data-store-connection-or-driver-related-errors"></a>数据存储连接或驱动程序相关错误
如果看到数据存储连接或驱动程序相关错误，请完成以下步骤：

1. 在网关计算机上启动数据管理网关配置管理器。
2. 切换到“诊断”选项卡。
3. 在“测试连接”中，添加网关组值。
4. 单击“测试”，查看是否可使用连接信息和凭据从网关计算机连接到本地数据源。 如果在安装驱动程序后测试连接仍然失败，请重新启动网关以便选择最新的更改。

![在“诊断”选项卡中测试连接](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>网关日志
### <a name="send-gateway-logs-to-microsoft"></a>向 Microsoft 发送网关日志
联系 Microsoft 支持部门以获取解决网关问题的帮助时，可能会要求共享网关日志。 网关发布以后，只需在数据管理网关配置管理器中点击两次按钮便可轻松共享所需的网关日志。    

1. 切换到数据管理网关配置管理器中的“诊断”选项卡。

    ![数据管理网关 -“诊断”选项卡](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. 单击“发送日志”查看以下对话框。

    ![数据管理网关 - 发送日志](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. （可选）单击“查看日志”在事件查看器中查看日志。
4. （可选）单击“隐私”查看 Microsoft Web 服务隐私声明。
5. 如若对要上传的内容感到满意，便可单击“发送日志”将过去 7 天内的日志发送给 Microsoft，以便进行故障排除。 应该会看到“发送日志”操作的状态如以下屏幕快照所示。

    ![数据管理网关 - 发送日志状态](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. 操作完成后，会看到如以下屏幕快照所示的对话框。

    ![数据管理网关 - 发送日志状态](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. 保存“报告 ID”，并将其与 Microsoft 支持部门共享。 此报告 ID 用于查找已经上传以进行故障排除的网关日志。  此报告 ID 也会保存在事件查看器中。  可通过查找事件 ID“25”找到该报表 ID，并查看日期和时间。

    ![数据管理网关 - 发送日志报告 ID](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>在网关主机计算机上存档网关日志
在某些情况下可能碰到网关出现问题且无法直接共享网关日志：

* 手动安装并注册网关。
* 尝试在数据管理网关配置管理器上使用重新生成的密钥注册网关。
* 尝试发送日志，但无法连接网关主机服务。

对于此类情况，可将网关日志另存为 zip 文件，然后在联系 Microsoft 支持部门时将其与之共享。 例如在注册网关时收到错误，如以下屏幕快照所示。   

![数据管理网关 - 注册错误](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

单击“存档网关”日志链接，存档并保存日志，并与 Microsoft 支持部门共享 zip 文件。

![数据管理网关 - 存档日志](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>查找网关日志
可以在 Windows 事件日志中找到详细的网关日志信息。

1. 启动 Windows **事件查看器**。
2. 在“应用程序和服务日志” > **Data Management Gateway** 文件夹中查找日志。

 解决网关相关问题时，在事件查看器中查找错误级别事件。

![数据管理网关 - 事件查看器中的日志](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
