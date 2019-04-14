---
title: 快速入门：将数据从 Logstash 引入 Azure 数据资源管理器
description: 本快速入门介绍如何将数据从 Logstash 引入（加载）到 Azure 数据资源管理器
author: tamirkamara
ms.author: takamara
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 01/14/2019
ms.openlocfilehash: e0d81454e6036d09bb74af6f522063a1aed5fffe
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046404"
---
# <a name="quickstart-ingest-data-from-logstash-to-azure-data-explorer"></a>快速入门：将数据从 Logstash 引入 Azure 数据资源管理器

[Logstash](https://www.elastic.co/products/logstash) 是一个开源服务器端数据处理管道，可以同时从多个源引入数据、转换数据，然后将数据发送到你偏好的“储存”中。 在本快速入门中，你要将该数据发送到 Azure 数据资源管理器 - 用于存储日志和遥测数据的高速且高度可缩放的数据探索服务。 首先你将在测试群集中创建一个表和数据映射，然后指示 Logstash 将数据发送到该表，并验证结果。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，请在开始之前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* Azure 数据资源管理器[测试群集和数据库](create-cluster-database-portal.md)
* Logstash 版本 6+ [安装说明](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)

## <a name="create-a-table"></a>创建表

准备好群集和数据库之后，可以创建一个表。

1. 在数据库查询窗口中运行以下命令，以创建表：

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. 运行以下命令，确认已创建新表 `logs` 并且该表是空的：
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>创建映射

Azure 数据资源管理器使用映射将传入的数据转换为目标表架构。 以下命令创建名为 `basicmsg` 的新映射。该映射根据 `path` 的指定从传入的 json 中提取属性，然后将这些属性输出到 `column`。

在查询窗口中运行以下命令：

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>安装 Logstash 输出插件

Logstash 输出插件与 Azure 数据资源管理器通信，并将数据发送到服务。
在 Logstash 根目录中运行以下命令，以安装该插件：

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>配置 Logstash 以生成示例数据集

Logstash 可以生成用于测试端到端管道的示例事件。
如果你正在使用 Logstash 并且有权访问自己的事件流，请跳到下一部分。 

> [!NOTE]
> 如果使用自己的数据，请更改上一步骤中定义的表和映射对象。

1. 编辑包含所需管道设置的新文本文件（使用 vi）：

    ```sh
    vi test.conf
    ```

1. 粘贴以下设置，告知 Logstash 生成 1000 个测试事件：

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

此配置还包括 `stdin` 输入插件。使用该插件可以自行编写更多的消息（请务必使用 *Enter* 将消息提交到管道中）。

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>配置 Logstash 以将数据发送到 Azure 数据资源管理器

将以下设置粘贴到在上一步骤中使用的同一配置文件中。 请将所有占位符替换为相关的设置值。 有关详细信息，请参阅[创建 AAD 应用程序](/azure/kusto/management/access-control/how-to-provision-aad-app)。 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| 参数名称 | 说明 |
| --- | --- |
| **路径** | Logstash 插件会将事件写入临时文件，然后将其发送到 Azure 数据资源管理器。 此参数包含要将文件写入到的路径，以及一个用于轮转文件的时间表达式，该表达式可触发上传到 Azure 数据资源管理器服务的操作。|
| **ingest_url** | 用于进行引入相关通信的 Kusto 终结点。|
| **app_id**、**app_key** 和 **app_tenant**| 连接到 Azure 数据资源管理器所需的凭据。 请务必使用具有引入特权的应用程序。 |
| **database**| 要将事件放入到的数据库的名称。 |
| **表** | 要将事件放入到的目标表的名称。 |
| **mapping** | mapping（映射）用于将传入事件的 json 字符串映射为正确的行格式（定义哪些属性要进入哪个列）。 |

## <a name="run-logstash"></a>运行 Logstash

现在，可以运行 Logstash 并测试设置。

1. 在 Logstash 根目录中运行以下命令：

    ```sh
    bin/logstash -f test.conf
    ```

    屏幕上应会输出信息，然后输出示例配置生成的 1000 条消息。 此时还可以手动输入更多消息。

1. 几分钟后，请运行以下数据资源管理器查询来查看所定义的表中的消息：

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. 按 Ctrl+C 退出 Logstash

## <a name="clean-up-resources"></a>清理资源

在数据库中运行以下命令以清理 `logs` 表：

```Kusto
.drop table logs
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [编写查询](write-queries.md)