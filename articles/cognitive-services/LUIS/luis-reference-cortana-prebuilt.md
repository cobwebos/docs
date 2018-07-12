---
title: Cortana 预构建的应用参考 | Microsoft Docs
description: 语言理解智能服务 (LUIS) 的预构建应用程序 - Cortana 个人助理的参考。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: cd808c30a6781fc0252992c13ba247486e35ad44
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365575"
---
# <a name="cortana-prebuilt-app-reference"></a>Cortana 预构建的应用参考
此参考列出了 Cortana 预构建应用识别的意向和实体。

## <a name="cortana-prebuilt-intents"></a>Cortana 预构建的意向
预构建个人助理应用程序可识别以下意向：

| 意向 | 示例陈述 |
|--------| ------------------|
| builtin.intent.alarm.alarm_other|将 7:30 的闹钟更新为 8:00 <br/>将早上 8 点的闹钟更改为早上 9 点 |
| builtin.intent.alarm.delete_alarm|删除闹钟 <br/>删除“起床”闹钟|
| builtin.intent.alarm.find_alarm|我的起床闹钟设置的是几点？ <br/> 我的起床闹钟开启了吗？ |
| builtin.intent.alarm.set_alarm|开启我的起床闹钟<br/>可以设置 12 点的闹钟提醒我吃抗生素吗？|
| builtin.intent.alarm.snooze|5 分钟后响起的贪睡闹钟<br/>贪睡闹钟|
| builtin.intent.alarm.time_remaining| 距离“起床”时间还有多久？ <br/> 距离下一闹钟还有多久？|
| builtin.intent.alarm.turn_off_alarm | 关闭我早上 7 点的闹钟 <br/> 关闭我的起床闹钟 |
| builtin.intent.calendar.change_calendar_entry| 更改约会<br/>将我的会议从今天改到明天|
|builtin.intent.calendar.check_availability|Tim 周五有空吗？<br/>Brian 周六有空吗|
|builtin.intent.calendar.connect_to_meeting|连接到会议<br/>加入联机会议|
|builtin.intent.calendar.create_calendar_entry|需要安排周五与 Tony 的约会<br/>周日下午 2 点与 Lisa 有约会|
|builtin.intent.calendar.delete_calendar_entry|删除我的约会<br/>从我的日历删除明天下午 3 点的会议|
|builtin.intent.calendar.find_calendar_entry|显示我的日历<br/>显示我的周日历|
|builtin.intent.calendar.find_calendar_when|与 Jon 下次会面定在什么时候？<br/>周一与 Larry 的约会具体是什么时间？|
|builtin.intent.calendar.find_calendar_where|显示我 6 点会议的地点<br/>与 Jon 在什么地方见面？|
|builtin.intent.calendar.find_calendar_who|与谁进行这次会议？<br/>还邀请了谁？|
|builtin.intent.calendar.find_calendar_why|我 11 点会议的具体内容是什么？<br/>与 Jon 的会面谈论些什么？|
|builtin.intent.calendar.find_duration|下次会议进行多久<br/>今天下午的会议进行多少分钟|
|builtin.intent.calendar.time_remaining|距离下次约会还有多久？<br/>会议开始前我最多还有多少时间？|
|builtin.intent.communication.add_contact|保存这个号码，将姓名存为 Jose<br/>将 Jason 保存到我的通讯录|
|builtin.intent.communication.answer_phone|回应<br/>接听电话|
|builtin.intent.communication.assign_nickname|给 Nickolas 编个昵称<br/>为 John Doe 添个昵称|
|builtin.intent.communication.call_voice_mail|语音邮件<br/>连接语音信箱|
|builtin.intent.communication.find_contact|显示我的联系人<br/>查找联系人|
|builtin.intent.communication.forwarding_off|停止转移我的来电<br/>关闭呼叫转移|
|builtin.intent.communication.forwarding_on|设置呼叫转移，将来电转到住宅电话<br/>将来电转到住宅电话|
|builtin.intent.communication.ignore_incoming|不接这个电话<br/>现在不接，我正忙|
|builtin.intent.communication.ignore_with_message|不接这个电话，但发送一封邮件<br/>忽略并回复短信|
|builtin.intent.communication.make_call|呼叫 Bob 和 John<br/>呼叫妈妈和爸爸|
|builtin.intent.communication.press_key|拨星号键<br/>按 1 2 3|
|builtin.intent.communication.read_aloud|阅读短信<br/>她在邮件中说了什么|
|builtin.intent.communication.redial|重拨最近一次通话的号码<br/>重拨|
|builtin.intent.communication.send_email|向 Mike Waters 发送电子邮件：Mike，上周的晚餐很棒<br/>向 Bob 发送电子邮件|
|builtin.intent.communication.send_text|向 Bob 发条短信，向 John 发封邮件<br/>message|
|builtin.intent.communication.speakerphone_off|关闭免提<br/>关闭免提|
|builtin.intent.communication.speakerphone_on|免提模式<br/>打开免提|
|builtin.intent.mystuff.find_attachment|查找 John 昨天电邮给我的文档 <br/>查找 John 发来的文档|
|builtin.intent.mystuff.find_my_stuff|我想编辑昨天的购物清单<br/>查找 9 月份的化学笔记
|builtin.intent.mystuff.search_messages|打开邮件 <br/>messages|
|builtin.intent.mystuff.transform_my_stuff|与丈夫共享购物清单<br/>删除购物清单|
|builtin.intent.ondevice.open_setting|打开 Cortana 设置 <br/>转到通知|
|builtin.intent.ondevice.pause|关闭音乐<br/>关音乐|
|builtin.intent.ondevice.play_music|我想听 owner of a lonely heart<br/>播放些福音音乐|
|builtin.intent.ondevice.recognize_song|告诉我这是哪首歌<br/>分析并检索歌名|
|builtin.intent.ondevice.repeat|重复这个曲目<br/>重播这首歌|
|builtin.intent.ondevice.resume|重新开始音乐<br/>再次开始播放音乐|
|builtin.intent.ondevice.skip_back|倒退一首歌<br/>上一首|
|builtin.intent.ondevice.skip_forward|下一首<br/>跳过一首歌|
|builtin.intent.ondevice.turn_off_setting|WiFi 关闭<br/>禁用飞行模式|
|builtin.intent.ondevice.turn_on_setting|WiFi 开启<br/>打开蓝牙|
|builtin.intent.places.add_favorite_place|将这个地址添加到收藏夹<br/>将这个地点保存到收藏夹|
|builtin.intent.places.book_public_transportation|购买火车票<br/>预定火车票|
|builtin.intent.places.book_taxi|你能不能在这个点帮我叫辆车？<br/>叫辆出租车|
|builtin.intent.places.check_area_traffic|520 的路况怎么样<br/>i-5 的路况怎么样|
|builtin.intent.places.check_into_place|登记入住 Joya<br/>在此登记入住|
|builtin.intent.places.check_route_traffic|向我展示去柯克兰的路况如何<br/>去西雅图的路况如何？|
|builtin.intent.places.find_place|我住哪里 <br/>告诉我排在前面 3 位的日本餐厅|
|builtin.intent.places.get_address|向我展示鲁滨逊大街上 Guu 的地址<br/>最近的星巴克在哪里？|
|builtin.intent.places.get_coupon|查找我所在区域的电视交易<br/>山景城中的折扣|
|builtin.intent.places.get_distance|假日酒店还有多远？<br/>离 Tahoe 有多远|
|builtin.intent.places.get_hours|周一 Bar Del Corso 的营业时间是什么？<br/>图书馆什么时候开馆？|
|builtin.intent.places.get_menu|向我展示 Applebee 的菜单<br/>Sizzler 的菜单上有些什么|
|builtin.intent.places.get_phone_number|告诉我家得宝的电话<br/>最近一家星巴克的电话是多少？|
|builtin.intent.places.get_price_range|Red Lobster 用餐的消费水平如何<br/>Purple Cafe 有多贵|
|builtin.intent.places.get_reviews|向我展示人们对当地五金店的评价<br/>我想看餐厅点评|
|builtin.intent.places.get_route|为我导航|可以走路去必胜客|
|builtin.intent.places.get_star_rating|French Laundry 是几星级的？<br/>蒙特雷的水族馆好不好？|
|builtin.intent.places.get_transportation_schedule|旧金山渡轮什么时候起航？<br/>到西雅图最晚的火车是几点？|
|builtin.intent.places.get_travel_time|从旧金山到丹佛开车要多久<br/>从这里到旧金山要多久|
|builtin.intent.places.make_call|呼叫 Bellevue 的 Smith<br/>致电第 1 大街上的家得宝|
|builtin.intent.places.rate_place|为这家餐厅评级<br/>在 Yelp 上为 Il Fornaio 打 5 星|
|builtin.intent.places.show_map|我现在在哪 <br/>我在哪|
|builtin.intent.places.takes_reservations|可以在 Olive Garden 预订座位<br/>美术馆接受预约吗|
|builtin.intent.reminder.change_reminder|更改提醒<br/>将我的“照相日”提醒提前 30 分钟|
|builtin.intent.reminder.create_single_reminder|提醒我早上 7 点起床<br/>提醒我下午 4:40 跟 Luca 玩“不请吃就捣蛋”|
|builtin.intent.reminder.delete_reminder|删除此提醒<br/>删除我的照相提醒|
|builtin.intent.reminder.find_reminder|今天我有没有任何提醒<br/>我有没有个 Luca 派对的提醒|
|builtin.intent.reminder.read_aloud|大声读出提醒<br/>朗读这条提醒|
|builtin.intent.reminder.snooze|将这条提醒推到明天<br/>推迟这条提醒|
|builtin.intent.reminder.turn_off_reminder|关闭提醒<br/>取消接机提醒|
|builtin.intent.weather.change_temperature_unit|从华氏度变为开尔文<br/>从华氏度变为摄氏度|
|builtin.intent.weather.check_weather|为我即将到来的西雅图之旅展示天气预报<br/>这周末的天气如何|
|builtin.intent.weather.check_weather_facts|夏威夷 12 月的天气如何？<br/>去年这个时候的温度如何？|
|builtin.intent.weather.compare_weather|比较德克萨斯州达拉斯和休斯敦的高温和低温<br/>与盐湖城和纽约相比，天气如何|
|builtin.intent.weather.get_frequent_locations|告诉我我最常去哪<br/>展示最常使用的公交站|
|builtin.intent.weather.get_weather_advisory|天气预警<br/>展示萨克拉门托的天气预报|
|builtin.intent.weather.get_weather_maps|显示气象图<br/>向我展示非洲的气象图|
|builtin.intent.weather.question_weather|明早会起雾吗？<br/>我这周末需要铲雪吗？|
|builtin.intent.weather.show_weather_progression|展示当地气象雷达图<br/>开始雷达扫描|
|builtin.intent.none|你多大了<br/>打开照相机|

