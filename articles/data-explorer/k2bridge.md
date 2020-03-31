---
title: 使用 Kibana 可视化 Azure 数据资源管理器中的数据
description: 在本文中，您将了解如何将 Azure 数据资源管理器设置为 Kibana 的数据源
author: orspod
ms.author: orspodek
ms.reviewer: guregini
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fac9c78607e50dca384670bf4cc08b50f723312b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065616"
---
# <a name="visualize-data-from-azure-data-explorer-in-kibana-with-the-k2bridge-open-source-connector"></a>使用 K2Bridge 开源连接器可视化基巴纳 Azure 数据资源管理器的数据

K2Bridge（基巴纳-库斯托桥）使您能够使用 Azure 数据资源管理器作为数据源，并在基巴纳中可视化该数据。 K2Bridge 是一个[开源](https://github.com/microsoft/K2Bridge)容器化应用程序，充当 Kibana 实例和 Azure 数据资源管理器群集之间的代理。 本文介绍如何使用 K2Bridge 创建该连接。

K2Bridge 将基巴纳查询转换为 Kusto 查询语言 （KQL），并将 Azure 数据资源管理器结果发送回基巴纳。 

   ![图表](media/k2bridge/k2bridge-chart.png)

K2Bridge 支持基巴纳的"发现"选项卡，您可以在其中：
* 搜索和浏览数据
* 筛选结果
* 添加或删除结果网格中的字段
* 查看记录内容
* 保存和共享搜索

下图显示了 K2Bridge 绑定到 Azure 数据资源管理器的 Kibana 实例。 基巴纳的用户体验保持不变。

   [![基巴纳页面](media/k2bridge/k2bridge-kibana-page.png)](media/k2bridge/k2bridge-kibana-page.png#lightbox)

## <a name="prerequisites"></a>先决条件

在将来自 Kibana 中的 Azure 数据资源管理器的数据可视化之前，准备好以下操作：

* [赫尔姆V3，](https://github.com/helm/helm#install)库伯内斯包经理
* Azure 库伯奈斯服务 （AKS） 群集或任何其他库伯奈斯群集（版本 1.14 到版本 1.16 已过测试和验证）。 如果需要 AKS 群集，请参阅[使用 Azure CLI](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough)或使用[Azure 门户](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal)部署 AKS 群集
* [Azure 数据资源管理器群集](create-cluster-database-portal.md)， 包括：
    * Azure 数据资源管理器群集的 URL 
    * 数据库名称
    
* 受权在 Azure 数据资源管理器中查看数据的 Azure AD 服务主体，包括：
    * 客户端 ID 
    * 客户端机密

    建议使用具有"查看器"权限的服务主体。 建议使用更高的权限。

    * [为 Azure AD 服务主体设置群集的视图权限](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)。

    有关 Azure AD 服务主体的详细信息，请参阅[创建 Azure AD 服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)。

## <a name="run-k2bridge-on-azure-kubernetes-service-aks"></a>在 Azure 库伯奈斯服务 （AKS） 上运行 K2Bridge

默认情况下，K2Bridges 的 Helm 图表引用位于 Microsoft 容器注册表 （MCR） 上的公开图像。 MCR 不需要任何凭据，并且开箱即用。

1. 下载所需的头盔图表。

1. 将弹性搜索依赖项添加到 Helm。 
    弹性搜索依赖项的原因是 K2Bridge 使用内部小型弹性搜索实例来为元数据相关请求（如索引模式和保存的查询）提供服务。 此内部实例中不保存任何业务数据，它可以被视为实现详细信息。 

    1. 要将弹性搜索依赖项添加到 Helm：

        ```bash
        helm repo add elastic https://helm.elastic.co
        helm repo update
        ```

    1. 要从 GitHub 存储库的图表目录下获取 K2Bridge 图表，请执行以下操作：
        1. 从[GitHub](https://github.com/microsoft/K2Bridge)克隆存储库 。
        1. 转到 K2Bridges 根存储库目录。
        1. 运行：

            ```bash
            helm dependency update charts/k2bridge
            ```

1. 部署 K2Bridge：

    1. 使用环境的正确值设置变量：

        ```bash
        ADX_URL=[YOUR_ADX_CLUSTER_URL] #For example, https://mycluster.westeurope.kusto.windows.net
        ADX_DATABASE=[YOUR_ADX_DATABASE_NAME]
        ADX_CLIENT_ID=[SERVICE_PRINCIPAL_CLIENT_ID]
        ADX_CLIENT_SECRET=[SERVICE_PRINCIPAL_CLIENT_SECRET]
        ADX_TENANT_ID=[SERVICE_PRINCIPAL_TENANT_ID]
        ```

    1. （可选）启用 Azure 应用程序见解遥测。 
        如果这是首次使用 Azure 应用程序见解，则应首先[创建应用程序见解资源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。 您需要将[检测密钥复制到](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#copy-the-instrumentation-key)变量： 

        ```bash
        APPLICATION_INSIGHTS_KEY=[INSTRUMENTATION_KEY]
        COLLECT_TELEMETRY=true
        ```

    1. <a name="install-k2bridge-chart"></a>安装 K2Bridge 图表：

        ```bash
        helm install k2bridge charts/k2bridge -n k2bridge --set image.repository=$REPOSITORY_NAME/$CONTAINER_NAME --set settings.adxClusterUrl="$ADX_URL" --set settings.adxDefaultDatabaseName="$ADX_DATABASE" --set settings.aadClientId="$ADX_CLIENT_ID" --set settings.aadClientSecret="$ADX_CLIENT_SECRET" --set settings.aadTenantId="$ADX_TENANT_ID" [--set image.tag=latest] [--set privateRegistry="$IMAGE_PULL_SECRET_NAME"] [--set settings.collectTelemetry=$COLLECT_TELEMETRY]
        ```

        在["配置"](https://github.com/microsoft/K2Bridge/blob/master/docs/configuration.md)中，您可以找到完整的配置选项集。

    1. 命令输出将建议运行下一个 Helm 命令以部署基巴纳。 可以选择运行：

        ```bash
        helm install kibana elastic/kibana -n k2bridge --set image=docker.elastic.co/kibana/kibana-oss --set imageTag=6.8.5 --set elasticsearchHosts=http://k2bridge:8080
        ```
        
    1. 使用端口转发访问本地主机上的 Kibana： 

        ```bash
        kubectl port-forward service/kibana-kibana 5601 --namespace k2bridge
        ```
        
    1. 通过浏览连接到http://127.0.0.1:5601基巴纳。

    1. 向最终用户公开基巴纳。 有多种方法可以执行此操作。 您使用的方法很大程度上取决于您的用例。

        例如：

        将服务公开为负载平衡器服务。 为此，请将`--set service.type=LoadBalancer`参数添加到 K2Bridge Helm 安装命令 （[上）。](#install-k2bridge-chart)        
    
        然后运行：
        
        ```bash
        kubectl get service -w -n k2bridge
        ```
        
        输出应如下所示： 

        ```bash
        NAME            TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
        kibana-kibana   LoadBalancer   xx.xx.xx.xx   <pending>      5601:30128/TCP   4m24s
        ```
 
        然后，您可以使用显示的生成的外部 IP，并通过打开浏览器访问`<EXTERNAL-IP>:5601`基巴纳来访问 Kibana。

1. 配置索引模式以访问数据：  
在新的 Kibana 实例中：
     1. 打开基巴纳
     1. 导航到管理层。
     1. 选择**索引模式**。 
     1. 创建索引模式。
索引的名称必须与表名称或函数名称完全匹配，而不带星号。 可以从列表中复制相关行。

> [!Note]
> 要在其他 Kubernetes 提供程序上运行，更改 中的`values.yaml`弹性搜索存储 ClassName 以适合提供程序建议的存储。

## <a name="visualize-data"></a>可视化数据

当 Azure 数据资源管理器配置为 Kibana 的数据源时，可以使用 Kibana 来浏览数据。 

1. 在左侧菜单的 Kibana 中，选择"**发现**"选项卡。

1. 从左侧下拉列表中选择索引模式（在本例中为 Azure 数据资源管理器表），该模式定义要浏览的数据源。
    
   ![选择索引模式](media/k2bridge/k2bridge-select-an-index-pattern.png)

1. 如果数据具有时间筛选器字段，则可以指定时间范围。 在页面的右上角，设置时间筛选器。 默认情况下，"发现"显示过去 15 分钟的数据。

   ![时间筛选器](media/k2bridge/k2bridge-time-filter.png)
    
1. 结果表显示前 500 条记录。 您可以展开文档以 JSON 或表格式检查其字段数据。

   ![展开记录](media/k2bridge/k2bridge-expand-record.png)

1. 默认情况下，结果表包括文档_source和时间字段（如果存在）的列。 您可以通过选择左侧边栏中字段名称旁边的**添加**，选择要添加到结果表的特定列。

   ![特定列](media/k2bridge/k2bridge-specific-columns.png)
    
1. 在查询栏中，您可以通过以下条件搜索数据：
    * 输入搜索词
    * 使用 Lucene 查询语法。 
    例如：
        * 搜索"错误"以查找包含此值的所有记录。 
        * 搜索"状态：200"，获取状态值为 200 的所有记录。 
    * 使用逻辑运算符（AND、OR、不）
    * 使用通配符（星号""\*或问号"？"）例如：
        * 查询`"destination_city: L*"`将匹配目标城市值以"l"开头的记录（K2Bridge 不区分大小写）。

    ![运行查询](media/k2bridge/k2bridge-run-query.png)
    
    > [!Tip]
    > 在[搜索](https://github.com/microsoft/K2Bridge/blob/master/docs/searching.md)中，可以找到更多的搜索规则和逻辑。

1. 要筛选搜索结果，请使用页面右侧栏上的**字段列表**。 
    字段列表是您可以看到的位置：
    * 字段的前五个值
    * 包含字段的记录数
    * 包含每个值的记录的百分比。 
    
    >[!Tip]
    > 使用 （+） 放大镜图标查找具有特定值的所有记录。
    
    ![字段列表](media/k2bridge/k2bridge-field-list.png)
   
    还可以使用结果表中每个记录的结果表格式视图中的 （+） 放大镜图标筛选结果。
    
     ![表列表](media/k2bridge/k2bridge-table-list.png)
    
1. 选择"**保存**"或"**共享**搜索"。

     ![保存搜索](media/k2bridge/k2bridge-save-search.png)
