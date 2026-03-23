```mermaid
graph TD
    %% 1. 前端交互层
    subgraph Client ["前端：微信小程序"]
        UI["对话式 UI (文本/语音/计划展示)"]
        Cam["拍照上传 (报告/病例照片)"]
    end

    %% 2. 后端中枢 (Java / Spring AI)
    subgraph Backend ["后端中枢: 业务逻辑与编排"]
        Gate["Rest Controller (API 接口)"]
        
        %% Orchestrator
        subgraph Orchestrator ["AI 任务编排器 (Spring AI)"]
            ChatClient["ChatClient (会话上下文追踪)"]
            
            subgraph InfoCheck ["信息完整性检查 (Advisor)"]
                CheckLogic{"检查档案：<br/>是否缺少主观数据?<br/>(从结构化数据读取)"}
                AwaitingInput["生成 UI_Schema:<br/>(追问/选择题/滑块)"]
                InformationComplete["状态: 信息完整"]
            end
            
            Strategy["Strategy Pattern (业务逻辑分发)"]
        end

        %% 存储层 (去技术名，强调内容)
        subgraph Storage ["动态健康档案存储"]
            OCR_Tool["医疗 OCR 逻辑 (本地大模型驱动)"]
            StructuredDB[("结构化数据: <br/>用户信息/指标数值/计划打卡")]
            VectorDB[("语义知识库: <br/>功能医学教材/症状描述记忆")]
            FileStore[("原始文件: <br/>报告原图/加密影像")]
        end
    end

    %% 3. AI 推理层
    subgraph AICore ["AI 推理层 (模型适配)"]
        RAG["知识检索增强 (ETL 匹配)"]
        CloudModel["云端大模型 (逻辑推理/方案规划)"]
        LocalModel["本地大模型 (隐私脱敏/OCR理解)"]
    end

    %% 4. 落地协商层
    subgraph Negotiation ["动态协商引擎"]
        DraftPlan["生成康养计划初稿"]
        Loop{"协商循环 (用户反馈反馈)"}
    end

    %% 逻辑流转
    UI --> Gate
    Cam --> OCR_Tool
    OCR_Tool -- "调用本地模型解析" --> LocalModel
    OCR_Tool -- "存储识别结果" --> StructuredDB
    Gate --> ChatClient
    
    %% 档案汇聚
    ChatClient -. 访问 .-> StructuredDB
    ChatClient -. 访问 .-> VectorDB
    
    %% 信息检查环节
    ChatClient --> InfoCheck
    InfoCheck --> CheckLogic
    
    %% 分支 A: 信息缺失
    CheckLogic -- "是 (如缺少压力自评)" --> AwaitingInput
    AwaitingInput --> Strategy
    Strategy -- "调用云端生成追问" --> CloudModel
    CloudModel -- "返回组件化追问" --> UI
    
    %% 分支 B: 信息完整
    CheckLogic -- "否" --> InformationComplete
    InformationComplete --> Strategy
    Strategy -- "RAG 增强" --> RAG
    Strategy -- "本地脱敏" --> LocalModel
    
    %% 最终生成
    RAG --> CloudModel
    LocalModel --> CloudModel
    CloudModel --> DraftPlan
    DraftPlan --> Loop
    Loop -- "用户点击: 做不到" --> UI
```
