---
title: JavaScript 示例
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中使用 JavaScript。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a26f6c5e69ca083335580a0368459e062de3941e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187655"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>用于 Azure Active Directory B2C 中的 JavaScript 示例

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

您可以将自己的 JavaScript 客户端代码添加到您的 Azure Active Directory B2C （Azure AD B2C）应用程序中。

为应用程序启用 JavaScript：

* 向[自定义策略](custom-policy-overview.md)添加元素
* 选择[页面布局](page-layout.md)
* 在请求中使用[b2clogin.com](b2clogin.md)

本文介绍如何更改自定义策略以启用脚本执行。

> [!NOTE]
> 如果要为用户流启用 JavaScript，请参阅[Azure Active Directory B2C 中的 javascript 和页面布局版本](user-flow-javascript-overview.md)。

## <a name="prerequisites"></a>系统必备

### <a name="select-a-page-layout"></a>选择页面布局

* 为应用程序的用户界面元素选择[页面布局](contentdefinitions.md#select-a-page-layout)。

    如果打算使用 JavaScript，则需要为自定义策略中的*所有*内容定义[定义页面布局版本](contentdefinitions.md#migrating-to-page-layout)以及页面 `contract` 版本。

## <a name="add-the-scriptexecution-element"></a>添加 ScriptExecution 元素

通过向 [RelyingParty](relyingparty.md)元素添加 ScriptExecution 元素来启用脚本执行。

1. 打开自定义策略文件。 例如，SignUpOrSignin.xml。
2. 向 RelyingParty 的 UserJourneyBehaviors 元素添加 ScriptExecution 元素：

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. 保存并上传文件。

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="javascript-samples"></a>JavaScript 示例

### <a name="show-or-hide-a-password"></a>显示或隐藏密码

帮助客户成功注册的一种常见的方式是：允许他们查看他们作为密码输入的内容。 该选项使用户能够根据需要轻松查看和更正密码，从而帮助用户登录。 任何密码类型的字段都有一个带有“显示密码”标签的复选框。  这使用户能够看到纯文本形式的密码。 使自断言页面的注册或登录模板中包括以下代码片段：

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>添加使用条款

使需要包含“使用条款”复选框的页面中含有以下代码。 本地帐户注册和社交帐户注册页面中通常需要该复选框。

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

在代码中，将 `termsOfUseUrl` 替换使用条款协议的链接。 对于目录，创建名为 " **termsOfUse** " 的新用户属性，然后将 " **termsOfUse** " 作为用户属性。

## <a name="next-steps"></a>后续步骤

有关如何自定义应用程序用户界面的详细信息，请参阅[在 Azure Active Directory B2C 中使用自定义策略自定义应用程序的用户界面](custom-policy-ui-customization.md)。
