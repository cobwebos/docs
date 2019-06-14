---
title: 向远程监视解决方案 UI 添加页 - Azure | Microsoft Docs
description: 本文介绍如何向远程监视解决方案加速器 Web UI 中添加新页。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 95830cdffb232e16f9fbae51cfa11fbd18172c3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447074"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>向远程监视解决方案加速器 Web UI 添加自定义页面

本文介绍如何向远程监视解决方案加速器 Web UI 中添加新页。 本文介绍：

- 如何准备本地开发环境。
- 如何向 Web UI 添加新页。

其他操作指南扩展此方案以向添加的页添加更多功能。

## <a name="prerequisites"></a>必备组件

要完成本操作指南中的步骤，需要在本地开发计算机上安装以下软件：

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>准备 UI 的本地开发环境

远程监视解决方案加速器 UI 代码是使用 [React](https://reactjs.org/) JavaScript 框架实现的。 可在 [远程监视 WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub 存储库中找到源代码。

若要对 UI 进行更改并测试，可在本地开发计算机上运行它。 （可选）本地副本可以连接到解决方案加速器的已部署实例，使其能够与真实或模拟设备进行交互。

若要准备本地开发环境，请使用 Git 将[远程监视 WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) 存储库克隆到本地计算机：

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>添加页面

要向 Web UI 添加页，需要添加定义页的源文件，然后修改部分现有文件，以使 Web UI 感知新页。

### <a name="add-the-new-files-that-define-the-page"></a>添加定义页的新文件

首先，src/walkthrough/components/pages/basicPage 文件夹包含定义简单页的四个文件  ：

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

创建新文件夹 src/components/pages/example 并将此四个文件复制到其中  。

### <a name="add-the-new-page-to-the-web-ui"></a>将新页添加到 Web UI

若要将新页添加到 Web UI，请对现有文件进行以下更改：

1. 将新页容器添加到 src/components/pages/index.js 文件中  ：

    ```js
    export * from './example/basicPage.container';
    ```

1. （可选）为新页添加 SVG 图标。 有关详细信息，请参阅 [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md)。 可使用现有 SVG 文件。

1. 将页名添加到翻译文件 public/locales/en/translations.json 中  。 Web UI 使用 [i18next](https://www.i18next.com/) 支持国际化。

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. 打开定义顶级应用程序页的 src/components/app.js 文件  。 将新页添加到导入列表：

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. 在同一文件中，将新页添加到 `pagesConfig` 数组。 设置路由的 `to` 地址，引用之前添加的 SVG 图标和翻译，并将 `component` 设置为页的容器：

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. 将任何新的痕迹导航添加到 `crumbsConfig` 数组：

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    此示例页只有一个痕迹导航，但有些页可能有更多痕迹导航。

保存所有更改。 现在即可使用添加的新页运行 Web UI。

### <a name="test-the-new-page"></a>测试新页

在命令提示符处，导航到存储库本地副本的根目录，然后运行以下命令以安装所需的库并在本地运行 Web UI：

```cmd/sh
npm install
npm start
```

上述命令在 [http://localhost:3000/dashboard](http://localhost:3000/dashboard) 以本地方式运行 UI。

如果不将 Web UI 的本地实例连接到解决方案加速器的已部署实例，仪表板上会出现错误。 这些错误不会影响测试新页的能力。

现在可在站点本地运行时编辑代码，并动态地查看 Web UI 更新。

## <a name="optional-connect-to-deployed-instance"></a>[可选] 连接已部署的实例

（可选）可将 Web UI 的本地运行副本连接到云中的远程监视解决方案加速器：

1. 使用 **pcs** CLI 部署解决方案加速器的**基本**实例。 记下部署名称以及为虚拟机提供的凭据。 有关详细信息，请参阅[使用 CLI 部署](iot-accelerators-remote-monitoring-deploy-cli.md)。

1. 使用 Azure 门户或 [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 来启用对托管解决方案中的微服务的虚拟机的 SSH 访问。 例如：

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    仅应在测试和开发期间启用 SSH 访问。 如果启用 SSH，[应尽快再次禁用](../security/azure-security-network-security-best-practices.md)。

1. 使用 Azure 门户或 [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 查找虚拟机的名称和公共 IP 地址。 例如：

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. 使用前一步骤中的 IP 地址以及运行 **pcs** 部署解决方案时提供的凭据，通过 SSH 连接到虚拟机。

1. 若要允许本地 UX 建立连接，请在虚拟机上的 bash shell 中运行以下命令：

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. 看到命令完成并且网站启动后，可从虚拟机断开连接。

1. 在 [远程监视 WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) 存储库的本地副本中，编辑 .env 文件以添加部署的解决方案的 URL  ：

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>后续步骤

本文已介绍可用来帮助自定义远程监视解决方案加速器中 Web UI 的资源。

现在已定义了页，下一步是[向远程监视解决方案加速器 Web UI 添加自定义服务](iot-accelerators-remote-monitoring-customize-service.md)，该服务检索要在 UI 中显示的数据。

有关远程监视解决方案加速器的其他概念性信息，请参阅[远程监视体系结构](iot-accelerators-remote-monitoring-sample-walkthrough.md)。
