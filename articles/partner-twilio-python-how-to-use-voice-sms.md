---
title: "如何使用 Twilio 实现语音和短信 (Python) - Microsoft 文档"
description: "了解如何在 Azure 中使用 Twilio API 服务发起电话呼叫和发送短信。 代码示例是使用 Python 编写的。"
services: 
documentationcenter: python
author: devinrader
manager: twilio
editor: 
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: MicrosoftHelp@twilio.com
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: f4a02bb7a7c46e7a0e3c75b870c522eae8294339
ms.lasthandoff: 03/07/2017


---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>如何通过 Python 使用 Twilio 实现语音和 SMS 功能
本指南演示如何在 Azure 中使用 Twilio API 服务执行常见编程任务。 所涉及的任务包括发起电话呼叫和发送短信服务 (SMS) 消息。 有关 Twilio 以及在应用程序中使用语音和短信的详细信息，请参阅[后续步骤](#NextSteps)部分。

## <a id="WhatIs"></a>什么是 Twilio？
Twilio 为将来的商业沟通提供强大支持，并使开发人员能够将语音、VoIP 和消息传送嵌入到应用程序中。 它们对基于云的全球环境中所需的所有基础结构进行虚拟化，并通过 Twilio 通信 API 平台将其公开。 可轻松构建和扩展应用程序。 享受现用现付定价所带来的灵活性，并从云可靠性中受益。

利用 **Twilio 语音**，应用程序可以发起和接收电话呼叫。
**Twilio SMS** 使应用程序能够发送和接收文本消息。
利用 **Twilio 客户端**，可以从任何手机、平板电脑或浏览器发起 VoIP 呼叫并支持 WebRTC。

## <a id="Pricing"></a>Twilio 定价和特别优惠
Azure 客户在升级 Twilio 帐户后即可获得&10; 美元的 Twilio 信用额度的[特别优惠][special_offer]。 此 Twilio 信用可应用于任何 Twilio 使用（10 美元信用等价于发送多达 1,000 条 SMS 消息或接收长达 1000 分钟的入站语音，具体取决电话号码和消息或呼叫目标的位置）。 兑换此 [Twilio 信用][special_offer]并开始使用。

Twilio 是一种现用现付服务。 没有设置费用，并且您可以随时关闭您的帐户。 可以在 [Twilio 定价][twilio_pricing]中找到更多详细信息。

## <a id="Concepts"></a>概念
Twilio API 是一个为应用程序提供语音和 SMS 功能的 RESTful API。 提供了多种语言版本的客户端库；有关列表，请参阅 [Twilio API 库][twilio_libraries]。

Twilio API 的关键方面是 Twilio 谓词和 Twilio 标记语言 (TwiML)。

### <a id="Verbs"></a>Twilio 谓词
API 利用了 Twilio 谓词；例如，**&lt;Say&gt;** 谓词指示 Twilio 在呼叫时传递语音消息。

下面是 Twilio 谓词的列表。 通过 [Twilio 标记语言文档][twiml]了解其他谓词和功能。

* **&lt;Dial&gt;**：将呼叫方连接到其他电话。
* **&lt;Gather&gt;**：收集通过电话按键输入的数字。
* **&lt;Hangup&gt;**：结束呼叫。
* **&lt;Pause&gt;**：安静地等待指定的时间（以秒为单位）。
* **&lt;Play&gt;**：播放音频文件。
* **&lt;队列&gt;**：添加到调用方的队列。
* **&lt;Record&gt;**：录制呼叫方的声音并返回包含该录音的文件的 URL。
* **&lt;Redirect&gt;**：将对呼叫或 SMS 的控制转移到其他 URL 上的 TwiML。
* **&lt;Reject&gt;**：拒绝对 Twilio 号码的传入呼叫且无需付费。
* **&lt;Say&gt;**：将文本转换为呼叫中生成的语音。
* **&lt;Sms&gt;**：发送 SMS 消息。

### <a id="TwiML"></a>TwiML
TwiML 是一组基于 XML 的指令，这些指令以用于指示 Twilio 如何处理呼叫或 SMS 的 Twilio 谓词为基础。

例如，以下 TwiML 将文本 **Hello World** 转换为语音。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

当应用程序调用 Twilio API 时，某个 API 参数将为返回 TwiML 响应的 URL。 在开发过程中，可以使用 Twilio 提供的 URL 来提供应用程序所使用的 TwiML 响应。 还可以托管自己的 URL 以生成 TwiML 响应，也可以选择使用 `TwiMLResponse` 对象。

有关 Twilio 谓词、其属性和 TwiML 的详细信息，请参阅 [TwiML][twiml]。 有关 Twilio API 的其他信息，请参阅 [Twilio API][twilio_api]。

## <a id="CreateAccount"></a>创建 Twilio 帐户
准备好获取 Twilio 帐户后，请在[试用 Twilio][try_twilio] 上注册。 可以先使用免费帐户，以后再升级您的帐户。

注册 Twilio 帐户时，你将收到帐户 SID 和身份验证令牌。 需要二者才能发起 Twilio API 呼叫。 为了防止对您的帐户进行未经授权的访问，请保护身份验证令牌。 帐户 SID 和身份验证令牌会分别显示在 [Twilio 控制台][twilio_console]上标记为“帐户 SID”和“身份验证令牌”的字段中。

## <a id="create_app"></a>创建 Python 应用程序
使用 Twilio 服务且在 Azure 中运行的 Python 应用程序与任何其他使用 Twilio 服务的 Python 应用程序没有任何差别。 Twilio 服务是基于 REST 的且可通过几种方法从 Python 中调用，本文将重点介绍如何将 Twilio 服务与 [GitHub 提供的用于 Python 的 Twilio 库][twilio_python]一起使用。 有关使用用于 Python 的 Twilio 库的详细信息，请参阅 [http://readthedocs.org/docs/twilio-python/en/latest/index.html][twilio_lib_docs]。

首先，[设置一个新的 Azure Linux VM][azure_vm_setup] 充当新 Python Web 应用程序的主机。 虚拟机运行后，需要在公共端口上公开应用程序，如下所述。

### <a name="add-an-incoming-rule"></a>添加传入规则
  1. 转到[网络安全组][azure_nsg] 页。
  2. 选择与虚拟机对应的网络安全组。
  3. 为**端口 80** 添加**传出规则**。 请确保允许从任何地址传入。

### <a name="set-the-dns-name-label"></a>设置 DNS 名称标签
  1. 转到[公共 IP 地址][azure_ips] 页。
  2. 选择与虚拟机对应的公共 IP。
  3. 在“配置”部分中设置“DNS 名称标签”。 就此示例而言，它将如此 *your-domain-label*.centralus.cloudapp.azure.com 中所示

能够通过 SSH 连接到虚拟机后，便可以安装所选的 Web 框架（Python 中两个最著名的 Web 框架是 [Flask](http://flask.pocoo.org/) 和 [Django](https://www.djangoproject.com)）。 只需运行 `pip install` 命令即可安装其中任一个 Web 框架。

请记住，我们已将虚拟机配置为仅允许端口 80 上的流量。 因此，请确保将应用程序配置为使用此端口。

## <a id="configure_app"></a>将应用程序配置为使用 Twilio 库
可以通过两种方式将应用程序配置为使用用于 Python 的 Twilio 库：

* 将用于 Python 的 Twilio 库作为 Pip 包安装。 可使用以下命令安装它：
   
        $ pip install twilio

    - 或 -

* 从 GitHub 下载用于 Python 的 Twilio 库 ([https://github.com/twilio/twilio-python][twilio_python]) 并将其安装如下：

        $ python setup.py install

安装用于 Python 的 Twilio 库后，可以在 Python 文件中`import`它：

        import twilio

有关详细信息，请参阅 [https://github.com/twilio/twilio-python/blob/master/README.md][twilio_github_readme]。

## <a id="howto_make_call"></a>如何：发起传出呼叫
下面演示如何发起传出呼叫。 此代码还使用 Twilio 提供的网站返回 Twilio 标记语言 (TwiML) 响应。 用自己的值替换 **from_number** 和 **to_number** 电话号码，并确保在运行代码之前验证 Twilio 帐户的 **from_number** 电话号码。

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "http://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

如前所述，此代码使用 Twilio 提供的网站返回 TwiML 响应。 可以改用自己的网站来提供 TwiML 响应；有关详细信息，请参阅[如何从自己的网站提供 TwiML 响应](#howto_provide_twiml_responses)。

## <a id="howto_send_sms"></a>如何：发送 SMS 消息
以下代码演示了如何使用 `TwilioRestClient` 类发送短信。 **from_number** 号码由 Twilio 提供，供试用帐户用来发送短信。 在运行代码前，必须为 Twilio 帐户验证 **to_number** 号码。

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a id="howto_provide_twiml_responses"></a>如何：从您自己的网站提供 TwiML 响应
当你的应用程序发起对 Twilio API 的调用时，Twilio 会将你的请求发送到应返回 TwiML 响应的 URL。 上面的示例使用 Twilio 提供的 URL [http://twimlets.com/message][twimlet_message_url]。 （虽然 TwiML 专供 Twilio 使用，但你可以在浏览器中查看它。 例如，单击 [http://twimlets.com/message][twimlet_message_url] 可查看空 `<Response>` 元素；又如，单击 [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] 可查看包含 `<Say>` 元素的 `<Response>` 元素。）

你可以创建自己的返回 HTTP 响应的网站，而不用依赖 Twilio 提供的 URL。 可以使用任何语言创建返回 XML 响应的站点；本主题假设使用 Python 创建 TwiML。

以下示例将输出在呼叫时念出 **Hello World** 的 TwiML 响应。

使用 Flask：

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

使用 Django：

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

如上面的示例中所示，TwiML 响应只是一个 XML 文档。 用于 Python 的 Twilio 库包含将为你生成 TwiML 的类。 以下示例将生成与上面所示相同的响应，但该响应会使用用于 Python 的 Twilio 库中的 `twiml` 模块：

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

有关 TwiML 的详细信息，请参阅 [https://www.twilio.com/docs/api/twiml][twiml_reference]。

将 Python 应用程序页设置为提供 TwiML 响应后，请使用应用程序的 URL 作为传入到 `client.calls.create` 方法中的 URL。 例如，如果将名为 **MyTwiML** 的 Web 应用程序部署到 Azure 托管服务，则可以使用其 URL 作为 webhook，如以下示例中所示：

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a id="AdditionalServices"></a>如何：使用其他 Twilio 服务
除了此处所示的示例之外，Twilio 还提供了基于 Web 的 API，可通过这些 API 从 Azure 应用程序中使用其他 Twilio 功能。 有关完整详细信息，请参阅 [Twilio API 文档][twilio_api]。

## <a id="NextSteps"></a>后续步骤
了解 Twilio 服务的基础知识后，请单击以下链接了解更多信息：

* [Twilio 安全准则][twilio_security_guidelines]
* [Twilio 操作方法指南和示例代码][twilio_howtos]
* [Twilio 快速入门教程][twilio_quickstarts]
* [GitHub 上的 Twilio][twilio_on_github]
* [与 Twilio 技术支持交流][twilio_support]

[special_offer]: http://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-python/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart

