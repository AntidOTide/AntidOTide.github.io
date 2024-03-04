---
title: 基于LangChain框架的GPT开发
date: 2024-02-17 01:25:23
tags: python langchain
---

# Langchain框架
LangChain 是一个开发由语言模型驱动的应用程序的框架。使用Langchain框架可以更高效的对大语言模型进行开发。本博客会详细介绍如何使用Langchain开发一个基于ChatGPT模型的智能音箱。
智能音响功能的实现可以分为四个步骤：唤醒词检测，语音转文字输入，逻辑处理，文字转语音输出。在第一，二，四，三个步骤中，有非常多的现成方法可以实现，比如语音转文字可以使用百度语音识别API，阿里语音识别API等，语音输出也可以使用tts，VITS所以这些与逻辑处理相比锦上添花的模块，各位可以自行寻找合适的的方式解决，当然我也会列出我自己实现的方式，本章着重里详解逻辑处理模块（怎么跟tm写论文一样）
Langchain中有一个模块叫Agent代理，如果想让ChatGPT理解我们的指令并加以行动是几乎不可能的，但使用Agent创建代理后就可以使GPT拥有路由功能，可以通过我们的的输入来自行判断需要执行的函数并得到输出。
### 1 导入Langchain模块
```
pip install langchain
pip install langchain_core
pip install langchain_openai
```
### 2 在Langchain中实现与GPT的交流
Langchain支持大多数的语言模型，本章使用了GPT-3.5-trubo进行示例，首先需要使用os将OpenAI的API配置成模块环境变量,
```python
import os
os.environ['OPENAI_API_KEY'] = "sk-1145141919810"
```
如果大家使用的是第三方的API也可以将API BASE加入模块环境变量
```python
os.environ['OPENAI_API_BASE'] = 'https://114514/v1'
```
配置完后可以先测试是否能够正确的连接到GPT
```python
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-3.5-turbo", temperature=0.5)
text = "你好，请问你是谁"
print(llm.invoke(text))
```

输出结果
```
content='你好！我是一个AI助手，我没有名字，你可以叫我OpenAI。有什么我可以帮助你的吗？'

进程已结束，退出代码为 0
```
成功打印出结果就已经可以正常使用gpt了，大家可以根据自己的需求更改模型等其余参数
### 3 创建提示词   
现在我们来使用`langchain_core`的`prompts`来创建提示词，使GPT知道自己的身份是Agent
```python
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder

prompt = ChatPromptTemplate.from_messages(
    [
        (
            "system",
            "You are very powerful assistant",
        ),
        ("user", "{input}"),
        MessagesPlaceholder(variable_name="agent_scratchpad"),
    ]
)
```
在使用OpenAI的模型时，OpenAI已经对工具的使用做出了微调和优化，此时我们只需考虑两个参数`input`和`agent_scratchpad`，而不需要考虑其他的参数。
### 4 创建工具
工具Tool是Agent中最重要的部分，它可以让LLM理解我们的指令并转化为可执行的函数，在创建Tool之前，先引入`langchain.agents`中的`tool`模块来使用tool装饰器，下面编写一个简易的获取系统本地时间的函数。
```python
import datetme
from langchain.agents import tool

@tool
def get_time(query: str) -> str:
    """Get the local date."""
    current_date = datetime.datetime.now().strftime("%Y-%m-%d")
    current_time = datetime.datetime.now().strftime("%H:%M:%S")
    return '今天的日期是' + current_date + '今天的时间是' + current_time
```
在定义工具时，函数下方必须要写上注释，有明确清晰的注释，LLM才知道它是什么类型的函数从而更好的使用函数，使用invoke方法可以测试这个函数是否正常运行
```python
print(get_time.invoke('114514'))
```
输出结果
```
今天的日期是2024-02-17今天的时间是21:15:29

进程已结束，退出代码为 0
```
定义完工具后，定义一个列表tools来存放定义好的工具
```python
tools = [get_time]
```
之后将存放好工具的绑定在LLM上
```python
llm_with_tools = llm.bind_tools(tools)
```
下一步按照Langchain官方的示例代码创建Agent
```python
from langchain.agents.format_scratchpad.openai_tools import (
    format_to_openai_tool_messages,
)
from langchain.agents.output_parsers.openai_tools import OpenAIToolsAgentOutputParser

agent = (
    {
        "input": lambda x: x["input"],
        "agent_scratchpad": lambda x: format_to_openai_tool_messages(
            x["intermediate_steps"]
        ),
    }
    | prompt
    | llm_with_tools
    | OpenAIToolsAgentOutputParser()
)
```
之后使用`AgentExecutor`来执行Agent代理
```python
from langchain.agents import AgentExecutor

agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)
```
运行agent
```python
list(agent_executor.stream({"input": "How many letters in the word eudca"}))
```
运行结果

```
> Entering new AgentExecutor chain...

Invoking: `get_time` with `{'query': 'tomorrow'}`


今天的日期是2024-02-17今天的时间是21:27:22明天是2024年2月18日。

> Finished chain.

进程已结束，退出代码为 0
```