## <a name="prebuilt-entities"></a>预构建的实体 

下面是预构建的个人助理应用程序可以识别的一些实体：

|实体 |陈述中的实体示例 |
|-------|------------------|
|builtin.alarm.alarm_state | 将我的起床闹钟设为`off`    <br/> 我的起床闹钟是否设为`on`  | 
|builtin.alarm.duration |贪睡 `10 minutes`    <br/> `5 minutes`后响的贪睡闹钟  |
|builtin.alarm.start_date | 设一个`monday`早上 7 点的闹钟   <br/> 设一个`tomorrow`中午的闹钟   |
|builtin.alarm.start_time | 创建一个 `30 minutes`后响的闹钟    <br/> 将闹钟设为 `in 20 minutes`关闭   |
|builtin.alarm.title | 我的`wake up`闹钟是否开启 <br/> 可否设一个周一到周五 11:45 响的`take antibiotics`闹钟 |
|builtin.calendar.absolute_location | 创建一个约会，时间是明天，地点在 `123 main street`   <br/> 会议于 6 月 5 日在`cincinnati`举行    |
|builtin.calendar.contact_name|在日历上创建一个市场营销会议，并确保 `joe` 出席 <br/>我想在 Il Fornaio 请吃午饭，并邀请 `paul` |   
|builtin.calendar.destination_calendar|将此项添加到`work`日程表   <br/>将此项添加到`personal`日历 |
|builtin.calendar.duration| 定一个今晚 6 点 `an hour`的约会 <br/>  与 Joe 预约一个 `2 hour`的会面 |  
|builtin.calendar.end_date | 创建一个日历条目，名为“度假”，时间是明天到`next monday` <br/>    将我的时间设为忙碌，直到`monday, october 5th` | 
|builtin.calendar.end_time | 会议在`5:30 PM` 结束 <br/> 计划为从 11 点到`noon`  |   
|builtin.calendar.implicit_location | 取消 DMV 的约会 <br/> 将 Miles 生日聚会地点改为 `poppy restaurant` |    
|builtin.calendar.move_earlier_time | 将会议提前 `an hour` <br/> 将牙医约会提前 `30 minutes`       |
|builtin.calendar.move_later_time | 将牙医约会延后 `30 minutes`    <br/> 将会议延后 `an hour` |  
|builtin.calendar.original_start_date | 重新安排理发店的约会，将时间从周二改到周三 <br/> 将与 Ken 的会面从`monday`改到周二 |
|builtin.calendar.original_start_time | 将会议从 `2:00` 推到 3:00  <br/> 将牙医约会从 `3:30` 改到 4:00 |  
|builtin.calendar.start_date | `flag day`的聚会什么时候开始 <br/> 安排`friday after next`中午的午餐 
|builtin.calendar.start_time | 我想将它安排在`this morning` <br/> 我想将它安排在`morning` |  
|builtin.calendar.title | `vet appointment`  <br/> 周二`dentist` |
|builtin.communication.audio_device_type | 使用`bluetooth`呼叫  <br/> 使用`headset`通话 | 
|builtin.communication.contact_name | 给 `bob jones` 发短信  <br/> | 给 `sarah` 打电话|
|builtin.communication.destination_platform | 致电`london`的 Dave <br/> 拨打他的`work line` |    
|builtin.communication.from_relationship_name | 显示来自我`daughter`的电话 <br/> 朗读来自我`mom`的电子邮件   |   
|builtin.communication.key | 拨`star` <br/>  按 `hash` 键    |
|builtin.communication.message | 给 Carly 发邮件：`i'm running late` <br/> 给 Angus Smith 发短信：`good luck on your exam` |    
|builtin.communication.message_category | 标记为`follow up`的新电子邮件  <br/> 标记为`high priority`的新电子邮件 |    
|builtin.communication.message_type | 发送`email`   <br/> 大声朗读我的`text` |
|builtin.communication.phone_number | 我想拨 `1-800-328-9459` <br/>     致电 `555-555-5555` |   
|builtin.communication.relationship_name | 给我的`husband`发短信 <br/>  给`family`发电子邮件| 
|builtin.communication.slot_attribute | 更改`recipient` <br/>    更改`text` | 
|builtin.comminication.source_platform | 通过`skype`呼叫他 <br/> 通过我的`personal line`呼叫他 |
|builtin.mystuff.attachment| `attached`文档 <br/> 查找 Bob 发送的电子邮件`attachment` |
|builtin.mystuff.contact_name| 查找 Lisa 发送给`me`的电子表格 <br/> 我昨晚发送给 `susan` 的文档在哪 |
|builtin.mystuff.data_source | `c:\dev\` <br/> 我的`desktop` <br/> |`"resolution": { "resolution_type": "metadataItems", "metadataType": CanonicalEntity", "value": "desktop" }`|
|builtin.mystuff.data_type | 查找我昨晚处理的`document` <br/> |`"resolution": {"resolution_type": "metadataItems", "metadataType": "CanonicalEntity", "value":Document" }` <br/> 找出 Mike 的 `visio diagram`  |
|builtin.mystuff.end_date| 向我展示我`between yesterday and today`处理的文档   <br/> 查找我 `before thursday the 31st`处理的文档 |
|builtin.mystuff.end_time| 查找我`before noon`保存的文件 <br/> 查找我`before 4pm`处理的文档|      
|builtin.mystuff.file_action| 打开我昨天`saved`的电子表格 <br/> 查找 Kevin `created`电子表格| 
|builtin.mystuff.from_contact_name | 查找 `jason` 发送给我的提议 <br/> 打开 `isaac` 最近一封电子邮件 |
|builtin.mystuff.keyword | 向我展示`french conjugation`文件 <br/> 查找我昨晚草拟的`marketing plan` |
|builtin.mystuff.location | 我`work`时编辑的文档 <br/> 我在`paris`拍摄的照片 |
|builtin.mystuff.message_category | 查找我的`new`电子邮件 <br/> 搜索`high priority`的电子邮件 |
|builtin.mystuff.message_type | 检查我的`email` <br/>  向我显示`text`  |
|builtin.mystuff.source_platform | 搜索 `hotmail` 电子邮件，查找来自 John 的邮件    <br/> 查找我通过从`work`发送的文件 |
|builtin.mystuff.start_date | 查找`january`的备注 <br/> 查找我 `after january 1st`发送给 Rob 的电子邮件 |
|builtin.mystuff.start_time | 查找`after noon`、2 点之前我发送给 Rob 的电子邮件 <br/> 查找 Kristin 发送给我、我`last night`编辑过的工作表格 | 
|builtin.mystuff.title | `c:\dev\mystuff.txt` <br/> `*.txt` |
|builtin.mystuff.transform_action | `download` John 发送给我的文件 <br/> `open`我的批注指南文档 |
|builtin.note.note_text | 创建购物清单，其中包含`pork chops, applesauce and milk` <br/> 记下要`buy milk` |
|builtin.note.title | 创建名为`grocery list`的备忘录 <br/> 创建名为`people to call`的备忘录 |
|builtin.ondevice.music_artist_name | 播放 `rufus wainwright` 的所有音乐 <br/> 播放 `garth brooks` 的音乐 |   
|builtin.ondevice.music_genre | 显示`classic rock`歌曲 <br/> 播放巴洛克时期的`classical`音乐 |
|builtin.ondevice.music_playlist | 随机播放`workout`播放列表中 Britney Spears 的所有音乐 <br/> 播放`breakup`播放列表中的音乐
|builtin.ondevice.music_song_name | 播放 `summertime` <br/> 播放 `me and bobby mcgee` | 
|builtin.ondevice.setting_type | `quiet hours` <br/> `airplane mode` |
|builtin.places.absolute_location | 带我去 `5th and pike` 的交叉口 <br/> 不，我现在想去 `1 microsoft way redmond wa 98052` |
|builtin.places.atmosphere | 查找`interesting`的地方，尽兴玩耍    <br/> 哪里可以找到一家`casual`餐厅 |  
|builtin.places.audio_device_type |通过`hands free`致电邮局 <br/> 通过`speakerphone`致电 Papa John's |    
|builtin.places.avoid_route | 避免`toll road` <br/> 告诉我怎么去旧金山可以避免 `construction on 101` |  
|builtin.places.cuisine | 山景城附近的 `halal` deli   <br/> 半岛地区的`kosher`高级餐厅 |
|builtin.places.date | 预约`next friday the 12th` <br/>  Mashiko `mondays`营业吗？ |
|builtin.places.discount_type | 查找 Macy's 的`coupon` <br/> 查找`coupon` |
|builtin.places.distance | `within 5 miles`有好的餐馆吗 <br/> 在 `within 15 miles`找一家 |   
|builtin.places.from_absolute_location | 从 `45 elm street` 到家的路 <br/> 告诉从`san francisco`到帕洛阿尔托的路  |
|builtin.places.from_place_name | 从`post office`开车到 56 Center Street <br/> 告诉我如何从`home depot`到 Lowes |
|builtin.places.from_place_type | 从市中心到`work`的路 <br/>  告诉我如何从`drug store`到家 |
|builtin.places.meal_type | 附近适合`dinner`的地方 <br/> 找个商务`lunch`的好去处 | 
|builtin.places.nearby | 向我展示我`near`一些有趣的商店  <br/> `around`有没有一些好的黎巴嫩餐厅？ |
|builtin.places.open_status | 商场什么时候`closed` <br/> 商店什么时候`opening` | 
|builtin.places.place_name | 带我去`central park` <br/> 查找`eiffel tower` |   
|builtin.places.place_type | `atms` <br/> `post office` |
|builtin.places.prefer_route | 显示`shortest`的路 <br/> 抄`fastest`道 | 
|builtin.places.price_range| 告诉我`moderately affordable`的地方 <br/>  我想去`expensive`的地方   |
|builtin.places.product | 附近哪里有`fresh fish`  <br/> 附近哪里卖`bare minerals` |
|builtin.places.public_transportation_route | `m2` 的公交车时刻表 <br/> 公交车路线 `3x` |  
|builtin.places.rating | 显示 `3 star`餐厅 <br/> 显示 `3 stars or higher`的结果  |
|builtin.places.reservation_number | 预订一个 `seven` 人的位子 <br/>  在 Il Fornaio 预订一个 `two` 人的位子 |
|builtin.places.results_number | 显示附近的 `10` 家咖啡店 <br/> 显示排在前 `3` 位的水族馆  
|builtin.places.service_provided | 我可以在哪坐公交去`whale watch`？ <br/> 我需要一名机修工来`fix my brakes` |    
|builtin.places.time | 我想去周六`8 am`开门的地方| Mashiko `now`开门了吗 |
|builtin.places.transportation_company | `new jersey transit`的火车时刻表 <br/> 我可以通过 `bart` 去那里吗 | 
|builtin.places.transportation_type | 我可以`on foot`去的音乐超市在哪里？ <br/>  告诉我如何`biking`去 Mashiko|
|builtin.places.travel_time | 我希望开车能够`less than 15 minutes` <br/>   我希望`under 15 minutes`就去到那个地方 |   
|builtin.reminder.absolute_location | 提醒我到达`chicago`后给爸爸打电话 <br/> 回到`seattle`后，提醒我加油 |
|builtin.reminder.contact_name | `bob` 电话来时，提醒我告诉他这个笑话 <br/> 创建一个提醒：与`arthur`谈话时提及校车 |
|builtin.reminder.leaving_absolute_location | 提醒离开 `1200 main` 时捎上 Craig |
|builtin.reminder.leaving_implicit_location | 提醒离开`work`时加油  |
|builtin.reminder.original_start_date | 更改有关草坪的提醒，将时间从`saturday`改到周日 <br/> 调整有关学校的提醒，将时间从`monday`改到周二   |
|builtin.reminder.relationship_name | 我的`husband`来电时，提醒我告诉他家长会的事 <br/> `mom`来电时，再次提醒我|
|builtin.reminder.reminder_text | 可否提醒我 Smith 医生来电时，`bring up my small spot of patchy skin`  <br/> 提醒我 4:40 时`pick up dry cleaning`   |
|builtin.reminder.start_date | 在`thursday after next`晚上 8 点提醒我  <br/> 在`next thursday the 18th`晚上 8 点提醒我    |
|builtin.reminder.start_time | 创建一个 `in 30 minutes`的提醒 <br/> 创建一个提醒：`this evening at 7`浇花 |  
|builtin.weather.absolute_location | `boston`会下雨吗 <br/> `seattle`的天气预报如何？  |
|builtin.weather.date_range | `this weekend`纽约的天气 <br/>   查看佛罗里达好莱坞 `five day`的天气预报 |
|builtin.weather.suitable_for | 这周末可以穿着短裤去`hiking`吗？   <br/> 今天`walk`去游戏岂不很妙？ | 
|builtin.weather.temperature_unit | 今天温度是多少 `kelvin`   <br/> 告诉我今天有多少 `celsius` |  
|builtin.weather.time_range | `tonight`看起来是不是要下雪？ <br/> `now`起风了？  |
|builtin.weather.weather_condition | 显示`precipitation` <br/> 太浩湖的`snow`现在有多厚？  |

