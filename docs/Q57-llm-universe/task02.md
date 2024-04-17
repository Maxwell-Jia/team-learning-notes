# Task02: 使用LLM API开发应用

## 基本概念

**Prompt：**我们每一次访问大模型的输入为一个 Prompt，而大模型给我们的返回结果则被称为 Completion。

**Temperature：**控制 LLM 生成结果的随机性与创造性。当取值较低接近 0 时，预测的随机性会较低，产生更保守、可预测的文本；当取值较高接近 1 时，预测的随机性会较高，会产生更有创意、多样化的文本。

**System Prompt：**一般设置 System Prompt 来对模型进行一些初始化设定，例如，我们可以在 System Prompt 中给模型设定我们希望它具备的人设如一个个人知识库助手等。

## 使用 LLM API

我将使用第三方提供的 openai api 进行后续的开发。下面是参考 datawhale 课程代码，测试了简单的 api 调用。这里仅对 openai 的 api key 进行测试，其余国产模型未作尝试。

```python

from openai import OpenAI

client = OpenAI(
    # 生成的 openai api key
    api_key="sk-xxxxxxx",
    # 我的 api 由第三方提供，需要用下面的链接替换 openai 的官方链接
    base_url="https://api.aiproxy.io/v1"
)

def gen_gpt_messages(prompt):
    '''
    构造 GPT 模型请求参数 messages
    
    请求参数：
        prompt: 对应的用户提示词
    '''
    messages = [{"role": "user", "content": prompt}]
    return messages


def get_completion(prompt, model="gpt-3.5-turbo", temperature = 0):
    '''
    获取 GPT 模型调用结果

    请求参数：
        prompt: 对应的提示词
        model: 调用的模型，默认为 gpt-3.5-turbo，也可以按需选择 gpt-4 等其他模型
        temperature: 模型输出的温度系数，控制输出的随机程度，取值范围是 0~2。温度系数越低，输出内容越一致。
    '''
    response = client.chat.completions.create(
        model=model,
        messages=gen_gpt_messages(prompt),
        temperature=temperature,
    )
    if len(response.choices) > 0:
        return response.choices[0].message.content
    return "generate answer error"
```

调用以下函数：

```python
get_completion("Who are you")
```

输出如下：

```
'I am an AI digital assistant designed to help answer questions and provide information to the best of my abilities. How can I assist you today?'
```