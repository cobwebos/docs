---
title: "部署 Azure Log Analytics Nozzle 以监视 Cloud Foundry | Microsoft文档"
description: "逐步介绍了如何部署 Cloud Foundry Loggregator 的 Azure Log Analytics Nozzle、如何配置 Azure Log Analytics OMS 控制台，以及如何使用这些工具监视 Cloud Foundry 系统运行状况和性能指标。"
services: virtual-machines-linux
documentationcenter: 
author: ningk
manager: timlt
editor: 
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f704a2638a4b6b57c014d502dd87303a55334672
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>部署 Azure Log Analytics Nozzle 以监视 Cloud Foundry 系统

## <a name="background"></a>背景

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) 是 Microsoft [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/) (OMS) 中的服务。 它有助于用户收集并分析云和本地环境生成的数据。

Microsoft Azure Log Analytics Nozzle（以下简称 Nozzle）是 Cloud Foundry 组件，负责将指标从 [Cloud Foundry Loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) Firehose 转发到 Azure Log Analytics。 使用 Nozzle，可以跨多个部署收集、查看和分析 Cloud Foundry 系统运行状况和性能指标。

在本文档中，将了解如何将 Nozzle 部署到 Cloud Foundry 环境中，以及如何在 Azure Log Analytics OMS 控制台中查看数据。

## <a name="prerequisites"></a>先决条件

### <a name="1-deploy-a-cf-or-pcf-environment-in-azure"></a>1.在 Azure 中部署 CF 或 PCF 环境

可以部署的开放源代码 Cloud Foundry (CF) 或 Pivotal Cloud Foundry (PCF) 环境中部署 Nozzle。

