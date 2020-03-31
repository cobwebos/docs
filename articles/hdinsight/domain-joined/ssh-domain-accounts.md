---
title: 在 Azure HDInsight 中管理域帐户的 SSH 访问权限
description: 在 HDInsight 中管理 Azure AD 帐户的 SSH 访问的步骤。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472513"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>在 Azure HDInsight 中管理域帐户的 SSH 访问权限

默认情况下，在安全群集上[，Azure AD DS](../../active-directory-domain-services/overview.md)中的所有域用户都允许[SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)进入头节点和边缘节点。 这些用户不是 sudoers 组的一部分，并且不会获取根访问权限。 在群集创建期间创建的 SSH 用户将具有根访问权限。

## <a name="manage-access"></a>管理访问权限

要修改对特定用户或组的 SSH 访问权限`/etc/ssh/sshd_config`，请在每个节点上更新。

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到群集。 编辑以下命令（将 CLUSTERNAME 替换为群集的名称），然后输入该命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 打开`ssh_confi`g 文件。

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. 根据需要修改`sshd_config`文件。 如果将用户限制为特定组，则本地帐户无法将 SSH 放入该节点。 以下只是语法的示例：

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    然后保存更改： **Ctrl = X**， **Y**，**输入**。

1. 重新启动 sshd。

    ```bash
    sudo systemctl restart sshd
    ```

1. 每个节点重复上述步骤。

## <a name="ssh-authentication-log"></a>SSH 身份验证日志

SSH 身份验证日志写入`/var/log/auth.log`。 如果您看到本地或域帐户通过 SSH 出现任何登录失败，则需要通过日志来调试错误。 通常，问题可能与特定用户帐户相关，通常最好使用默认 SSH 用户（本地帐户）尝试其他用户帐户或 SSH，然后尝试 kinit。

## <a name="ssh-debug-log"></a>SSH 调试日志

要启用详细日志记录，您需要使用 该`sshd``-d`选项重新启动。 就像`/usr/sbin/sshd -d`您也可以在自定义端口`sshd`（如 2222）上运行一样，您不必停止主 SSH 守护进程。 您还可以使用`-v`SSH 客户端选项获取更多日志（故障的客户端视图）。

## <a name="next-steps"></a>后续步骤

* [使用企业安全性套餐管理 HDInsight 群集](./apache-domain-joined-manage.md)
* [使用 SSH 连接到 HDInsight （阿帕奇 Hadoop）。](../hdinsight-hadoop-linux-use-ssh-unix.md)
