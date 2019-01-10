---
title: 使用 Azure Application Insights 进行 OpenCensus Go 跟踪 | Microsoft Docs
description: 提供有关将 OpenCensus Go 跟踪与本地转发器和 Application Insights 集成的说明
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 935119f2efe40ee22d2d11005fa79b12c712b7c2
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104556"
---
# <a name="collect-distributed-traces-from-go-preview"></a>从 Go（预览版）收集分布式跟踪

Application Insights 现在支持通过与 [OpenCensus](https://opencensus.io) 和我们新的[本地转发器](./opencensus-local-forwarder.md)集成来对 Go 应用程序进行分布式跟踪。 本文将逐步介绍设置 OpenCensus for Go 并将跟踪数据提供给 Application Insights 的过程。

## <a name="prerequisites"></a>先决条件

- 需要一个 Azure 订阅。
- 应该安装 Go，本文使用版本 1.11 [去下载](https://golang.org/dl/)。
- 按照说明[将本地转发器安装为 Windows 服务](./opencensus-local-forwarder.md#windows-service)。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-application-insights-resource"></a>创建 Application Insights 资源

首先，你需要创建一个 Application Insights 资源，该资源将生成一个检测密钥 (ikey)。 然后使用 ikey 配置本地转发器，以将 OpenCensus 检测应用程序中的分布式跟踪发送到 Application Insights。   

1. 选择“创建资源” > “开发人员工具” > “Application Insights”。

   ![添加 Application Insights 资源](./media/opencensus-Go/0001-create-resource.png)

   此时会显示配置对话框，请使用下表填写输入字段。

    | 设置        | 值           | 说明  |
   | ------------- |:-------------|:-----|
   | **名称**      | 全局唯一值 | 标识所监视的应用的名称 |
   | **应用程序类型** | 常规 | 所监视的应用的类型 |
   | **资源组**     | myResourceGroup      | 用于托管 App Insights 数据的新资源组的名称 |
   | **位置** | 美国东部 | 选择离你近的位置或离托管应用的位置近的位置 |

2. 单击“创建”。

## <a name="configure-local-forwarder"></a>配置本地转发器

1. 选择“概述” > “概要”> 复制应用程序的**检测密钥**。

   ![检测密钥的屏幕截图](./media/opencensus-Go/0003-instrumentation-key.png)

2. 编辑 `LocalForwarder.config` 文件并添加检测密钥。 如果已按照[先决条件](./opencensus-local-forwarder.md#windows-service)中的说明操作，则该文件位于 `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. 重启应用程序**本地转发器**服务。

## <a name="opencensus-go-packages"></a>OpenCensus Go 包

1. 从命令行安装用于 Go 的 Open Census 包：

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. 将以下代码添加到 .go 文件，然后生成并运行。 （此示例源自官方 OpenCensus 指南，其中添加了有助于与本地转发器集成的代码）

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. 运行 Simple Go 应用后，导航到 `http://localhost:50030`。 每次刷新浏览器都将生成文本“hello world”，并附带由本地转发器拾取的相应 span 数据。

4. 若要确认**本地转发器**是否正在拾取跟踪，请检查 `LocalForwarder.config` 文件。 如果已按照[先决条件](https://docs.microsoft.com/azure/application-insights/local-forwarder#windows-service)中的步骤执行了操作，它将位于 `C:\LF-WindowsServiceHost` 中。

    在下面的日志文件图像中，可以看到在运行第二个脚本（已在其中添加了导出程序）之前，`OpenCensus input BatchesReceived` 为 0。 开始运行更新的脚本以后，`BatchesReceived` 根据我们输入的值的数目递增：
    
    ![“新建 App Insights 资源”窗体](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>开始在 Azure 门户中监视

1. 现在可以在 Azure 门户中重新打开 Application Insights“概览”页，查看当前正在运行的应用程序的详细信息。 选择“实时指标流”。

   ![概览窗格的屏幕截图，其中的实时指标流在红框中呈选中状态。](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. 如果再次运行第二个 Go 应用并针对 `http://localhost:50030` 开始刷新浏览器，则当实时跟踪数据从本地转发器服务到达 Application Insights 时，你会看到它们。

   ![实时指标流的屏幕截图，其中显示了性能数据](./media/opencensus-go/0006-stream.png)

3. 导航回“概览”页，选择“应用程序映射”以获取应用程序组件之间依赖关系和调用时间的可视布局。

    ![基本应用程序映射的屏幕截图](./media/opencensus-go/0007-application-map.png)

    由于我们只跟踪一个方法调用，因此应用程序映射的信息不多。 但是，应用程序映射可以通过缩放将多得多的分布式应用程序可视化：

   ![应用程序地图](media/opencensus-go/application-map.png)

4. 选择“调查性能”，执行详细的性能分析并确定性能减慢的根本原因。

    ![性能窗格的屏幕截图](./media/opencensus-go/0008-performance.png)

5. 选择“示例”，然后单击显示在右窗格中的任意示例，这将启动端到端事务详细信息体验。 虽然我们的示例应用只会显示单个事件，但更复杂的应用程序会让你在探索端到端事务时，可以深入到单个事件的调用堆栈级别。

     ![端到端事务界面的屏幕截图](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>适用于 Go 的 OpenCensus 跟踪

我们只简单介绍了如何将适用于 Go 的 OpenCensus 与本地转发器和 Application Insights 集成。 [官方 OpenCensus Go 使用指南](https://godoc.org/go.opencensus.io)介绍更高级的主题。

## <a name="next-steps"></a>后续步骤

* [应用程序映射](./../../azure-monitor/app/app-map.md)
* [端到端性能监视](./../../azure-monitor/learn/tutorial-performance.md)
