---
title: 使用 Kibana 从 Azure 数据资源管理器显示数据
description: 本文介绍如何将 Azure 数据资源管理器设置为 Kibana 的数据源
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 30d74f36c6462d1fba039595d2ed6fe722b742e8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79164808"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>将 Kibana 中 Azure 数据资源管理器的数据与 K2Bridge 开源连接器可视化

K2Bridge （Kibana-Kusto Bridge）使你能够使用 Azure 数据资源管理器作为数据源，并将该数据可视化到 Kibana 中。 K2Bridge 是一个[开源](https://github.com/microsoft/K2Bridge)容器化应用程序，它充当 Kibana 实例和 Azure 数据资源管理器群集之间的代理。 本文介绍如何使用 K2Bridge 创建该连接。

K2Bridge 将 Kibana 查询转换为 Kusto 查询语言（KQL），并将 Azure 数据资源管理器结果发送回 Kibana。 

   ![图表](media/k2bridge/k2bridge-chart.png)

K2Bridge 支持 Kibana 的 "发现" 选项卡，你可以在其中执行以下操作：
* 搜索和浏览数据
* 筛选结果
* 在结果网格中添加或删除字段
* 查看记录内容
* 保存和共享搜索

下图显示了由 K2Bridge 绑定到 Azure 数据资源管理器的 Kibana 实例。 Kibana 中的用户体验不变。

   [![Kibana 页](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>必备条件

若要在 Kibana 中对 Azure 数据资源管理器中的数据进行可视化，请准备好以下内容：

* [Helm V3](https://github.com/helm/helm#install)，Kubernetes 包管理器
* Azure Kubernetes 服务（AKS）群集或其他任何 Kubernetes 群集（版本1.14 到版本1.16 的测试和验证）。 如果需要 AKS 群集，请参阅[使用 Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough)部署 AKS 群集或[使用 Azure 门户](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)
* [Azure 数据资源管理器群集](create-cluster-database-portal.md)，其中包括：
    * Azure 数据资源管理器群集的 URL 
    * 数据库名称
    
* 有权查看 Azure 数据资源管理器中数据的 Azure AD 服务主体，包括：
    * 客户端 ID 
    * 客户端密码

    建议使用具有 "查看器" 权限的服务主体。 不建议使用较高的权限。

    * [为 Azure AD 服务主体设置群集的 "查看" 权限](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)。

    有关 Azure AD 服务主体的详细信息，请参阅[创建 Azure AD 服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)。

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务（AKS）上运行 K2Bridge

默认情况下，K2Bridges's Helm chart 引用位于 Microsoft 容器注册表（MCR）上的公开可用图像。 MCR 不需要任何凭据，而是现成的。

1. 下载所需的 Helm 图表。

1. 将 Elasticsearch 依赖项添加到 Helm。 
    Elasticsearch 依赖关系的原因在于，K2Bridge 使用内部小型 Elasticsearch 实例来处理与元数据相关的请求（如索引模式和保存的查询）。 此内部实例中不保存任何业务数据，可以将其视为实现细节。 

    1. 将 Elasticsearch 依赖项添加到 Helm：

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. 若要从 GitHub 存储库的图表目录下获取 K2Bridge 图表：
        1. 从[GitHub](https://github.com/microsoft/K2Bridge)克隆存储库。
        1. 请参阅 K2Bridges 根存储库目录。
        1. 运行：

            ```bash
            helm dependency update charts/k2bridge
            ```

1. 部署 K2Bridge：

    1. 为环境设置具有正确值的变量：

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. 可有可无启用 Azure 应用程序 Insights 遥测。 
        如果这是你第一次使用 Azure 应用程序 Insights，则应首先[创建 Application Insights 资源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。 需要将[检测密钥复制](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key)到变量： 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>安装 K2Bridge 图表：

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        在 "[配置](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md)" 中，可以找到完整的一组配置选项。

    1. 命令输出将建议要运行的下一个 Helm 命令来部署 Kibana。 （可选）运行：

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
    1. 使用端口转发访问 localhost 上的 Kibana： 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
    1. 通过浏览到 http://127.0.0.1:5601连接到 Kibana。

    1. 向最终用户公开 Kibana。 有多种方法可实现此目的。 你使用的方法很大程度上取决于你的用例。

        例如：

        将服务作为 LoadBalancer 服务公开。 为此，请将以下参数添加到 K2Bridge Helm 安装命令（[如上](#install-k2bridge-chart)所示）：

        `--set service.type=LoadBalancer`
    
        然后运行：

           ```bash
           kubectl get service -w -n k2bridge
           ```   
        输出应如下所示： 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
        然后，可以使用出现的生成的外部 IP，并使用它通过打开浏览器 `\<EXTERNAL-IP>:5601`来访问 Kibana。

1. 配置索引模式以访问数据：  
在新的 Kibana 实例中：
     1. 打开 Kibana。
     1. 导航到 "管理"。
     1. 选择 "**索引模式**"。 
     1. 创建索引模式。
索引名称必须与表名称或函数名称完全匹配，不含星号。 您可以从列表中复制相关行。

> [!Note]
> 若要在其他 Kubernetes 提供程序上运行，请在 `values.yaml` 中更改 Elasticsearch storageClassName，使其适合提供程序所建议的项。

## <a name="visualize-data"></a>可视化数据

将 Azure 数据资源管理器配置为 Kibana 的数据源时，可以使用 Kibana 来浏览数据。 

1. 在 Kibana 的左侧菜单中，选择 "**发现**" 选项卡。

1. 从左侧下拉列表中，选择用于定义要浏览的数据源的索引模式（在本例中为 "Azure 数据资源管理器表"）。
    
   ![选择索引模式](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. 如果你的数据具有时间筛选器字段，你可以指定时间范围。 在页面的右上角，设置时间筛选器。 默认情况下，发现将显示过去15分钟的数据。

   ![时间筛选器](media/k2bridge/k2bridge-time-filter.png)
    
1. 结果表显示前500个记录。 您可以扩展文档以使用 JSON 或表格式检查其字段数据。

   ![展开记录](media/k2bridge/k2bridge-expand-record.png)

1. 默认情况下，结果表包含文档的列 _source 和时间字段（如果存在）。 您可以选择要添加到结果表中的特定列，方法是在左侧栏中选择字段名称旁边的 "**添加**"。

   ![特定列](media/k2bridge/k2bridge-specific-columns.png)
    
1. 在查询栏中，可以通过以下方式搜索数据：
    * 输入搜索词
    * 使用 Lucene 查询语法。 
    例如：
        * 搜索 "错误" 可查找所有包含此值的记录。 
        * 搜索 "status： 200" 以获取状态值为200的所有记录。 
    * 使用逻辑运算符（AND、OR、NOT）
    * 使用通配符（星号 "\*" 或问号 "？"）例如：
        * 查询 `"destination_city: L*"` 将匹配目标城市值以 "l" 开头的记录（K2Bridge 不区分大小写）。

    ![运行查询](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > 在[搜索](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)中，可以找到更多搜索规则和逻辑。

1. 若要筛选搜索结果，请使用页面右侧栏中的 "**字段列表**"。 
    可以在字段列表中查看：
    * 字段的前五个值
    * 包含字段的记录数
    * 包含每个值的记录的百分比。 
    
    >[!Tip]
    > 使用 "（+）放大镜" 图标可查找所有具有特定值的记录。
    
    ![字段列表](media/k2bridge/k2bridge-field-list.png)
   
    你还可以使用 "结果" 表中每个记录的 "结果" 表格式视图中的 "（+）放大镜" 图标来筛选结果。
    
     ![表列表](media/k2bridge/k2bridge-table-list.png)
    
1. 选择 "**保存**" 或 "**共享**搜索"。

     ![保存搜索](media/k2bridge/k2bridge-save-search.png)
