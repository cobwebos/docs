---
title: 向远程监视解决方案 UI 添加网格 - Azure | Microsoft Docs
description: 本文介绍如何在远程监视解决方案加速器 Web UI 中的页面上添加新网格。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.openlocfilehash: e1c694847a1ec16d4d7a7b1118df71cb06396186
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165924"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>向远程监视解决方案加速器 Web UI 添加自定义网格

本文介绍如何在远程监视解决方案加速器 Web UI 中的页面添加新网格。 本文介绍：

- 如何准备本地开发环境。
- 如何向 Web UI 中的页面添加新网格。

本文中的示例网格显示来自服务的数据，在[向远程监视解决方案加速器 Web UI 添加自定义服务](iot-accelerators-remote-monitoring-customize-service.md)操作指南文章中介绍了服务的添加方法。

## <a name="prerequisites"></a>先决条件

要完成本操作指南中的步骤，需要在本地开发计算机上安装以下软件：

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>开始之前

应先完成下列文章中的步骤，再继续操作：

- [向远程监视解决方案加速器 Web UI 添加自定义页面](iot-accelerators-remote-monitoring-customize-page.md)。
- [向远程监视解决方案加速器 Web UI 添加自定义服务](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>添加网格

要向 Web UI 添加网格，需要添加定义网格的源文件，然后修改部分现有文件，以使 Web UI 感知新组件。

### <a name="add-the-new-files-that-define-the-grid"></a>添加定义网格的新文件

首先，src/walkthrough/components/pages/pageWithGrid/exampleGrid 文件夹包含定义网格的文件：

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

将 src/walkthrough/components/pages/pageWithGrid/exampleGrid 文件夹复制到 src/components/pages/example 文件夹。

### <a name="add-the-grid-to-the-page"></a>向页面添加网格

按如下方式修改 src/components/pages/example/basicPage.container.js，以导入服务定义：

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

按如下方式修改 src/components/pages/example/basicPage.js，以添加网格：

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

按如下方式修改 src/components/pages/example/basicPage.test.js，以更新测试：

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>测试网格

如果 Web UI 尚未在本地运行，请在存储库的本地副本的根目录中运行以下命令：

```cmd/sh
npm start
```

上述命令在 [http://localhost:3000/dashboard](http://localhost:3000/dashboard) 以本地方式运行 UI。 导航到“示例”页，查看显示服务中的数据的网格。

## <a name="select-rows"></a>选择行

可通过两个选择，使用户选择网格中的行：

### <a name="hard-select-rows"></a>硬选择行

如果用户需要同时处理多行，对行使用复选框：

1. 通过将 checkboxColumn 添加到向网格提供的 columnDefs，启用行的硬选择。 checkboxColumn 在 /src/components/shared/pcsGrid/pcsGrid.js 中定义：

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. 要访问所选项，请获得对内部网格 API 的引用：

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. 硬选择网格中的行时，请提供页面的上下文按钮：

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. 单击上下文按钮时，获取要在其上完成工作的硬选择项：

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>软选择行

如果用户只需对单行操作，在 columnDefs 中为一列或多列配置软选择链接。

1. 在 exampleGridConfig.js 中，添加 SoftSelectLinkRenderer 作为 columnDef 的 cellRendererFramework。

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. 单击软选择链接后，触发 onSoftSelectChange 事件。 对该行执行所需的任何操作，例如打开详细信息浮出控件。 此示例仅写入控制台：

    ```js
    onSoftSelectChange = (rowId, rowEvent) => {
      const { onSoftSelectChange } = this.props;
      const obj = (this.gridApi.getDisplayedRowAtIndex(rowId) || {}).data;
      if (obj) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', obj);
        this.setState({ softSelectedObj: obj });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(obj, rowEvent);
      }
    }
    ```

## <a name="next-steps"></a>后续步骤

本文介绍了可以帮助你在远程监视解决方案加速器 Web UI 中添加或自定义页面的资源。

现在已定义了网格，下一步是[向远程监视解决方案加速器 Web UI 添加自定义浮出控件](iot-accelerators-remote-monitoring-customize-flyout.md)，该浮出控件显示在示例页面上。

有关远程监视解决方案加速器的其他概念性信息，请参阅[远程监视体系结构](iot-accelerators-remote-monitoring-sample-walkthrough.md)。
