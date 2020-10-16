---
title: 访问内置指标-Azure IoT Edge
description: 远程访问 IoT Edge 运行时组件中的内置指标
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 10/05/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b6f4e50cac2f809172c2525ea9136a63e6bd9066
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107005"
---
# <a name="access-built-in-metrics"></a>访问内置指标

IoT Edge 运行时组件（IoT Edge 中心和 IoT Edge 代理）以 [Prometheus 处于阐释格式](https://prometheus.io/docs/instrumenting/exposition_formats/)生成内置的指标。 远程访问这些指标来监视和了解 IoT Edge 设备的运行状况。

从发布1.0.10，默认情况下，在**edgeHub**和**edgeAgent**模块的**端口 9600**上自动公开指标 (`http://edgeHub:9600/metrics` 和 `http://edgeAgent:9600/metrics`) 。 默认情况下，它们不会映射到主机。

通过从模块的中公开和映射指标端口来访问主机中的指标 `createOptions` 。 下面的示例将默认指标端口映射到主机上的端口9601：

```
{
  "ExposedPorts": {
    "9600/tcp": {},
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

如果要映射 edgeHub 和 edgeAgent 的指标终结点，请选择 "其他唯一主机端口号"。

> [!NOTE]
> 如果希望禁用指标，请将环境变量设置为，以将 `MetricsEnabled` `false` **edgeAgent**。

## <a name="available-metrics"></a>可用度量值

指标包含有助于识别所收集指标的性质的标记。 所有指标都包含以下标记：

| 标记 | 说明 |
|-|-|
| iothub | 设备与之通信的集线器 |
| edge_device | 当前设备的 ID |
| instance_number | 表示当前运行时的 GUID。 重新启动时，将重置所有指标。 此 GUID 便于协调重启。 |

在 Prometheus 处于阐释格式中，有四个核心指标类型： counter、仪表、直方图和 summary。 有关不同指标类型的详细信息，请参阅 [Prometheus 公制 types 文档](https://prometheus.io/docs/concepts/metric_types/)。

为内置直方图和摘要指标提供的分位数为0.1、0.5、0.9 和0.99。

**EdgeHub**模块生成以下度量值：

| 名称 | 维度 | 说明 |
|-|-|-|
| `edgehub_gettwin_total` | `source` (操作源) <br> `id` (模块 ID)  | 类型：计数器<br> GetTwin 调用总数 |
| `edgehub_messages_received_total` | `route_output` 发送消息 (输出) <br> `id` | 类型：计数器<br> 从客户端接收的消息总数 |
| `edgehub_messages_sent_total` | `from` (消息源) <br> `to` (消息目标) <br>`from_route_output`<br> `to_route_input` (消息目标输入) <br> `priority` (目标) 的消息优先级 | 类型：计数器<br> 发送到客户端或上游的消息总数<br> `to_route_input`当 $upstream 时，为空 `to` |
| `edgehub_reported_properties_total` | `target` (更新目标) <br> `id` | 类型：计数器<br> 报告的属性更新调用总数 |
| `edgehub_message_size_bytes` | `id`<br> | 类型：摘要<br> 来自客户端的消息大小<br> `NaN`如果在一段时间内未报告任何新测量 (当前10分钟) ，则可能报告值为 `summary` ，将发出相应的 `_count` 和 `_sum` 计数器。 |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | 类型：摘要<br> 获取用于完成操作所花费的时间 |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | 类型：摘要<br> 发送消息所用的时间 |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | 类型：摘要<br> 处理来自队列的消息所用的时间 |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | 类型：摘要<br> 更新报告属性所用的时间 |
| `edgehub_direct_method_duration_seconds` | `from` (调用方) <br> `to` (接收方)  | 类型：摘要<br> 解决直接消息所用的时间 |
| `edgehub_direct_methods_total` | `from`<br> `to` | 类型：计数器<br> 发送的直接消息总数 |
| `edgehub_queue_length` | `endpoint` (消息源) <br> `priority` (队列优先级)  | 类型：仪表<br> 给定优先级的 edgeHub 队列的当前长度 |
| `edgehub_messages_dropped_total` | `reason` (no_route，ttl_expiry) <br> `from` <br> `from_route_output` | 类型：计数器<br> 由于原因而删除的消息总数 |
| `edgehub_messages_unack_total` | `reason` (storage_failure) <br> `from`<br> `from_route_output` | 类型：计数器<br> 由于存储故障未确认的消息总数 |
| `edgehub_offline_count_total` | `id` | 类型：计数器<br> EdgeHub 脱机的总次数 |
| `edgehub_offline_duration_seconds`| `id` | 类型：摘要<br> 时间边缘中心脱机 |
| `edgehub_operation_retry_total` | `id`<br> `operation` (操作名称)  | 类型：计数器<br> 重试 edgeHub 操作的总次数 |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (未经过身份验证) <br> | 类型：计数器<br> 客户端连接到 edgeHub 失败的总次数 |

**EdgeAgent**模块生成以下度量值：

| 名称 | 维度 | 说明 |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | 类型：仪表<br> 在部署中指定模块并处于运行状态的时间量 |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | 类型：仪表<br> 在部署中指定模块的时间量 |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | 类型：计数器<br> EdgeAgent 请求 docker 启动模块的次数 |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | 类型：计数器<br> EdgeAgent 请求 docker 停止模块的次数 |
| `edgeAgent_command_latency_seconds` | `command` | 类型：仪表<br> Docker 执行给定命令所花费的时间。 可能的命令有：创建、更新、删除、启动、停止、重新启动 |
| `edgeAgent_iothub_syncs_total` | | 类型：计数器<br> EdgeAgent 尝试与 iotHub 同步其副本的次数（成功和不成功）。 此数目包括两个代理，请求对克隆更新进行克隆和中心通知 |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | 类型：计数器<br> EdgeAgent 无法与 iotHub 同步其副本的次数。 |
| `edgeAgent_deployment_time_seconds` | | 类型：计数器<br> 接收到更改后完成新部署所花的时间。 |
| `edgeagent_direct_method_invocations_count` | `method_name` | 类型：计数器<br> 调用内置 edgeAgent 直接方法的次数，如 Ping 或 Restart。 |
| `edgeAgent_host_uptime_seconds` | | 类型：仪表<br> 主机的开启时间 |
| `edgeAgent_iotedged_uptime_seconds` | | 类型：仪表<br> Iotedged 已运行的时间 |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | 类型：仪表<br> 磁盘上的剩余空间量 |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| 类型：仪表<br> 磁盘大小 |
| `edgeAgent_used_memory_bytes` | `module_name` | 类型：仪表<br> 所有进程使用的 RAM 量 |
| `edgeAgent_total_memory_bytes` | `module_name` | 类型：仪表<br> 可用 RAM |
| `edgeAgent_used_cpu_percent` | `module_name` | 类型：直方图<br> 所有进程使用的 cpu 百分比 |
| `edgeAgent_created_pids_total` | `module_name` | 类型：仪表<br> 容器创建的进程或线程数 |
| `edgeAgent_total_network_in_bytes` | `module_name` | 类型：仪表<br> 从网络接收的字节数 |
| `edgeAgent_total_network_out_bytes` | `module_name` | 类型：仪表<br> 向网络发送的字节数 |
| `edgeAgent_total_disk_read_bytes` | `module_name` | 类型：仪表<br> 从磁盘读取的字节数 |
| `edgeAgent_total_disk_write_bytes` | `module_name` | 类型：仪表<br> 写入磁盘的字节数 |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | 类型：仪表<br> 有关设备的常规元数据。 此值始终为0，将在标记中对信息进行编码。 请注意 `experimental_features` 和 `host_information` 是 json 对象。 `host_information` 如下所示 ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` 。 请注意， `ServerVersion` Docker 版本是 `Version` IoT Edge 安全守护程序版本。 |

## <a name="next-steps"></a>后续步骤

* [了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)
* [IoT Edge 代理和 IoT Edge 中心模块孪生的属性](module-edgeagent-edgehub.md)