* [在 Azure 上部署 Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [在 Azure 上部署 Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2.安装用于部署 Nozzle 的 CF 命令行工具

由于 Nozzle 作为 CF 环境中的应用程序运行，因此需要使用 CF CLI 部署此应用程序。 它还需要有权访问 Loggregator Firehose 和 Cloud Controller，因此需要使用 UAA 命令行客户端创建并配置用户。

* [安装 Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [安装 Cloud Foundry UAA 命令行客户端](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

安装 UAA 命令行客户端前，请务必先安装 Rubygems。

### <a name="3-create-an-oms-workspace-in-azure"></a>3.在 Azure 中创建 OMS 工作区

#### <a name="create-the-oms-workspace-manually"></a>手动创建 OMS 工作区

可以手动创建 OMS 工作区，并在完成 Nozzle 部署后加载预配置的 OMS 视图和警报。

1. 在 Azure 门户中，从 Marketplace 服务列表中搜索“Log Analytics”，再选择“Log Analytics”。
2. 单击“创建”，并设置以下选项：

* OMS 工作区：键入工作区的名称。
* 订阅：如果有多个订阅，请选择与 CF 部署相同的订阅。
* 资源组：可以新建资源组，也可以使用与 CF 部署相同的资源组。
* 位置
* 定价层：单击“确定”即可完成设置。
> 有关详细信息，请参阅 [Log Analytics 入门](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)

#### <a name="create-the-oms-workspace-through-the-oms-template"></a>通过 OMS 模板创建 OMS 工作区

可以创建 OMS 工作区，并加载预配置的 OMS 视图和警报，这全都是通过[适用于 Cloud Foundry 的 Azure OMS Log Analytics 解决方案](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution)完成的

## <a name="deploy-the-nozzle"></a>部署 Nozzle

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>将 Nozzle 部署为 PCF Ops Manager 磁贴

如果已通过 Ops Manager 部署了 PCF，请按照以下步骤操作，[安装并配置适用于 PCF 的 Nozzle](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html)，Nozzle 将安装为 Ops Manager 磁贴。

### <a name="deploy-the-nozzle-as-an-application-to-cloud-foundry"></a>将 Nozzle 作为应用程序部署到 Cloud Foundry

如果使用的不是 PCF Ops Manager，需要将 Nozzle 作为应用程序推送。

#### <a name="log-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>通过 CF CLI 以管理员身份登录 CF 部署

在开发环境中，运行以下命令：
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

> “SYSTEM_DOMAIN”是 CF 域名。 检索方法为，在 CF 部署清单文件中搜索“SYSTEM_DOMAIN”。 
> “CF_User”是 CF 管理员名称。 可以检索名称和密码，具体方法是在 CF 部署清单文件的“scim”部分中搜索名称和“cf_admin_password”。

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>创建 CF 用户并授予所需的权限

在开发环境中，运行以下命令：
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

> “SYSTEM_DOMAIN”是 CF 域名。 检索方法为，在 CF 部署清单文件中搜索“SYSTEM_DOMAIN”。

#### <a name="download-the-latest-azure-log-analytics-nozzle-release"></a>下载最新版 Azure Log Analytics Nozzle

在开发环境中，运行以下命令：
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables-in-manifestyml-in-your-current-directory"></a>在当前目录中的“manifest.yml”内设置环境变量

这是 Nozzle 的应用程序清单，需要将值替换为特定的 OMS 工作区信息。

```
OMS_WORKSPACE             : OMS workspace ID: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_KEY                   : OMS key: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to OMS Log Analytics, default is 10s.
OMS_BATCH_TIME            : Interval for posting a batch to OMS Log Analytics, default is 10s.
OMS_MAX_MSG_NUM_PER_BATCH : The max number of messages in a batch to OMS Log Analytics, default is 1000.
API_ADDR                  : The api URL of the CF environment, refer to "Push the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
DOPPLER_ADDR              : Loggregator's traffic controller URL, refer to "Deploy the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
FIREHOSE_USER             : CF user you created in "Push the Nozzle as an App to Cloud Foundry" section, who has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma separated list, valid event types are METRIC,LOG,HTTP
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the Trafficcontroller
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments
IDLE_TIMEOUT              : Keep Alive duration for the firehose consumer, default is 60s.
LOG_LEVEL                 : Logging level of the nozzle, valid levels: DEBUG, INFO, ERROR
LOG_EVENT_COUNT           : If true, the total count of events that the nozzle has received and sent will be logged to OMS Log Analytics as CounterEvents
LOG_EVENT_COUNT_INTERVAL  : The time interval of logging event count to OMS Log Analytics, default is 60s.
```

### <a name="push-the-application-from-your-dev-box"></a>从开发环境推送应用程序

请务必在文件夹“oms-log-analytics-firehose-nozzle”下运行以下命令：
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>验证 Nozzle 安装

### <a name="from-apps-manager-for-pcf"></a>通过应用程序管理器（对于 PCF）

1. 登录 Ops Manager，并确保磁贴显示在安装仪表板上。
2. 登录应用程序管理器，并确保为 Nozzle 创建的空间已在使用情况报告上列出，且状态正常。

### <a name="from-dev-box"></a>通过开发环境

在开发环境 CF CLI 窗口中，键入以下命令：
```
cf apps
```
确保 OMS Nozzle 应用程序正在运行。

## <a name="view-the-data-in-oms-portal"></a>在 OMS 门户中查看数据

### <a name="1-import-oms-view"></a>1.导入 OMS 视图

在 OMS 门户中，依次转到“视图设计器” -> “导入” -> “浏览”，选择 omsview 文件之一（如 Cloud Foundry.omsview），再保存视图。 此时，“磁贴”显示在 OMS 主页“概述”上。 单击“磁贴”，便会看到直观呈现的指标。

操作员可以通过“视图设计器”自定义这些视图或新建视图。

“Cloud Foundry.omsview”是预览版的 Cloud Foundry OMS 视图模板，完全配置的默认模板正在开发中。如有建议和反馈，请发送到[问题部分](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)。

### <a name="2-create-alert-rules"></a>2.创建警报规则

操作员可以[创建警报](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)，并视需要自定义查询和阈值。 下面展示了一组建议的警报。

| 搜索查询                                                                  | 基于以下项生成警报 | 说明                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | 结果数 < 1   | bbs.Domain.cf-apps 指明 cf-apps 域是否为最新。也就是说，来自 Cloud Controller 的 CF 应用程序请求会同步到 bbs.LRPsDesired（Diego 需要的 AI），以供执行。 没有收到数据则表示在给定时间范围内，cf-apps 域不是最新的。 |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | 结果数 > 0   | 对于 Diego 单元，0 表示正常，1 表示不正常。 设置在给定时间范围内检测到多个不正常的 Diego 单元时发出的警报。 |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | 结果数 > 0 | 1 表示系统正常，0 表示系统不正常。 |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | 结果数 > 0   | Consul 定期发出自己的运行状况状态。 0 表示系统正常，1 表示路由发射器检测到 Consul 停止运行。 |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | 结果数 > 0 | 由于反压力，Doppler 特意降低了消息的增量数。 |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | 结果数 > 0   | Loggregator 发出 LGR 以指明日志记录进程存在问题（例如，日志消息输出过高）。 |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | 结果数 > 0   | 当 Nozzle 从 Loggregator 收到慢使用者警报时，它会向 OMS 发送 slowConsumerAlert ValueMetric。 |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | 结果数 > 0   | 如果“丢失事件”增量数达到阈值，表示 Nozzle 存在问题，无法正常运行。 |

## <a name="scale"></a>缩放

### <a name="scale-the-nozzle"></a>缩放 Nozzle

建议操作员开始至少使用两个 Nozzle 实例。 Firehose 跨所有 Nozzle 实例分布工作负载。
为确保 Nozzle 能够随时掌握 Firehose 的数据流量，操作员应设置“创建警报规则”部分中列出的 slowConsumerAlert 警报；看到警报后，请参阅[慢 Nozzle 警报指南](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz)，确定是否需要进行缩放。
若要纵向扩展 Nozzle，请使用应用程序管理器或 CF CLI，增加 Nozzle 的实例数量或内存/磁盘资源。

### <a name="scale-the-loggregator"></a>缩放 Loggregator

Loggregator 发送 LGR 日志消息，以指明日志记录进程存在的问题。 操作员可以监视警报，以确定是否需要纵向扩展 Loggregator。
若要纵向扩展 Loggregator，操作员可以增加 Doppler 缓冲区大小，也可以在 CF 清单中添加其他 Doppler 服务器实例。有关详细信息，请参阅 [Loggregator 缩放指南](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling)。

## <a name="update"></a>更新

若要将 Nozzle 更新为更高版本，请下载新版 Nozzle，按照“部署”部分中的步骤操作，重新推送应用程序。

若要删除 Nozzle，请按照以下步骤操作：

### <a name="from-the-ops-manager"></a>通过 Ops Manager

1. 登录 Ops Manager
2. 找到“适用于 PCF 的 Microsoft Azure Log Analytics Nozzle”磁贴
3. 单击垃圾桶图标，并确认删除操作。

### <a name="from-the-dev-box"></a>通过开发环境

在 CF CLI 窗口中，键入以下命令：
```
cf delete <App Name> -r
```

如果删除 Nozzle，不会自动删除 OMS 门户中的数据，这些数据会根据 OMS Log Analytics 保留期设置自动过期。

## <a name="support-and-feedback"></a>支持和反馈

Azure Log Analytics Nozzle 开放源代码。若有问题和反馈，请发送到 [github 部分](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)。 若要开启 Azure 支持请求，请使用“运行 Cloud Foundry 的虚拟机”作为服务类别。 

## <a name="next-step"></a>后续步骤

除了 Nozzle 涵盖的 Cloud Foundry 指标之外，还可以利用 OMS 代理获取有关 VM 级操作数据（Syslog、性能、警报、清单）的见解。此代理可作为 Bosh Addon 安装到 CF VM 中。
> 有关详细信息，请参阅[将 OMS 代理部署到 Cloud Foundry 部署](https://github.com/Azure/oms-agent-for-linux-boshrelease)。
