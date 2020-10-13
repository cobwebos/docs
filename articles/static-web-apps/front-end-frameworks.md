---
title: 通过 Azure 静态 Web 应用预览配置前端框架
description: Azure 静态 Web 应用所需的常用前端框架的设置
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 4b1bc58b6b4a87cd6e5e09e83020a38261b8746f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905366"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>通过 Azure 静态 Web 应用预览配置前端框架和库

Azure 静态 Web 应用要求在前端框架或库的 [生成配置文件](github-actions-workflow.md) 中具有相应的配置值。

## <a name="configuration"></a>Configuration

下表列出了一系列框架和库的设置<sup>1</sup>。

表列的目的是通过以下项进行说明：

- **应用项目位置**：列出的值 `app_artifact_location` ，它是 [应用程序文件的生成版本的文件夹](github-actions-workflow.md#build-and-deploy)。

- **自定义生成命令**：当框架需要不同于或的命令时 `npm run build` `npm run azure:build` ，可以定义 [自定义生成命令](github-actions-workflow.md#custom-build-commands)。

| 框架 | 应用项目位置 | 自定义生成命令 |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | 不适用 <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [协调世界](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia meets](https://aurelia.io/) | `dist` | 不适用 |
| [Backbone.js](https://backbonejs.org/) | `/` | 不适用 |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | 不适用 |
| [Ember](https://emberjs.com/) | `dist` | 不适用 |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | 不适用 |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | 不适用 |
| [Hyperapp](https://hyperapp.dev/) | `/` | 不适用 |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | 不适用 |
| [jQuery](https://jquery.com/) | `/` | 不适用 |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | 不适用 |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | 不适用 |
| [Marko](https://markojs.com/) | `public` | 不适用 |
| [流星](https://www.meteor.com/) | `bundle` | 不适用 |
| [Mithril](https://mithril.js.org/) | `dist` | 不适用 |
| [Polymer](https://www.polymer-project.org/) | `build/default` | 不适用 |
| [Preact](https://preactjs.com/) | `build` | 不适用 |
| [React](https://reactjs.org/) | `build` | 不适用 |
| [模具](https://stenciljs.com/) | `www` | 不适用 |
| [Svelte](https://svelte.dev/) | `public` | 不适用 |
| [Three.js](https://threejs.org/) | `/` | 不适用 |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | 不适用 |
| [Vue.js](https://vuejs.org/) | `dist` | 不适用 |

<sup>1</sup> 上面的表并不是与 Azure 静态 Web 应用配合使用的框架和库的详尽列表。

<sup>2</sup> 不适用

## <a name="next-steps"></a>后续步骤

- [生成和工作流配置](github-actions-workflow.md)
