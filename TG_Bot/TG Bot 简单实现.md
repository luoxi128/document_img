# TG Bot 简单实现



### 创建一个TG Bot

在搜索栏中搜索___BotFather___ ,在聊天框中输入`/new_bot` 进行机器人创建。根据提示输入机器人的名字以及用户名即可创建成功（用户名可能会多次重复，需要耐心起名字）。最后___BotFather___ 会提供一个机器人的链接以及API Token，要注意token安全。 

![Bot创建过程](/assets/img/new_bot.jpg "Bot创建过程")



### 推送消息

从https://core.telegram.org/bots/api中*Available methods*章节查阅相关的方法进行消息推送。

> All methods in the Bot API are case-insensitive. We support **GET** and **POST** HTTP methods. Use either [URL query string](https://en.wikipedia.org/wiki/Query_string) or *application/json* or *application/x-www-form-urlencoded* or *multipart/form-data* for passing parameters in Bot API requests.
> On successful call, a JSON-object containing the result will be returned.

从这里看出我们可以简单地使用HTTP POST方法上传一个JSON格式的请求，服务器将会返回一个JSON格式的结果。查阅*SendMessage* 章节中，可以查询到必要或可选的字段。要推送一段文本只有两个必选的字段Chat_id以及Text。Text即是我们想要推送的内容，这个由用户准备，Chat_id是用于表示要推送的地方，官方文档仅给出推送到channel的简单示例，下面会简单讲一下如何推送到聊天里。



### 获取Chat_id

TG中的机器人可以加入群聊或者channel中，类似用户一样的存在，发送消息或相应消息。channelusername是可以唯一标识一个channel的，所以在推送消息时只要传入@channelusername即可将消息推送到channel中；类似的，TG中每一个群聊也是由一个Chat_id唯一标识，只要能够获取到这个唯一标识，即可往该群聊推送消息。

##### 第一步 将机器人拉入群聊

在群聊的成员管理中选择添加成员，搜索刚刚建好的机器人，将其加入群聊。然后在群聊里发送`/my_id` 并且@我们刚拉进群的机器人。

![获取chat_id](/assets/img/get_chat_id.jpg "获取chat_id")



##### 第二步 通过getupdates方法获取chat_id

Chat_id不会在群聊中回复，需要在浏览器中打开https://api.telegram.org/botXXXX:YYYY/getUpdates (将XXXX:YYYY替换为***BotFather*** 给的API Token)，服务器会返回一个JSON格式的回复，从中找到chat中的id字段，即为我们需要的Chat_id。

![chat_id的json](/assets/img/get_chat_id_json.jpg)



### demo测试

经过刚刚的一系列步骤，我们已经获取了我们所需的所有东西，下面使用python进行一个最简单的demo测试，将我们的文本推送到群聊中。

> ```python
> import requests
> 
> # 创建JSON消息体
> message = {
>     "chat_id": "-706785147",
>     "text": "Hello World!!!"
> }
> 
> # 推送到群聊
> tg_bot_api = 'https://api.telegram.org/botYour:Api_token/sendMessage'
> proxy = {"https": "127.0.0.1:7890"}
> res = requests.post(url=tg_bot_api, json=message, proxies=proxy)
> print(res.content)
> ```

其中的proxy是因为特殊原因需要自行设置代理类型。当res "OK"字段返回true，群聊中能够正确推送内容，这次的TG Bot就搭建成功了！！！

![hello world](/assets/img/helloworld.jpg)





### 结语

本次Bot搭建只是TG机器人最简单的用法，但是也能够用来晚点新花样，例如：集成天气预报、每日一句、倒计时等功能，制作成为一个每天播报的机器人。

有空也可以研究一下超文本、交互等功能，制作一个更精良的机器人











