---
title: 向远程监视解决方案 UI 添加服务 - Azure | Microsoft Docs
description: 本文介绍如何在远程监视解决方案加速器 Web UI 中添加新服务。
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/02/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447040"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>向远程监视解决方案加速器 Web UI 添加自定义服务

本文介绍如何在远程监视解决方案加速器 Web UI 中添加新服务。 本文介绍：

- 如何准备本地开发环境。
- 如何向 Web UI 添加新服务。

本文中的示例服务为网格提供数据，若要了解如何添加网格，请参阅[向远程监视解决方案加速器 Web UI 添加自定义网格](iot-accelerators-remote-monitoring-customize-grid.md)操作指南。

在 React 应用程序中，服务通常与后端服务交互。 远程监视解决方案加速器中的示例包括与 IoT 中心管理器和配置微服务交互的服务。

## <a name="prerequisites"></a>必备组件

要完成本操作指南中的步骤，需要在本地开发计算机上安装以下软件：

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>开始之前

应先完成[向远程监视解决方案加速器 Web UI 添加自定义页面](iot-accelerators-remote-monitoring-customize-page.md)操作指南中的步骤，然后再继续操作。

## <a name="add-a-service"></a>添加服务

若要向 Web UI 添加服务，需要添加定义服务的源文件，然后修改部分现有文件，从而让 Web UI 识别新服务。

### <a name="add-the-new-files-that-define-the-service"></a>添加定义服务的新文件

为了帮助你入门，**src/walkthrough/services** 文件夹包含定义简单服务的文件：

**exampleService.js**



若要详细了解如何实现服务，请参阅 [The introduction to Reactive Programming you've been missing](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)（你一直错过的反应式编程简介）。

**model/exampleModels.js**



将 **exampleService.js** 复制到 **src/services** 文件夹，将 **exampleModels.js** 复制到 **src/services/models** 文件夹。

更新 **src/services** 文件夹中的 **index.js** 文件以导出新服务：

```js
export * from './exampleService';
```

更新 **src/services/models** 文件夹中的 **index.js** 文件以导出新模型：

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>从存储中设置对服务的调用

为了帮助你入门，**src/walkthrough/store/reducers** 文件夹包含一个示例化简器：

**exampleReducer.js**



将 **exampleReducer.js** 复制到 **src/store/reducers** 文件夹。

若要详细了解化简器和 **Epics**，请参阅 [redux-observable](https://redux-observable.js.org/)。

### <a name="configure-the-middleware"></a>配置中间件

若要配置中间件，请将化简器添加到 **src/store** 文件夹中的 **rootReducer.js** 文件：

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

将 epics 添加到 **src/store** 文件夹中的 **rootEpics.js** 文件：

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>后续步骤

本文介绍了可以帮助你在远程监视解决方案加速器 Web UI 中添加或自定义服务的资源。

现在已定义了服务，下一步是[向远程监视解决方案加速器 Web UI 添加自定义网格](iot-accelerators-remote-monitoring-customize-grid.md)，该网格将显示服务返回的数据。

有关远程监视解决方案加速器的其他概念性信息，请参阅[远程监视体系结构](iot-accelerators-remote-monitoring-sample-walkthrough.md)。
