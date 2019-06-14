---
title: 将浮出控件添加到远程监视解决方案 UI - Azure | Microsoft Docs
description: 本文介绍如何在远程监视解决方案加速器 Web UI 中的页面上添加新的浮出控件。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: ccb1a7ff6abbc68f42c7632a8ba7a392b2c48794
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447108"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>向远程监视解决方案加速器 Web UI 添加自定义浮出控件

本文介绍如何在远程监视解决方案加速器 Web UI 中的页面上添加新的浮出控件。 本文介绍：

- 如何准备本地开发环境。
- 如何向 Web UI 中的页面添加新浮出控件。

本文中的示例浮出控件显示在具有网格的页面上，若要了解如何添加网格，请参阅[向远程监视解决方案加速器 Web UI 添加自定义网格](iot-accelerators-remote-monitoring-customize-grid.md)操作指南。

## <a name="prerequisites"></a>必备组件

要完成本操作指南中的步骤，需要在本地开发计算机上安装以下软件：

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>开始之前

应先完成下列文章中的步骤，再继续操作：

- [向远程监视解决方案加速器 Web UI 添加自定义页面](iot-accelerators-remote-monitoring-customize-page.md)。
- [向远程监视解决方案加速器 Web UI 添加自定义服务](iot-accelerators-remote-monitoring-customize-service.md)
- [向远程监视解决方案加速器 Web UI 添加自定义网格](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>添加浮出控件

要向 Web UI 添加浮出控件，需要添加定义浮出控件的源文件，然后修改部分现有文件，从而让 Web UI 识别新组件。

### <a name="add-the-new-files-that-define-the-flyout"></a>添加定义浮出控件的新文件

首先，src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout 文件夹包含定义浮出控件的文件  ：

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

将 src/walkthrough/components/pages/pageWithFlyout/flyouts 文件夹复制到 src/components/pages/example 文件夹   。

### <a name="add-the-flyout-to-the-page"></a>将浮出控件添加到页面

修改 src/components/pages/example/basicPage.js 以添加浮出控件  。

将 Btn 添加到 components/shared 的导入中，并为 svgs 和 ExampleFlyoutContainer 添加导入     ：

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

为 closedFlyoutState 添加 const 定义并将其添加到构造函数的状态中   ：

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

将以下函数添加到 BasicPage 类  ：

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

将以下 const 定义添加至 render 函数   ：

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

在上下文菜单中添加用于打开浮出控件的按钮：

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

将一些文本和浮出控件容器添加到页面内容：

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>测试浮出控件

如果 Web UI 尚未在本地运行，请在存储库的本地副本的根目录中运行以下命令：

```cmd/sh
npm start
```

上述命令在 [http://localhost:3000/dashboard](http://localhost:3000/dashboard) 以本地方式运行 UI。 导航到“示例”页，单击“打开浮出控件”   。

## <a name="next-steps"></a>后续步骤

本文介绍了可以帮助你在远程监视解决方案加速器 Web UI 中添加或自定义页面的资源。

现已在页面上定义了浮出控件，下一步是[向远程监视解决方案加速器 Web UI 中的仪表板添加面板](iot-accelerators-remote-monitoring-customize-panel.md)。

有关远程监视解决方案加速器的其他概念性信息，请参阅[远程监视体系结构](iot-accelerators-remote-monitoring-sample-walkthrough.md)。
