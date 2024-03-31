导入日期时间
导入异步
从revChatGPT.V1导入AsyncChatbot作为V1Chatbot
从配置导入OpenAIAuthBase
从utils导入QueueInfo


类ChatGPTBrowserChatbot(asyncio.Lock)：
ID=0

    帐户：OpenAIAuthBase

    bot:V1Chatbot

    模式：str

    队列：QueueInfo

    unused_conversions_pools={}

    access_at=[]
    """访问时间，仅保存一小时以内的时间"""

    last_rate_limited=无
    """上一次遇到限流的时间"""

    Def__init__(self、bot、mode)：
    self.bot=bot
    self.mode=模式
    self.queue=QueueInfo()
    Super().__init__()

    Def update_access_at(自身)：
    """更新最后一次请求的时间，用于流量统计"""
    current_time=datetime.datetime.now()
    self.accessed_at.append(当前时间)
    self.refresh_access_at()

    异步定义重命名对话(self，对话ID:str，标题：str)：
    等待self.bot.change_title(conversation_id，title)

    Def refresh_access_at(自身)：
    # 删除栈顶过期的信息
    current_time=datetime.datetime.now()
    当len(self.accessed_at)>0且current_time-self.accessed_at[0]>datetime.timedelta(hours=1)时：
    self.accessed_at.pop(0)
    如果len(self.accessed_at)==0：
    self.accessed_at.append(当前时间)

    异步定义delete_conversation(self，conversation_id)：
    等待自己.Bot.delete_conversation(conversation_id)

    异步定义询问(self，prompt，conversation_id=None，parent_id=None，model=“”)：
    """向ChatGPT发送提问"""
    #self.queue已交给MiddlewareConcurrentLock处理，此处不处理
    self.bot.conversation_id=conversation_id
    self.bot.parent_id=parent_id
    self.bot.config['model']=model
    self.Bot.ask中r的异步(提示=提示，conversation_id=conversation_id，parent_id=parent_id)：
    产量r
    self.update_access_at()

    Def__str__(self)->str：
    return self.bot.__str__()
