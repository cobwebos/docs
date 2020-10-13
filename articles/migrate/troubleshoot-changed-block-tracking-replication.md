---
title: 排查无代理 VMware VM 迁移中的复制问题
description: 获取有关复制周期失败的帮助
author: anvar-ms
ms.manager: bsiva
ms.author: anvar
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: 6318f426e42612f21da7a43c9857894ae610f68e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871168"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>排查无代理 VMware VM 迁移中的复制问题

本文介绍了使用 Azure Migrate：服务器迁移无代理方法复制本地 VMware Vm 时可能会遇到的一些常见问题和具体的错误。

使用无代理复制方法复制 VMware 虚拟机时，虚拟机&#39;s 磁盘 (vmdk) 的数据将复制到 Azure 订阅中的副本托管磁盘。 VM 的复制开始时，将发生初始复制循环，其中复制了磁盘的完整副本。 初始复制完成后，将定期计划增量复制循环，以传输自上一次复制循环后发生的任何更改。

有时可能会发现 VM 的复制周期失败。 由于本地网络配置中的问题到 Azure Migrate 云服务后端出现问题，可能会导致这些失败。 在本文中，我们将：

 - 向您展示如何监视复制状态并解决错误。
 - 列出某些经常发生的复制错误，并建议执行其他步骤来更正这些错误。

## <a name="monitor-replication-status-using-the-azure-portal"></a>使用 Azure 门户监视复制状态

使用以下步骤监视虚拟机的复制状态：

  1. 中转到 Azure 门户 Azure Migrate 上的 "服务器" 页。
  2. 单击 "服务器迁移" 磁贴中的 "复制服务器"，导航到 "复制计算机" 页。
  3. 你将看到复制服务器的列表以及其他信息，如状态、运行状况、上次同步时间等。"运行状况" 列指示 VM 的当前复制运行状况。 运行状况列中的 "严重" 或 "警告" 值通常指示 VM 的上一个复制周期失败。 若要获取更多详细信息，请右键单击 VM，并选择 "错误详细信息"。 "错误详细信息" 页包含有关错误的信息以及有关如何进行故障排除的其他详细信息。 你还将看到 "最近的事件" 链接，可用于导航到 VM 的 "事件" 页。
  4. 单击 "最近的事件"，以查看 VM 的以前的复制周期失败。 在 "事件" 页中，查找 VM 的 "复制周期失败" 或 "磁盘的复制周期失败" 类型的最近事件。
  5. 单击事件以了解错误的可能原因和建议的修正步骤。 使用提供的信息来排除错误并对其进行修正。
    
## <a name="common-replication-errors"></a>常见复制错误

本部分介绍一些常见错误以及如何解决这些错误。

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>尝试复制 Vm 时出现 Key Vault 操作失败错误

**错误：** "Key Vault 操作失败。 操作：配置托管存储帐户、Key Vault：密钥保管库名称、存储帐户：存储帐户名称失败，并出现以下错误： "

**错误：** "Key Vault 操作失败。 操作：生成共享访问签名定义，Key Vault：密钥保管库名称，存储帐户：存储帐户名称失败，出现错误： "

![Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

发生此错误的原因通常是，Key Vault 的用户访问策略不向当前登录的用户提供配置要 Key Vault 管理的存储帐户所需的权限。 若要检查密钥保管库上的用户访问策略，请在门户中访问密钥保管库的密钥保管库页面，并选择 "访问策略" 

当门户创建密钥保管库时，它还会添加一个授予当前已登录用户权限的用户访问策略，以便将存储帐户配置为 Key Vault 管理。 这可能由于两个原因而失败

- 已登录用户是客户 Azure 租户 (CSP 订阅的远程主体，登录用户是合作伙伴管理员) 。 在这种情况下，解决方法是删除密钥保管库，从门户注销，然后使用客户租户中的用户帐户登录， (不是远程主体) ，并重试该操作。 CSP 合作伙伴通常会在客户 Azure Active Directory 租户中拥有用户帐户，用户可以使用该帐户。 如果不能为客户 Azure Active Directory 租户中的用户创建新用户帐户，请以新用户的身份登录到门户，然后重试复制操作。 所使用的帐户必须拥有 "所有者" 或 "参与者" 和 "用户访问管理员" 权限，才能在资源组 (迁移项目资源组) 

- 另一种情况是，当某个用户 (user1) 尝试在首次设置复制时尝试设置复制，并且已创建了密钥保管库， (并为此用户) 适当分配了用户访问策略。 稍后，其他用户 (用户 2) 尝试设置复制，但是配置托管存储帐户或生成 SAS 定义操作失败，因为没有与密钥保管库中的用户2相对应的用户访问策略。

**解决**方法：若要解决此问题，请在 keyvault 授予用户2权限中为用户2创建一个用户访问策略，以便配置托管存储帐户和生成 SAS 定义。 可以使用以下 cmdlet 通过 Azure PowerShell 来执行此操作：

$userPrincipalId = $ (Get-azurermaduser-UserPrincipalName "user2_email_address" ) 。识别

Set-AzureRmKeyVaultAccessPolicy-VaultName "keyvaultname"-ObjectId $userPrincipalId-PermissionsToStorage 获取、列出、删除、设置、更新、regeneratekey、getsas、listsas、deletesas、setsas、恢复、备份、还原、清除


## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**错误 ID：** 181008

**错误消息：** VM： VMName。 错误：遇到状态 & "[" SnapshotReplication. SnapshotReplicationEngine + WaitingForArtefactsDisposalPreCycle " (" WaitingForArtefactsDisposalPreCycle ") ]" 的超时事件 "DisposeArtefactsTimeout"。

**可能的原因：**

尝试将数据复制到 Azure 的组件已关闭或未响应。 可能的原因包括：

- Azure Migrate 设备中运行的网关服务已关闭。
- 网关服务遇到与服务总线/事件中心/设备存储帐户的连接问题。

**确定 DisposeArtefactsTimedOut 的确切原因以及相应的解决方法：**

1. 确保 Azure Migrate 设备已启动并运行。
2. 检查设备上的网关服务是否正在运行：
   1.  使用远程桌面登录到 Azure Migrate 设备，并执行以下操作。

   2.  打开 Microsoft 服务 MMC 管理单元 (运行 > services.msc) ，并检查 "Microsoft Azure 网关服务" 是否正在运行。 如果服务已停止或未运行，请启动服务。 或者，可以打开命令提示符或 PowerShell，并执行以下操作： "Net Start asrgwy"

3. 检查 Azure Migrate 设备和设备存储帐户之间的连接问题： 

    下载 Azure Migrate 设备中的 azcopy 后，请运行以下命令：
    
    _azcopy 工作台 https：//[account]。 net/[container]？块_
    
    **运行性能基准测试的步骤：**
    
      1. [下载](https://go.microsoft.com/fwlink/?linkid=2138966) azcopy
        
      2. 在资源组中查找设备存储帐户。 存储帐户的名称类似于 migrategwsa \* \* \* \* \* \* \* \* \* \* 。 这是上述命令中参数 [account] 的值。
        
      3. 在 Azure 门户中搜索存储帐户。 确保用于搜索的订阅与在其中创建存储帐户)  (目标订阅相同。 在 "Blob 服务" 部分中转到容器。 单击 "+ 容器" 并创建容器。 将 "公共访问级别" 设置为默认的 "选定的值"。
        
      4. 中转到 "设置" 下的 "共享访问签名"。 选择 "允许的资源类型" 中的容器。 单击 "生成 SAS 和连接字符串"。 复制 SAS 值。
        
      5. 通过将帐户、容器、SAS 替换为在步骤2、3和4中获取的值，在命令提示符下执行以上命令。
        
      或者，将 Azure 存储资源管理器 [下载](https://go.microsoft.com/fwlink/?linkid=2138967) 到设备上，并尝试将 ~ 64 MB 的10个 blob 上传到存储帐户。 如果没有问题，则上传应成功。
        
    **解决方法：** 如果此测试失败，则会出现网络问题&#39;。 联系你的本地网络团队以检查连接问题。 通常，某些防火墙设置会导致失败。
    
4.  检查 Azure Migrate 设备和服务总线之间的连接问题：

    此测试检查 Azure Migrate 设备能否与 Azure Migrate 云服务后端通信。 设备通过服务总线和事件中心消息队列与服务后端通信。 若要验证是否可从设备连接到服务总线，请 [下载](https://go.microsoft.com/fwlink/?linkid=2139104) 服务总线资源管理器，尝试连接到设备服务总线并执行发送消息/接收消息。 如果没有问题，则应成功。

    **运行测试的步骤：**

    1. 从在迁移项目中创建的服务总线复制连接字符串
    2. 打开服务总线资源管理器
    3. 中转到文件，然后连接
    4. 粘贴连接字符串并单击 "连接"
    5. 这会打开服务总线命名空间
    6. 选择主题中的 Snapshot Manager。 右键单击 Snapshot Manager，选择 "接收消息" > 选择 "查看"，然后单击 "确定"
    7. 如果连接成功，则会在控制台输出中看到 "已收到 [x] 条消息"。 如果连接未成功，你将看到一条消息，指出连接失败
    
    **解决方法：** 如果此测试失败，则会出现网络问题。 联系你的本地网络团队以检查连接问题。 通常，某些防火墙设置会导致失败。

5. Azure Migrate 设备和 Azure Key Vault 之间的连接问题：

    此测试检查 Azure Migrate 设备和 Azure Key Vault 之间的连接问题。 Key Vault 用于管理用于复制的存储帐户访问。
    
    **检查连接性的步骤：**
    
    1. 从对应于 Azure Migrate 项目的资源组中的资源列表提取 Key Vault URI。
    
    1. 在 Azure Migrate 设备中打开 PowerShell，并运行以下命令：
    
    _test-netconnection Key Vault URI-P 443_
    
    此命令将尝试 TCP 连接，并将返回一个输出。
    
     - 在输出中，选中 "_TcpTestSucceeded_" 字段。 如果该值为 "_True_"，则 Azure Migrate 设备与 Azure Key Vault 之间不存在连接问题。 如果该值为 "False"，则存在连接问题。
    
    **解决方法：** 如果此测试失败，则 Azure Migrate 设备与 Azure Key Vault 之间存在连接问题。 联系你的本地网络团队以检查连接问题。 通常，某些防火墙设置会导致失败。
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**错误 ID：** 1011

**错误消息：** 为磁盘 DiskPath、DiskId 的虚拟机 VMName 数据上传;VMId 未在预期时间内完成。

此错误通常表示执行复制的 Azure Migrate 设备无法连接到 Azure 云服务，或者复制正在缓慢地导致复制周期超时。

可能的原因包括：

- Azure Migrate 设备已关闭。
- 设备上的复制网关服务未运行。
- 复制网关服务在以下用于复制的 Azure 服务组件之一出现连接问题：服务总线/事件中心/Azure 缓存存储帐户/Azure Key Vault。
- 尝试读取磁盘时，会在 vCenter 级别限制网关服务。

**确定根本原因并解决问题：**

1. 确保 Azure Migrate 设备已启动并运行。
2. 检查设备上的网关服务是否正在运行：
   1.  使用远程桌面登录到 Azure Migrate 设备，并执行以下操作。

   2.  打开 Microsoft 服务 MMC 管理单元 (运行 > services.msc) ，并检查 "Microsoft Azure 网关服务" 是否正在运行。 如果服务已停止或未运行，请启动服务。 或者，您可以打开命令提示符或 PowerShell，并执行以下操作： "Net Start asrgwy"。


3. **检查 Azure Migrate 设备和缓存存储帐户之间的连接问题：** 

    下载 Azure Migrate 设备中的 azcopy 后，请运行以下命令：
    
    _azcopy 工作台 https：//[account]。 net/[container]？块_
    
    **运行性能基准测试的步骤：**
    
      1. [下载](https://go.microsoft.com/fwlink/?linkid=2138966) azcopy
        
      2. 在资源组中查找设备存储帐户。 存储帐户的名称类似于 migratelsa \* \* \* \* \* \* \* \* \* \* 。 这是上述命令中参数 [account] 的值。
        
      3. 在 Azure 门户中搜索存储帐户。 确保用于搜索的订阅与在其中创建存储帐户)  (目标订阅相同。 在 "Blob 服务" 部分中转到容器。 单击 "+ 容器" 并创建容器。 将 "公共访问级别" 设置为默认的 "选定的值"。
        
      4. 中转到 "设置" 下的 "共享访问签名"。 选择 "允许的资源类型" 中的容器。 单击 "生成 SAS 和连接字符串"。 复制 SAS 值。
        
      5. 通过将帐户、容器、SAS 替换为在步骤2、3和4中获取的值，在命令提示符下执行以上命令。
        
      或者，将 Azure 存储资源管理器 [下载](https://go.microsoft.com/fwlink/?linkid=2138967) 到设备上，并尝试将 ~ 64 MB 的10个 blob 上传到存储帐户。 如果没有问题，则上传应成功。
        
    **解决方法：** 如果此测试失败，则会出现网络问题&#39;。 联系你的本地网络团队以检查连接问题。 通常，某些防火墙设置会导致失败。
                
4.  **Azure Migrate 设备与 Azure 服务总线之间的连接问题：**

    此测试将检查 Azure Migrate 设备是否可以与 Azure Migrate 云服务后端通信。 设备通过服务总线和事件中心消息队列与服务后端通信。 若要验证是否可从设备连接到服务总线，请 [下载](https://go.microsoft.com/fwlink/?linkid=2139104) 服务总线资源管理器，尝试连接到设备服务总线并执行发送消息/接收消息。 如果没有问题，则应成功。

    **运行测试的步骤：**
    
    1. 从与 Azure Migrate 项目对应的资源组中创建的服务总线复制连接字符串
    
    1. 打开服务总线资源管理器
    
    1. 中转到 File > Connect
    
    1. 粘贴在步骤1中复制的连接字符串，然后单击 "连接"
    
    1. 这会打开服务总线命名空间。
    
    1. 在命名空间中的主题中选择 Snapshot Manager。 右键单击 Snapshot Manager，选择 "接收消息" > 选择 "查看"，然后单击 "确定"。
    
    如果连接成功，则会在控制台输出中看到 "已收到 [x] 条消息"。 如果连接未成功，你将看到一条消息，指出连接失败。
    
    **解决方法：** 如果此测试失败，则 Azure Migrate 设备和服务总线之间存在连接问题。 联系你的本地网络团队以检查这些连接问题。 通常，某些防火墙设置会导致失败。
    
 5. **Azure Migrate 设备和 Azure Key Vault 之间的连接问题：**

    此测试检查 Azure Migrate 设备和 Azure Key Vault 之间的连接问题。 Key Vault 用于管理用于复制的存储帐户访问。
    
    **检查连接性的步骤：**
    
    1. 从对应于 Azure Migrate 项目的资源组中的资源列表提取 Key Vault URI。
    
    1. 在 Azure Migrate 设备中打开 PowerShell，并运行以下命令：
    
    _test-netconnection Key Vault URI-P 443_
    
    此命令将尝试 TCP 连接，并将返回一个输出。
    
    1. 在输出中，选中 "_TcpTestSucceeded_" 字段。 如果该值为 "_True_"，则 Azure Migrate 设备与 Azure Key Vault 之间不存在连接问题。 如果该值为 "False"，则存在连接问题。
    
    **解决方法：** 如果此测试失败，则 Azure Migrate 设备与 Azure Key Vault 之间存在连接问题。 联系你的本地网络团队以检查连接问题。 通常，某些防火墙设置会导致失败。
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>尝试提取已更改的块时遇到错误

错误消息： "尝试提取更改块时遇到错误"

无代理复制方法使用 VMware 的已更改的块跟踪技术 (CBT) 将数据复制到 Azure。 CBT 允许服务器迁移工具只跟踪和复制自上次复制循环以来发生更改的块。 如果重置复制虚拟机的更改后的块跟踪或更改后的块跟踪文件已损坏，则会发生此错误。

可以通过以下两种方式解决此错误：

- 如果已选择 "自动修复复制"，则在触发 VM 复制时选择 "是" 时，该工具将尝试修复它。 右键单击 VM，然后选择 "修复复制"。
- 如果未选择 "自动修复复制" 或上述步骤不适用于你，请停止虚拟机的复制，在虚拟机上 [重置更改的阻止跟踪](https://go.microsoft.com/fwlink/?linkid=2139203) ，然后重新配置复制。

[VMWARE KB 2048201](https://go.microsoft.com/fwlink/?linkid=2138888)中介绍了可能导致 VMware vSphere 5.5 上的虚拟机的 CBT 重置的一个此类问题：在 vSphere 1.x 中进行存储 vMotion 操作后，更改了阻止跟踪。 如果你使用的是 VMware vSphere 5.5，请确保使用此知识库中描述的更新。

或者，你可以使用 VMware PowerCLI 在虚拟机上 [重置 VMware 更改的块跟踪]。

## <a name="an-internal-error-occurred"></a>发生了内部错误

有时，你可能会遇到由于 VMware 环境/API 问题而发生的错误。 我们已将以下错误集识别为 VMware 环境相关错误。 这些错误具有固定格式。

_错误消息：出现内部错误。[错误消息]_

例如：错误消息：出现内部错误。 [检测到无效的快照配置]。

以下部分列出了一些常见的 VMware 错误，以及如何缓解这些错误。

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>错误消息：出现内部错误。 [服务器拒绝连接]

此问题是已知的 VMware 问题，并在 VDDK 6.7 中发生。 需要停止在 Azure Migrate 设备上运行的网关服务， [从 VMWARE KB 下载更新](https://go.microsoft.com/fwlink/?linkid=2138889)，然后重新启动网关服务。

停止网关服务的步骤：

1. 按 Windows + R，打开 "services.msc"。 单击 "Microsoft Azure 网关服务"，并将其停止。
2. 或者，可以打开命令提示符或 PowerShell，并执行以下操作： Net Stop asrgwy。 请确保等待，直到收到该服务不再运行的消息。

启动网关服务的步骤：

1. 按 Windows + R，打开 "services.msc"。 右键单击 "Microsoft Azure 网关服务"，然后启动该服务。
2. 或者，可以打开命令提示符或 PowerShell，并执行以下操作： Net Start asrgwy。

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>错误消息：出现内部错误。 [' 检测到无效的快照配置。 ']

如果有包含多个磁盘的虚拟机，则在从虚拟机中删除磁盘时可能会遇到此错误。 若要修正此问题，请参阅 [此 VMware 文章](https://go.microsoft.com/fwlink/?linkid=2138890)中的步骤。

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>错误消息：出现内部错误。 [生成快照挂起]

当快照生成停止响应时，会出现此问题。 发生此问题时，可以看到创建快照任务在95% 或99% 停止。 请参阅此 [VMWARE KB](https://go.microsoft.com/fwlink/?linkid=2138969) ，以解决此问题。

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>错误消息：出现内部错误。 [未能合并 VM 上的磁盘 _[原因]_]

在复制周期结束时合并磁盘时，操作将失败。 按照 [VMWARE KB](https://go.microsoft.com/fwlink/?linkid=2138970) 中的说明进行操作 _，以解决_ 问题。

当与 VMware 快照相关的操作（创建、删除或合并磁盘）失败时，会出现以下错误。 按照下一节中的指导修正错误：

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>错误消息：出现内部错误。 [另一个任务已经在进行中]

如果后台中运行的虚拟机任务冲突，或者 vCenter Server 中的任务超时，则会出现此问题。遵循以下 [VMWARE KB](https://go.microsoft.com/fwlink/?linkid=2138891)提供的解决方案。

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>错误消息：出现内部错误。 [当前状态中不允许操作]

VCenter Server 管理代理停止工作时，会出现此问题。 若要解决此问题，请参阅以下 [VMWARE KB](https://go.microsoft.com/fwlink/?linkid=2138971)中的解决方案。

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>错误消息：出现内部错误。 [快照磁盘大小无效]

这是一个已知的 VMware 问题，快照指示的磁盘大小为零。 按照 [VMWARE KB](https://go.microsoft.com/fwlink/?linkid=2138972)中提供的解决方法进行操作。

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>错误消息：出现内部错误。 [内存分配失败。 内存不足。]

当 NFC 主机缓冲区内存不足时，会发生这种情况。 若要解决此问题，需要将 VM (计算 vMotion) 转移到具有可用资源的其他主机。

## <a name="next-steps"></a>后续步骤

继续 VM 复制，并执行 [测试迁移](https://go.microsoft.com/fwlink/?linkid=2139333)。