---
title: 在 Azure HDInsight 中管理域帐户的 SSH 访问
description: 管理 HDInsight 中 Azure AD 帐户的 SSH 访问权限的步骤。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472513"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>在 Azure HDInsight 中管理域帐户的 SSH 访问

默认情况下，在安全群集上，允许[AZURE AD DS](../../active-directory-domain-services/overview.md)中的所有域用户通过[SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到头节点和边缘节点。 这些用户不是 sudoers 组的成员，也不会获得根访问权限。 在群集创建过程中创建的 SSH 用户将具有根访问权限。

## <a name="manage-access"></a>管理访问权限

若要修改 SSH 对特定用户或组的访问权限，请更新每个节点上的 `/etc/ssh/sshd_config`。

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到群集。 将 CLUSTERNAME 替换为群集名称，然后输入以下命令，以编辑以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 打开 `ssh_confi`g 文件。

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. 根据需要修改 `sshd_config` 文件。 如果将用户限制为特定组，则本地帐户无法通过 SSH 连接到该节点。 下面只是一个语法示例：

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    然后保存更改： **Ctrl + X**、 **Y**、 **Enter**。

1. 重新启动 sshd。

    ```bash
    sudo systemctl restart sshd
    ```

1. 为每个节点重复以上步骤。

## <a name="ssh-authentication-log"></a>SSH 身份验证日志

SSH 身份验证日志将写入 `/var/log/auth.log`。 如果通过 SSH 为本地帐户或域帐户显示任何登录失败，则需要通过日志来调试错误。 通常，此问题可能与特定的用户帐户有关，并且通常是使用默认的 SSH 用户（本地帐户）尝试其他用户帐户或 SSH，然后尝试 kinit。

## <a name="ssh-debug-log"></a>SSH 调试日志

若要启用详细日志记录，需要使用 `-d` 选项重新启动 `sshd`。 与 `/usr/sbin/sshd -d` 一样，你还可以在自定义端口（例如2222）上运行 `sshd`，以便无需停止主 SSH 后台程序。 你还可以将 `-v` 选项与 SSH 客户端一起使用，以获取更多日志（故障的客户端视图）。

## <a name="next-steps"></a>后续步骤

* [用企业安全性套餐管理 HDInsight 群集](./apache-domain-joined-manage.md)
* [使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。
