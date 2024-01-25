### 提示管理器（Prompt Manager）
管理多智能体链路中的prompt创建
- 快速配置：采用预设的处理函数，用户仅需通过定义智能体的输入输出即可轻松配置，实现多智能体的prompt快速组装和配置。
- 自定义支持：允许用户自定义prompt内部各模块的处理逻辑，以达到个性化的智能体prompt实现。

### Prompt预设模板结构

- Agent Profile：此部分涉及到智能体的基础描述，包括但不限于代理的类型、功能和指令集。用户可以在这里设置智能体的基本属性，确保其行为与预期相符。
- Context：上下文信息，给智能体做参考，帮助智能体更好的进行决策。
  - Tool Information：此部分为智能体提供了一套可用工具的清单，智能体可以根据当前的场景需求从中挑选合适的工具以辅助其执行任务。
  - Reference Documents：这里可以包含代理参考使用的文档或代码片段，以便于它在处理请求时能够参照相关资料。
  - Session Records：在进行多轮对话时，此部分会记录之前的交谈内容，确保智能体能够在上下文中保持连贯性。
- Response Output Format：用户可以在此设置智能体的输出格式，以确保生成的响应满足特定的格式要求，包括结构、语法等。
- Response：在与智能体的对话中，如果用户希望智能体继续某个话题或内容，可以在此模块中输入续写的上文。例如，在运用REACT模式时，可以在此区域内详细阐述智能体先前的行为和观察结果，以便于智能体构建连贯的后续响应。

### Prompt自定义配置

#### Prompt模块参数
- field_name：唯一的字段名称标识，必须提供。
- function：指定如何处理输入数据的函数，必须提供。
- title：定义模块的标题。若未提供，将自动生成一个标题，该标题通过把字段名称中的下划线替换为空格并将每个单词的首字母大写来构建。
- description：提供模块的简要描述，位于模块最上方（标题下方）。默认为空，可选填。
- is_context：标识该字段是否属于上下文模块的一部分。默认为True，意味着除非显式指定为False，否则都被视为上下文的一部分。
- omit_if_empty：设定当模块内容为空时，是否在prompt中省略该模块，即不显示相应的模板标题和内容。默认为False，意味着即使内容为空也会显示标题。如果希望内容为空时省略模块，需显式设置为True。

#### Prompt配置示例

Prompt配置由一系列定义prompt模块的字典组成，这些模块将根据指定的参数和功能来处理输入数据并组织成一个完整的prompt。

在配置中，每个字典代表一个模块，其中包含相关的参数如 field_name, function_name, is_context, title, description, 和 omit_if_empty，用以控制模块的行为和呈现方式。

context_placeholder 字段用于标识上下文模板的位置，允许在prompt中插入动态内容。
```
[
    {"field_name": 'agent_profile', "function_name": 'handle_agent_profile', "is_context": False},
    {"field_name": 'context_placeholder', "function_name": '', "is_context": True},
    {"field_name": 'tool_information',"function_name": 'handle_tool_data', "is_context": True},
    {"field_name": 'reference_documents', "function_name": 'handle_doc_info'},
    {"field_name": 'session_records', "function_name": 'handle_session_records'},
    {"field_name": 'task_records', "function_name": 'handle_task_records'},
    {"field_name": 'output_format', "function_name": 'handle_output_format', 'title': 'Response Output Format', "is_context": False},
    {"field_name": 'response', "function_name": 'handle_response', "title"="begin!!!", "is_context": False, "omit_if_empty": False}
]
```

### 未来规划

#### Prompt配置简化

未来的Prompt配置简化旨在降低用户面对复杂配置的难度。通过引入更直观的配置方法，我们计划使得Prompt配置不仅对高级用户友好，还能让初学者轻松上手。简化计划可能包括：

- 预设配置短语：将复杂的配置字典转换为简洁的短语，每个短语都预定义了一个Prompt模块。用户将能够使用简单的字符串指令来快速配置Prompt，而无需深入了解所有参数。
- 配置校验和建议：增加配置的即时校验，如果检测到配置错误或不一致性，自动提供修改建议，帮助用户优化Prompt结构。

#### 动作（Action）注册的改进计划

在现行系统中，智能体必须在其角色提示（role prompt）内定义所有的动作（actions）。这意味着智能体需要同时处理动作的意图识别和生成动作所需的输入数据，这一过程对语言模型的理解和推理能力提出了更高要求。

为了优化这一流程，我们打算在后续版本中对动作的输入生成和执行进行模块化。这将使智能体的工作重点转移至判断当前情境下应执行哪些动作，而不必负责具体的操作指令。在这种新的架构下，当需要执行某个动作时，将有专门的机制负责生成相应动作的具体输入指令。

这种分离将显著降低单个模块的复杂性，使得整个系统更加灵活、易于扩展，同时也提升了动作执行的效率和准确性。