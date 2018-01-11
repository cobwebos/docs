---
title: "将 iOS Web 视图与原生 Mobile Engagement iOS SDK 桥接"
description: "介绍如何在运行 Javascript 的 Web 视图和原生 Mobile Engagement iOS SDK 之间创建网桥"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: e1d6ff6f-cd67-4131-96eb-c3d6318de752
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 35f7bdbeb480122513ae2a0b04a6d8cfd426802a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>将 iOS Web 视图与原生 Mobile Engagement iOS SDK 桥接
> [!div class="op_single_selector"]
> * [Android 网桥](mobile-engagement-bridge-webview-native-android.md)
> * [iOS 网桥](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

某些移动应用被设计成混合应用，而应用本身是使用原生 iOS Objective C 开发进行开发的，但部分或者甚至所有屏幕都是在 iOS Web 视图中进行渲染的。 仍可以使用此类应用内的 Mobile Engagement iOS SDK，本教程介绍如何执行此操作。 

有两种方法可以实现此目的，但这两种方法都是未经事实证明的：

* 有关第一种方法的描述请查看此[链接](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip)，这需要在 Web 视图上注册 `UIWebViewDelegate`，然后捕获并立即取消在 Javascript 中进行的位置更改。 
* 第二种方法基于此 [WWDC 2013 会话](https://developer.apple.com/videos/play/wwdc2013/615)，这是一种比第一种方法更简洁的方法，在本指南中我们将遵循这第二种方法。 请注意，此方法仅可以在 iOS7 及更高版本上正常工作。 

对于 iOS 桥接示例，请执行以下步骤︰

1. 首先，需要确保已完成我们的[入门教程](mobile-engagement-ios-get-started.md)，以便在混合应用中集成 Mobile Engagement iOS SDK。 还可以选择启用测试日志记录，如下所示，以便在我们从 Web 视图触发 SDK 方法时，可以看到它们。 
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
             [EngagementAgent setTestLogEnabled:YES];
           ....
        }
2. 现在请确保混合应用有一个包含 Web 视图的屏幕。 可以将其添加到应用的 `Main.storyboard` 中。 
3. 通过单击 Web 视图并将其从视图控制器场景拖动到 `ViewController.h` 编辑屏幕（将其紧挨着放置在 `@interface` 行的下方），将此 Web 视图与 **ViewController** 相关联。 
4. 执行此操作之后，将立即弹出一个对话框询问名称。 提供该名称作为 **Web 视图**。 `ViewController.h` 文件应如下所示：
   
        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
   
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
   
        @end
5. 我们稍后将更新 `ViewController.m` 文件，但首先我们将创建网桥文件，该网桥文件会在某些常用 Mobile Engagement iOS SDK 方法上创建一个包装。 创建一个名为 **EngagementJsExports.h** 的新头文件，该头文件会使用前面提到的[会话](https://developer.apple.com/videos/play/wwdc2013/615)中描述的 `JSExport` 机制，以公开本机 iOS 方法。 
   
        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
   
        @protocol EngagementJsExports <JSExport>
   
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
   
        @end
   
        @interface EngagementJs : NSObject <EngagementJsExports>
   
        @end
6. 接下来我们将创建网桥文件的第二部分。 创建一个名为 **EngagementJsExports.m** 的文件，该文件将包含通过调用 Mobile Engagement iOS SDK 方法来创建实际包装的实现。 此外应注意，我们正在分析从 Web 视图 javascript 传递的 `extras`并将其放置到一个 `NSMutableDictionary` 对象中，以与 Engagement SDK 方法调用一起传递。  
   
        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
   
        @implementation EngagementJs
   
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
   
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
   
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
   
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
   
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
   
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
   
           return extras;
        }
   
        @end
7. 现在我们又回到 **ViewController.m**，并使用下面的代码更新它： 
   
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
   
        @interface ViewController ()
   
        @end
   
        @implementation ViewController
   
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
   
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
   
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
   
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
   
           context[@"EngagementJs"] = [EngagementJs class];
        }
   
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
   
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
   
        @end
8. 关于 **ViewController.m** 文件，请注意以下几点：
   
   * 在 `loadWebView` 方法中，我们正在加载一个称为 **LocalPage.html** 的本地 HTML 文件，我们接下来将查看其代码。 
   * 在 `webViewDidFinishLoad` 方法中，我们将获取 `JsContext` 并将我们的包装类与其相关联。 这会允许从 Web 视图使用句柄 **EngagementJs** 调用我们的包装 SDK 方法。 
9. 使用以下代码创建名为 **LocalPage.html** 的文件：
   
        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
   
               <script type="text/javascript">
   
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
   
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
   
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
   
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
   
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
   
           </head>
           <body>
               <h1>Bridge Tester</h1>
   
               <div id='engagement'>
   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
   
               </div>
           </body>
        </html>
10. 关于上述 HTML 文件，请注意以下几点：
    
    * 它包含一组输入框，可以在其中提供要用作事件、作业、错误和应用信息的名称的数据。 单击它旁边的按钮时，将调用 Javascript，最终调用网桥文件中的方法将此调用传递到 Mobile Engagement iOS SDK。 
    * 我们对事件、作业和甚至是错误标记某些静态额外信息，以演示这是如何完成的。 如果查看 `EngagementJsExports.m` 文件，此额外信息会以 JSON 字符串形式发送，JSON 字符串会被分析，并在发送事件、作业和错误时一起传递。 
    * 启动一个 Mobile Engagement 作业，名称在输入框中指定的名称，运行 10 秒并关闭。 
    * 系统会传递 Mobile Engagement 应用信息或标记，以“customer name”作为静态密钥，并会在输入框中输入的值作为标记的值。 
11. 运行该应用，会看到以下内容。 现在为测试事件提供一些名称，如下所示，并单击其旁边的**发送**。 
    
     ![][1]
12. 如果现在转到应用的“**监视器**”选项卡，查看“**事件 -> 详细信息**”，会看到此事件以及我们发送的静态应用信息。 
    
    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
