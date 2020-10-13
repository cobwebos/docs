---
title: 教程：在 Azure HDInsight 中配置 Apache Ambari 电子邮件通知
description: 本文介绍了如何将 SendGrid 与 Apache Ambari 配合使用来发送电子邮件通知。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 40f8c36b197b0c68b9f04a02dc7731877b27ddd2
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541656"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>教程：在 Azure HDInsight 中配置 Apache Ambari 电子邮件通知

在本教程中，你将使用 SendGrid 配置 Apache Ambari 电子邮件通知。 [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) 提供了易于使用的 Web UI 和 REST API，从而简化了 HDInsight 群集的管理和监视。 Ambari 包括在 HDInsight 群集中，用于监视群集和进行配置更改。 [SendGrid](https://sendgrid.com/solutions/) 是一项基于云的免费电子邮件服务，该服务提供可靠的事务性电子邮件传递、可缩放性、实时分析以及可用于简化自定义集成的灵活的 API。 Azure 客户每月可解锁 25,000 封免费电子邮件。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 获取 Sendgrid 用户名
> * 配置 Apache Ambari 电子邮件通知

## <a name="prerequisites"></a>先决条件

* 一个 SendGrid 电子邮件帐户。 有关说明，请参阅[如何在 Azure 中使用 SendGrid 发送电子邮件](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email)。

* HDInsight 群集。 请参阅[使用 Azure 门户创建 Apache Hadoop 群集](./hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="obtain-sendgrid-username"></a>获取 SendGrid 用户名

1. 从 [Azure 门户](https://portal.azure.com)中，导航到你的 SendGrid 资源。

1. 从“概述”页中，选择“管理”  ，以转到你的帐户的 SendGrid 网页。

    ![Azure 门户中的 SendGrid 概述](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. 在左侧菜单中，导航到你的帐户名称，然后导航到“帐户详细信息”  。

    ![SendGrid 仪表板导航](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. 在“帐户详细信息”  页上，记下“用户名”  。

    ![SendGrid 帐户详细信息](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>配置 Ambari 电子邮件通知

1. 在 Web 浏览器中导航到 `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts`，其中的 `CLUSTERNAME` 是群集的名称。

1. 从“操作”  下拉列表中，选择“管理通知”。 

1. 从“管理警报通知”  窗口中，选择 **+** 图标。

    ![屏幕截图显示了“管理警报通知”对话框。](./media/apache-ambari-email/azure-portal-create-notification.png)

1. 在“创建警报通知”  对话框中，提供以下信息：

    |属性 |说明 |
    |---|---|
    |名称|为通知提供一个名称。|
    |组|根据需要进行配置。|
    |严重性|根据需要进行配置。|
    |说明|可选。|
    |方法|保留为“电子邮件”  。|
    |电子邮件收件人|提供要接收通知的电子邮件，用逗号分隔。|
    |SMTP 服务器|`smtp.sendgrid.net`|
    |SMTP 端口|25 或 587（适用于未加密的/TLS 连接）。|
    |电子邮件发件人|提供电子邮件地址。 该地址不需要是真实的。|
    |使用身份验证|选中此复选框。|
    |用户名|提供 SendGrid 用户名。|
    |密码|提供你在 Azure 中创建 SendGrid 资源时使用的密码。|
    |密码确认|重新输入密码。|
    |启动 TLS|选中此复选框|

    ![屏幕截图显示了“创建警报通知”对话框。](./media/apache-ambari-email/ambari-create-alert-notification.png)

    选择“保存”。  你将返回到“管理警报通知”  窗口。

1. 从“管理警报通知”  窗口中，选择“关闭”  。

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何使用 SendGrid 配置 Apache Ambari 电子邮件通知。 使用以下内容，详细了解 Apache Ambari：

* [使用 Apache Ambari Web UI 管理 HDInsight 群集](./hdinsight-hadoop-manage-ambari.md)

* [创建警报通知](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)
