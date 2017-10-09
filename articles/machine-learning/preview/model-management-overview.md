---
title: "Azure 机器学习模型管理的概念性概述 | Microsoft Docs"
description: "本文档解释 Azure 机器学习的模型管理概念。"
services: machine-learning
author: nk773
ms.author: neerajkh, padou
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 31b859d86e82c92839462280721c5f84f1d923cd
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="azure-machine-learning-model-management"></a>Azure 机器学习模型管理

使用 Azure 机器学习模型管理可以管理和部署机器学习工作流与模型。 

模型管理提供以下方面的功能：
- 模型版本控制
- 在生产环境中跟踪模型
- 配合 [Azure 容器服务](https://azure.microsoft.com/services/container-service/)和 [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough) 通过 AzureML 计算环境将模型部署到生产环境
- 创建包含模型的 Docker 容器并在本地对其进行测试
- 自动重新训练模型
- 捕获模型遥测数据，获得可行的见解。 

Azure 机器学习模型管理提供模型版本的注册表。 它还提供自动化工作流用于以 REST API 形式打包和部署机器学习容器。 使用预测 API 在基于 Linux 的 Docker 容器中打包模型及其运行时依赖项。 

Azure 机器学习计算环境可帮助设置和管理用于托管模型的可缩放群集。 计算环境基于 Azure 容器服务。 Azure 容器服务可自动将机器学习 API 公开为 REST API 终结点，并提供以下功能：

- 身份验证
- 负载均衡
- 自动横向扩展
- 加密

Azure 机器学习模型管理通过 CLI、API 和 Azure 门户提供这些功能。 

Azure 机器学习模型管理使用以下信息：

 - 模型文件或包含模型文件的目录
 - 用户创建的 Python 文件，可实现模型评分函数
 - 列出运行时依赖项的 Conda 依赖性文件
 - 运行时环境选项，以及 
 - API 参数的架构文件 

执行以下操作时，会使用上述信息：

- 注册模型
- 创建生成容器时使用的清单
- 生成 Docker 容器映像
- 将容器部署到 Azure 容器服务
 
下图显示了在群集中注册和部署模型的概况。 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>创建和管理模型 
可将模型注册到 Azure 机器学习模型管理，以便在生产环境中跟踪模型版本。 为方便重现问题和监管，该服务会捕获所有依赖项和关联的信息。 若要深入洞察性能，可以使用提供的 SDK 捕获模型遥测数据。 模型遥测数据存档在用户提供的存储中。 以后可以使用模型遥测数据来分析模型性能、重新训练和获得业务见解。

## <a name="create-and-manage-manifests"></a>创建和管理清单 
模型需要有附加的项目才能部署到生产环境中。 系统提供了相应的功能用于创建包含模型、依赖项、推理脚本（又称为评分脚本）、样本数据、架构等的清单。此清单充当创建 Docker 容器映像时所用的配方。 企业可以自动生成清单、创建不同的版本，以及管理其清单。 

## <a name="create-and-manage-docker-container-images"></a>创建和管理 Docker 容器映像 
可以使用前一步骤创建的清单在相应的环境中生成基于 Docker 的容器映像。 基于 Docker 的容器化映像可让企业在以下计算环境中灵活运行这些映像：

- [基于 Kubernetes 的 Azure 容器服务](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- 本地容器服务
- 开发环境
- IoT 设备

这些基于 Docker 的容器化映像原生包含生成预测所需的所有必要依赖项。 

## <a name="deploy-docker-container-images"></a>部署 Docker 容器映像 
使用 Azure 机器学习模型管理，只需运行一条命令即可将基于 Docker 的容器映像部署到机器学习计算环境管理的 Azure 容器服务。 这些部署是使用前端服务器创建的，提供以下功能：

- 大规模、低延迟的预测
- 负载均衡
- 机器学习终结点自动缩放
- API 密钥的授权
- API swagger 文档

可通过以下配置设置来控制部署规模和遥测：

- 每个 Web 服务级别的系统日志记录和模型遥测。 所有 stdout 日志会流式传输到 [Azure Application Insights](https://azure.microsoft.com/services/application-insights/)（如果已启用此功能）。 模型遥测数据存档在自己提供的存储中。 
- 自动缩放和并发限制。 这些设置可根据现有群集中的负载自动增加已部署的容器数。 它们还能控制吞吐量和预测延迟一致性。

## <a name="consumption"></a>消耗 
Azure 机器学习模型管理为部署的模型创建 REST API，并创建 swagger 文档。 可以结合 API 密钥和模型输入调用 REST API 来使用已部署的模型，获取业务线应用程序附带的预测。 GitHub 中提供了适用于 Java、[Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py) 和 C# 语言的示例代码来调用 REST API。 通过 Azure 机器学习模型管理 CLI 可以轻松使用这些 REST API。 可以通过单个 CLI 命令、支持 swagger 的应用程序或 curl 使用 API。 

## <a name="retraining"></a>重新训练 
Azure 机器学习模型管理提供可用于重新训练模型的 API。 还可以借助 API 使用模型的更新版本来更新现有部署。 在执行数据科学工作流的过程中，可以在试验环境中重新创建模型。 然后，可将模型注册到模型管理，并更新现有部署。 使用单个 UPDATE CLI 命令执行更新。 UPDATE 命令会更新现有部署，且不更改 API URL 或密钥。 使用模型的应用程序在不更改任何代码的情况下可继续正常工作，并使用新模型获得更好的预测效果。

下图中捕获了描述这些概念的整个工作流：

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ) 
- 支持哪些数据类型？ 是否可以直接传递 NumPy 数组作为 Web 服务的输入？

   如果提供使用 generate_schema SDK 创建的架构文件，则可以传递 NumPy 和/或 Pandas DF。 还可以传递任何 JSON 可序列化输入。 还可以二进制编码字符串的形式传递映像。

- Web 服务是否支持多个输入或分析不同的输入？ 

   是的，可将一个 JSON 请求中打包的多个输入用作字典。 每个输入对应于单个唯一的字典键。

- 向 Web 服务发出的请求所激活的调用是阻塞调用还是异步调用？

   如果服务是使用 CLI 或 API 附带的实时选项创建的，则调用是阻塞/同步调用。 它的实时速度预期较快。 不过，在客户端上，可以使用异步 HTTP 库来调用服务，以免阻塞客户端线程。

- Web 服务可以同时处理多少个请求？

   这取决于群集和 Web 服务的规模。 可将服务横向扩展到副本数的 100 倍，然后，服务便可以并发处理许多的请求。 还可以配置每个副本的最大并发请求数，以提高服务吞吐量。

- Web 服务可将多少个请求排队？

   这是可以配置的。 默认情况下，设一个副本的排队请求数设置为大约 10 个，但可以根据应用程序的要求增大/减小该数字。 通常，增大请求排队数会提高服务吞吐量，但会导致延迟率变得更糟。 为了保持一致的延迟，可将排队数设置为较小的值 (1-5)，并增加副本数来处理吞吐量。 还可以启用自动缩放，使副本数能够根据负载自动调整。 

- 是否可对多个 Web 服务终结点使用同一个计算机或群集？

   完全可以。 可在同一个群集上运行 100 倍个服务/终结点。 

## <a name="next-steps"></a>后续步骤
若要开始使用模型管理，请参阅[配置模型管理](model-management-configuration.md)。

