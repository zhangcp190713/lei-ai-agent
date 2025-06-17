# Lei AI Agent API 使用指南

## 简介

Lei AI Agent 是一个基于 Spring Boot 3 和 Spring AI 构建的智能旅游助手系统，采用了 RAG (Retrieval-Augmented Generation)、Tool Calling 和 MCP (Multiple Conversation Paths) 等先进技术，为用户提供个性化的旅游规划、信息查询和智能对话服务。

## API 使用说明

### 智能体API

#### 1. 执行智能体任务（同步模式）

- **接口地址**：`POST /api/agent/execute`
- **描述**：使用LiManus智能体处理用户请求，同步返回执行结果
- **参数**：
  - `prompt`：用户请求内容，必填
- **示例请求**：
  ```
  POST /api/agent/execute?prompt=帮我规划一次北京三日游
  ```

#### 2. 执行智能体任务（高级同步模式）

- **接口地址**：`POST /api/agent/execute/advanced`
- **描述**：使用LiManus智能体处理复杂请求，支持会话、系统提示和步骤控制
- **请求体**：
  ```json
  {
    "prompt": "帮我规划一次北京三日游",
    "sessionId": "user123-session456",
    "systemPrompt": "你是一个专业的旅游规划师",
    "maxSteps": 10
  }
  ```

#### 3. 执行智能体任务（流式模式）

- **接口地址**：`GET /api/agent/stream`
- **描述**：使用LiManus智能体处理用户请求，通过SSE流式返回执行过程和结果
- **参数**：
  - `prompt`：用户请求内容，必填
- **示例请求**：
  ```
  GET /api/agent/stream?prompt=帮我规划一次北京三日游
  ```

#### 4. 执行智能体任务（高级流式模式）

- **接口地址**：`POST /api/agent/stream/advanced`
- **描述**：使用LiManus智能体处理复杂请求，支持会话、系统提示和步骤控制，通过SSE流式返回执行过程和结果
- **请求体**：
  ```json
  {
    "prompt": "帮我规划一次北京三日游",
    "sessionId": "user123-session456",
    "systemPrompt": "你是一个专业的旅游规划师",
    "maxSteps": 10
  }
  ```

#### 5. 获取智能体状态

- **接口地址**：`GET /api/agent/status`
- **描述**：获取LiManus智能体的当前状态和配置信息
- **示例请求**：
  ```
  GET /api/agent/status
  ```

#### 6. 重置智能体状态

- **接口地址**：`POST /api/agent/reset`
- **描述**：将LiManus智能体重置到初始状态，清除所有当前上下文
- **示例请求**：
  ```
  POST /api/agent/reset
  ```

#### 7. 关闭流式连接

- **接口地址**：`POST /api/agent/stream/close/{sessionId}`
- **描述**：手动关闭指定会话ID的SSE流式连接
- **参数**：
  - `sessionId`：会话ID，路径参数
- **示例请求**：
  ```
  POST /api/agent/stream/close/user123-session456
  ```

## 统一响应格式

所有API返回统一的响应格式：

```json
{
  "status": "success",
  "code": "200",
  "message": "操作成功",
  "data": {
    // 实际返回数据
  },
  "timestamp": "2023-06-01T12:34:56.789Z",
  "path": "/api/agent/execute",
  "success": true
}
```

- `status`：响应状态，可能值为 success（成功）、error（错误）、warning（警告）
- `code`：响应码
- `message`：响应消息
- `data`：响应数据
- `timestamp`：响应时间戳
- `path`：请求路径
- `success`：是否成功

## 错误码说明

| 错误码 | 描述 |
| ------ | ---- |
| 1000 | 系统错误 |
| 1001 | 参数错误 |
| 1002 | 数据未找到 |
| 3000 | 模型错误 |
| 4000 | 智能体错误 |
| 4002 | 智能体超时 |
| 4003 | 工具执行错误 |
| 4004 | 最大步骤数超出 | 