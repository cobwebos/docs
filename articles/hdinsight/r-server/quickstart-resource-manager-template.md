---
title: 快速入门：使用模板创建 ML Services 群集 - Azure HDInsight
description: 本快速入门介绍如何使用 Azure 资源管理器模板在 Azure HDInsight 中创建一个 ML Services 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 85e46c0056b3bcf00544786f639fd7003ceeb7bd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "79475140"
---
# <a name="quickstart-create-ml-services-cluster-in-azure-hdinsight-using-resource-manager-template"></a>快速入门：使用资源管理器模板在 Azure HDInsight 中创建 ML Services 群集

在本快速入门中，我们使用 Azure 资源管理器模板在 Azure HDInsight 中创建一个 [ML Services](./r-server-overview.md) 群集。 可以在 Azure 中创建 HDInsight 群集时选择使用 Microsoft Machine Learning Server 部署。 提供此选项的群集类型名为 ML Services。 这项功能可让数据科研人员、统计人员和 R 程序员根据需要访问 HDInsight 上可缩放的分布式分析方法。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-ml-services-cluster"></a>创建 ML Services 群集

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-rserver)。

:::code language="json" source="~/quickstart-templates/101-hdinsight-rserver/azuredeploy.json" range="1-171":::


该模板中定义了两个 Azure 资源：

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts)：创建 Azure 存储帐户。
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters)：创建 HDInsight 群集。

### <a name="deploy-the-template"></a>部署模板

1. 选择下面的“部署到 Azure”按钮以登录到 Azure，并打开资源管理器模板  。

    [![部署到 Azure](./media/quickstart-resource-manager-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-rserver%2Fazuredeploy.json)

1. 输入或选择下列值：

    |properties |说明 |
    |---|---|
    |订阅|从下拉列表中选择用于此群集的 Azure 订阅。|
    |资源组|从下拉列表中选择现有资源组，或选择“新建”  。|
    |位置|将使用用于资源组的位置自动填充此值。|
    |群集名称|输入任何全局唯一的名称。 对于此模板，请只使用小写字母和数字。|
    |群集登录用户名|提供用户名，默认值为 **admin**。|
    |群集登录密码|提供密码。 密码长度不得少于 10 个字符，且至少必须包含一个数字、一个大写字母和一个小写字母、一个非字母数字字符（' " ` 字符除外）。 |
    |SSH 用户名|提供用户名，默认值为 sshuser|
    |SSH 密码|提供密码。|

    ![资源管理器模板 HBase](./media/quickstart-resource-manager-template/resource-manager-template-rserver.png)

1. 查看“条款和条件”  。 接着选择“我同意上述条款和条件”，然后选择“购买”   。 你会收到一则通知，说明正在进行部署。 创建群集大约需要 20 分钟时间。

## <a name="review-deployed-resources"></a>查看已部署的资源

创建群集后，你会收到“部署成功”的通知，通知中附有“转到资源”链接   。 “资源组”页面会列出新的 HDInsight 群集以及与该群集关联的默认存储。 每个群集都有一个 [Azure 存储帐户](../hdinsight-hadoop-use-blob-storage.md)或 [Azure Data Lake Storage 帐户](../hdinsight-hadoop-use-data-lake-store.md)依赖项。 该帐户称为默认存储帐户。 HDInsight 群集及其默认存储帐户必须共存于同一个 Azure 区域中。 删除群集不会删除存储帐户。

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，可以删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。

从 Azure 门户导航到群集，然后选择“删除”。 

![资源管理器模板 HBase](./media/quickstart-resource-manager-template/azure-portal-delete-rserver.png)

还可以选择资源组名称来打开“资源组”页，然后选择“删除资源组”  。 通过删除资源组，可以删除 HDInsight 群集和默认存储帐户。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解了如何使用资源管理器模板在 HDInsight 中创建 ML Services 群集。 下篇文章介绍如何使用 RStudio Server 运行 R 脚本，以演示如何使用 Spark 进行分布式 R 计算。

> [!div class="nextstepaction"]
> [使用 RStudio Server 在 Azure HDInsight 中的 ML Services 群集上执行 R 脚本](./machine-learning-services-quickstart-job-rstudio.md)
