***Rasa安装：***
1. *教程：https://www.rasachatbot.com/1_Installation/*            
2. _安装命令：**pip install rasa-x --extra-index-url https://pypi.rasa.com/simple**
3. ***pip uninstall scikit-learn  conda install scikit-learn***
4. 在linux下：`pip install rasa-x -i https://pypi.rasa.com/simple`    instead to install the rasa-x package
5. 安装时的报错信息1：Building wheels for collected packages: ujson,
6. 解决方案：conda install json
7. 安装时的报错信息2：ERROR: Command errored out with exit status 1: python setup.py egg_inf
8. 解决方案：
>1. ___pip install --upgrade setuptools___
>2. python -m pip install --upgrade pip
9. 运行后会出现ERROR: Failed building wheel for ujson错误
>+ 解决方案：卸载ujson ，命令为conda uninstall ujson
>+ 重新安装使用：***pip install rasa-x --extra-index-url https://pypi.rasa.com/simple***
>+ 依然报错：
 ![alt 错误信息1](https://roundxin.github.io/images/image.png)
>+ 解决方案：https://visualstudio.microsoft.com/zh-hans/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16后，选择visual c++ 14.0后，再次运行pip install --upgrade setuptools 
>+ python -m pip install --upgrade pip后安装成功！satisfied!!!
错误二：`RemoveError: 'setuptools' is a dependency of conda and cannot be removed from conda's operating environment.`
>- 解决：`conda update --force conda`   
> 将conda-forge添加到搜索路径上》：conda config --append channels conda-forge
>>- conda update -n base -c defaults conda
>>- conda install setuptools-62.3.2
>>- conda install ujson
>>- pip install rasa-x --extra-index-url https://pypi.rasa.com/simple
>>- pip install ujson-1.35-cp37-cp37m-win_amd64.whl（需要自己下载）
>>- pip install rasa
```
1. 如何定义问题和对应的意图
>+ 在nlu中意图为faq/***后,ResponseSelector训练数据中的意图是group/intent的格式，需要在data/responses.yml中写入utter_faq/***普通意图intent，需要在domain中写入utter_***

rasa run -m models --enable-api --cors "*" –debug
vpn对rasa模型训练有影响。
Rasa运行：
1.	运行动作服务器：rasa run actions
2.	运行Rasa服务器和客户端：rasa shell
3.	运行rasa服务器：rasa run –cors “*”
4.	运行网页客户端：Python -m http.server
```

### 系统案例：
***报时机器人***
>+	Rasa core是rasa体系中负责对话管理的部分，用于记录对话过程和选择下一个动作。机器学习驱动的对话管理引擎。
>+	Domain:定义了对话机器人需要知道的所有信息，如意图intent、实体entity、词槽slot、动作action、表单from、回复response，意图和实体表示输入的范围。词槽和表单相当于内部的变量，用表征状态存储记忆。动作给定了模型的输出范围。回复字段为机器的回复模板，既为简单有为复杂动作的自然语言生成NLG步骤。Templetes是模板
1.	意图和实体字段列表和Rasa NLU数据保持一致
>+	Action为对话管理模型的输出。既机器人的动作，容易填表、回复消息、调用外部api等。
>+	以utter_开头的动作表示渲染同名的模板并发给用户。这是一种约定。
>+	词槽slot：定义了机器人在对话中需要跟踪（记忆）的信息。
>+	回复Response定义了机器人回复的模板。{name}为模板变量，多个模板时，将随机选择一个。可通过类似于dispatcher.utter_message(template=’utter_greet’,name=’Silly’)来给模板变量赋值。
>+	Rasa支持富文本(rich response)，如按钮、图像，但需要即时通信软件的支持。
>+	不同的通道具有不同的输出，如果一个回复有多个模板，可以使用通道channel去相应特定的通道。
>+	Rasa也提供了custom字段，方便开发者自定义复杂的相应内容。但需要客户端的支持。
>+	会话Session是用户和机器人之间的一场对话，一个对话可能横跨很多轮对话，目前支持的会话配置为：session_expiration_time过期时间和carry_over_slots_to_new_session当新的会话开始时，是否应该将上一个会话的词槽延续(继承)到新的会话。
>+	Resa的全局性配置选项，store_entities_as_slots，当得到NLU结果时，是否同步更新同名词槽，默认为true.
>+	Rasa是通过学习故事story来学习对话管理知识的。故事是一种在教高语义层次上记录对话过程的方式，记录对话国曾，不仅要记录用户的语义表达，且还需记录系统内部正确的状态变化。每个故事都是stories列表。Story故事的备注，steps对应键的内容。
>+	用户消息：用户消息保存了用户的意图和实体信息。
>+	机器人动作名：-action:action_ask_howcanhelp, action_ask_howcanhelp为机器人的动作名。对于复杂的故事，可能存在用户一次请求后rasa连续返回多次动作。
>+	动作返回事件：当使用自定义事件时，由于Rasa无法再训练阶段确定这样的自定义动作会给对话状态带来什么样的改变，因此需要手动给出动作的状态，即为事件。常用事件包括槽事件和active_loop事件。
>+	符号辅助：检查点(checkpoint)是用来减少故事中的重复部分，名字相同的检查点可以相互跳转。一个故事结束时的检查电脑可以和另一个故事开始时的名字相同的检查点连接形成新的故事。
>+	Or语句：故事若仅仅是某个节点上存在不同，如果为了这小小的不同而写两个故事，会给后续维护带来很大的开销。所以用or来精简故事。
>+	动作action:接收用户输入和对话状态信息，按照业务逻辑进行处理，并输出改变对话状态的事件和回复用户的信息。
>+	回复动作Response：自动查找回复中同名的模板并渲染。且必须要和回复模板保持名字相同。这类动作需要用utter_开头。
>+	表单：任务型对话是收集任务所需要的要素需多次和用户交互，直到所需的要素手机完整。即填表过程。
>+	默认动作：
>>1.	action_listen:停止预测动作，等待用户输入.
>>2.	Action_restart:重启对话过程，清理对话历史和词槽，用户可以通过在客户端中输入\restart来执行此动作。
>>3.	Action_session_start…
2.	默认动作可以被同名的自定义动作代替。
>1.	词槽：一个词槽必须有名字和类型。
>>1.	词槽的行为：influence_conversation来设置该词槽对对话过程是否有影响。默认为true。若为false,则该词槽仅用于存储信息，不会影响对话行为。
>>2.	词槽类型：text、bool、category、float、list、Any
>>3.	词槽的映射mapping:一个词槽可以同时有多个映射，在运行时会按照从上到下的顺序一次执行。每一个映射中，type字段给出了这个映射的类型，其余的都是这个类型的参数，和type字段密切相关。
>>4.	词槽初始化：词槽可以设置可选的初始值initial_value:”human”。
>2.	策略policy：负责学习故事，从而预测动作。需要通过特征提取组件featurizer将故事转换成对话状态，进而得到对话状态特征，rasa可以拥有多个策略，这些策略可独立进行训练和预测，最后通过优先级及预测得分共同决策。策略由多个列表构成。每个列表元素都是一个字典，字典包含name和配置选项，name指定组件名，其他为配置选项。
>>3.	内建的策略：
```text
i.	TEDPolicy:对话预测算法，基于transformer的方案将会话映射成一个对话向量，找和这个向量最近的已知动作的对话向量。
ii.	MemoizationPolicy:用于记住历史中出现的状态和对应的动作，把这种关系做成字典。在预测时，直接返回value，否则预测失败。
iii.	AugmentedMemoizationPolicy:和Memoization相似，但有个遗忘机制，会随机的遗忘当前对话历史中的部分步骤，随后在训练的故事机中寻找和当前历史匹配的故事。
dd)	策略优先级： 每个策略独立预测下一个动作后，会使用得分最高的动作。策略之间是有优先级的，优先级越高，策略越优先。FormPolicy:6、MemoizationPolicy和AugmentedMemoizationPolicy为3，TEDPolicy为1。
ee)	数据增强：rasa可把故事首尾相接生成新的故事，这是故事的数据增强。也可以在rasa命令时，添加—augmentation来设定数据增强的数量，rasa按照最多生辰—argumentation设置乘10来增强故事。也可以完全关闭argumentation参数，不进行增强。
ff)	端点endpoints.yml定义了Rasa Core和其他服务的连接配置，该信息称为端点endpoint。目前端点有event broker、tracker store、locak store、动作服务器action server、NLU服务器、NLG服务器和model storage。动作服务器action server和NLG服务器有默认值，单机时无需配置。
gg)	Rasa SDK和自定义动作：自定义动作是实现具体业务逻辑的入口和载体。
i.	安装：rasa包含rasa SDK，即安装了rasa也就安装了Rasa SDK。若在生产环境下只想安装SDK可用：pip install rasa-sdk
ii.	自定义动作：需要继承sdk类，这样服务器就能自动发现并注册动作。Tracker代表对话状态追踪，即历史记忆，可以通过获取tracker对象获取当前或历史的对话状态(实体情况和词槽等)，这通常作为业务的输入。
iii.	Tracker的属性
1.	Sender_id:（str）用户的唯一ID
2.	Slots:（list）词槽的列表
3.	Latest_message:（dict）包含三个键：intent、entities和text，分别代表意图、实体、用户的话。
4.	Events:代表历史上所有的事件。
5.	Action_form:（str）表示当前被激活的表单，也可鞥为空即没有表单被激活
6.	Latest_action_name:（str）：表示最后一个动作的名字。
iv.	Tracker对象的方法：
v.	Current_state()：返回当前的tracker对象
vi.	Is_paused():返回当前的tracker对象的过程是否被暂停
vii.	Get_latest_entity_values()：返回某个实体的最后值
viii.	Get_latest_input_channel():返回最后用户所用的通道(input_channel)的名字
ix.	Events_after_latest_restart():f返回最后一个重启后的所有事件
x.	Get_slot():返回一个词槽的具体值。返回一个词槽的具体值。
hh)	事件对象event
i.	通用事件对象
1.	Slotset(key,value=None)：要求系统将名字为key的词槽的值设置为value
2.	Restarted():重启对话过程
3.	AllSlotReset():重置所有的词槽
4.	ReminderScheduled():在指定的时间发起一个意图和实体都给定的请求，也称为定时任务。
5.	ReminderCancelled():取消一个定时任务
6.	ConversaionPaused():暂停对话过程
7.	ConversationResumed()”继续对话过程。
8.	FollowupAction(name):强制设定下一轮的动作（不通过预测得到）
ii.	Rasa自动跟踪(由系统创建的)的事件
1.	UserUttered():表示用户发送的信息
2.	BotUttered():表示机器人发送的消息
3.	UserUtteranceReverted():侧小用户最后消息UserUttered后的所有发生的事件和事件本身，通常这样之后只剩下action_listen,回到待用户输入状态。
4.	ActionExecuted():撤销上一个动作，清除上个动作所有的事件效果，机器人会重新开始预测下个动作。
5.	actionExecuted():记录一个动作,动作创建的事件会被单独记录
6.	SessionStarted():开始一个新的对话会话。重置tracker，并触发执行ActionSessionStart(默认情况下会将slotSet拷贝到新的会话)
iii.	运行自定义动作
1.	若安装的不是单独的sdk而是rasa则运行:Rasa run actions
2.	若安装的是sdk则运行:python -m rasa_sdk –actions actions
ii)	Rasa支持客户端
i.	即时通信instant messaging ,IM
1.	Facebook messenger,slack,telegram,Twilio,Microsoft Bot Framework、Cisco Webex Teams、rocketChat,Mattermost和Google Hangouts Chat…,rasa的有rasa webchat和chatroom
2.	负责各IM连接的组件为connector,负责实现通信协议，支持多个connector,在credentials.yml文件中配置客户端连接即可。Rasa WebChat的底层协议是socketio，所以配置里使用的是socketio.
2.	FAQ（Frequently Asked Question）闲聊功能:使用组件ResponseSelector来做FAQ型或闲聊型的任务。使用responseSelector，需要为每种问题定义一个分类，并为每种分类定了以想要返回给用户的答案。
a)	定义问题和对应的意图
i.	ReponseSelector采用的是grop/intent的格式命名。注：普通意图不能包含”/”
ii.	Grop为检索意图retrieval intent
b)	定义问题的答案
i.	ResponseSelector中使用domain.yml的reponses字段来存放答案数据。Rasa中意图为intent的问题需要一个名为utter_intent的response作为答案。
c)	训练rasa
d)	为了根据问题智能具有良好的泛化性，需要responseSelector在现有数据上进行训练。只需将responseSlector组件加入NLU的流水线上即可。
e)	经过训练的ResponseSelector组件能够根据用户的语义正确地进行语义分类。需要启动RelePolicy
f)	还需要设置一个规则rule,将问题分类映射到对应的动作上。当rasa服务运行时，ReluPolicy的自动触发机制将会保证在NLU正确识别成检索意图时，自动执行对应的动作。
g)	FAQ机器人功能分为业务无关功能和业务相关功能。
3.	基于规则的对话管理
a)	Fallback:用于兜底，确保即使出错也能很有丫的用对不起，我没明白您的意思之类的文本回复给用户，根据不同的原因，fallback可以分为NLU fallback和策略fallback。
i.	NLU fallback负责NLU阶段理解用户意图困难或模糊的情况，可用FallbackClassifier组件。（最高置信度不大于或等于0.6，最高的前两个意图得分之差不超过0.1时NLU的意图 就会被替换为nlu fallback）
ii.	策略fallback:在预测下一个动作时，如果预测结果的置信度不高或最高的两个动作差很低，就需要fallback。
b)	意图触发动作
i.	Rasa允许通过类似/intent{‘’entity1：vall,entity2：val2}的快捷方式表示意图和实体。RelePolicy为action_restart,action_back,action_session_start这三个会话级别提供了意图restart,back,session_start并建立好了意图到动作的映射。可通过/restart,/back,/session_start来表达意图。
ii.	自定义意图触发动作需要用到ReluPolicy功能，在stories.yml中定义。
c)	表单：
i.	引导用户填写表单。为了使Rasa正确地进行基于表单的对话，需要将ReluPolicy加入配置文件。
ii.	Action_loop和表单同名，会进入对应的填槽-询问的循环过程。
iii.	执行表单：当表单要求满足后，就可执行表单任务了。
```
### rasa代码编写注意事项：
> domain.yml文件是存放rasa机器人的回答，作用于stories.yml文件的故事中的action所对应的行为。

> nlu.yml文件是对用户的回答进行匹配，匹配到对应意图intent后，根据stories中的步骤继续对话。

> rasa的actions运行：python -m rasa_sdk --actions actions

> rasa中文文档：https://www.rasachatbot.com/

>- 特别注意：nul文件里的内容不能有;,否则，模型训练不起来。
>- 实体要规范,不能有特殊字符，否则就作为类别吧。
 --- 
若需要进行词槽的存储，可以在domain中写词槽：
```python
slots:
  detail:
    type: text
    influence_conversation: false
    mappings:
      - type: from_entity
        entity: detail
  reason:
    type: text
    influence_conversation: false
    mappings:
      - type: from_entity
        entity: detail
```
 --- 
- 在action的run方法中中写入词槽：`return [SlotSet('detail', detail if detail else [])]`
- 获取词槽的方式：`tracker.get_slot('detail')`
- rules中可以定义规则，在用户输入时匹配到对应规则则返回相应的动作，**一个规则只能写一个动作**。




