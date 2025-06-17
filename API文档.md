# Lei AI Agent API 文档

## 基础信息

- 基础路径: `/`
- 响应格式: 所有API返回统一的响应格式

## 通用响应格式

所有API调用都会返回以下格式的数据:

```json
{
  "code": 0,       // 状态码: 0表示成功，非0表示错误
  "data": {},      // 响应数据: 根据接口不同而不同
  "message": "ok"  // 响应消息: 成功为"ok"，失败时显示错误信息
}
```

### 常见状态码

| 状态码 | 说明 |
|-------|------|
| 0 | 成功 |
| 40000 | 请求参数错误 |
| 40100 | 未登录 |
| 40101 | 无权限 |
| 40300 | 禁止访问 |
| 40400 | 请求数据不存在 |
| 50000 | 系统内部异常 |
| 50001 | 操作失败 |

## 智能体 API

### 1. 执行智能体任务（同步模式）

使用LiManus智能体处理用户请求，同步返回执行结果

- **URL**: `/agent/execute`
- **方法**: `POST`
- **参数**:
  - `prompt`: 用户请求内容（必填）
- **请求示例**:
```
POST /agent/execute?prompt=帮我规划一次北京三日游
```
- **响应示例**:
```json
{
  "code": 0,
  "data": {
    "status": "success",
    "result": "我为您规划了一次北京三日游行程...",
    "agentState": "FINISHED",
    "sessionId": "user123-session456",
    "message": null,
    "timestamp": "2023-06-01T12:00:00",
    "executionTime": 1500
  },
  "message": "ok"
}
```

### 2. 执行智能体任务（高级同步模式）

使用LiManus智能体处理复杂请求，支持会话、系统提示和步骤控制

- **URL**: `/agent/execute/advanced`
- **方法**: `POST`
- **请求体**:
```json
{
  "prompt": "帮我规划一次北京三日游",
  "sessionId": "user123-session456",
  "systemPrompt": "你是一个专业的旅游规划师",
  "maxSteps": 10
}
```
- **响应示例**:
```json
{
  "code": 0,
  "data": {
    "status": "success",
    "result": "我为您规划了一次北京三日游行程...",
    "agentState": "FINISHED",
    "sessionId": "user123-session456",
    "message": null,
    "timestamp": "2023-06-01T12:00:00",
    "executionTime": 1500
  },
  "message": "ok"
}
```

### 3. 执行智能体任务（流式模式）

使用LiManus智能体处理用户请求，通过SSE流式返回执行过程和结果

- **URL**: `/agent/stream`
- **方法**: `GET`
- **参数**:
  - `prompt`: 用户请求内容（必填）
- **请求示例**:
```
GET /agent/stream?prompt=帮我规划一次北京三日游
```
- **响应**:
  - 返回Server-Sent Events流，每个事件包含部分响应结果

### 4. 执行智能体任务（高级流式模式）

使用LiManus智能体处理复杂请求，支持会话、系统提示和步骤控制，通过SSE流式返回执行过程和结果

- **URL**: `/agent/stream/advanced`
- **方法**: `POST`
- **请求体**:
```json
{
  "prompt": "帮我规划一次北京三日游",
  "sessionId": "user123-session456",
  "systemPrompt": "你是一个专业的旅游规划师",
  "maxSteps": 10
}
```
- **响应**:
  - 返回Server-Sent Events流，每个事件包含部分响应结果

### 5. 获取智能体状态

获取LiManus智能体的当前状态和配置信息

- **URL**: `/agent/status`
- **方法**: `GET`
- **响应示例**:
```json
{
  "code": 0,
  "data": {
    "status": "success",
    "result": "智能体状态信息",
    "agentState": "IDLE",
    "sessionId": null,
    "message": null,
    "timestamp": "2023-06-01T12:00:00",
    "executionTime": null
  },
  "message": "ok"
}
```

### 6. 重置智能体

将LiManus智能体重置到初始状态，清除所有当前上下文

- **URL**: `/agent/reset`
- **方法**: `POST`
- **响应示例**:
```json
{
  "code": 0,
  "data": {
    "status": "success",
    "result": "智能体已重置",
    "agentState": "IDLE",
    "sessionId": null,
    "message": null,
    "timestamp": "2023-06-01T12:00:00",
    "executionTime": null
  },
  "message": "ok"
}
```

### 7. 关闭流式连接

手动关闭指定会话ID的SSE流式连接

- **URL**: `/agent/stream/close/{sessionId}`
- **方法**: `POST`
- **路径参数**:
  - `sessionId`: 会话ID（必填）
- **响应示例**:
```json
{
  "code": 0,
  "data": {
    "status": "success",
    "result": "流式连接已关闭",
    "agentState": null,
    "sessionId": "user123-session456",
    "message": null,
    "timestamp": "2023-06-01T12:00:00",
    "executionTime": null
  },
  "message": "ok"
}
```

## AI聊天 API

### 1. 对话聊天-同步调用

处理用户消息并返回聊天响应

- **URL**: `/ai/chat`
- **方法**: `GET`
- **参数**:
  - `message`: 用户消息（必填）
  - `chatId`: 会话ID（必填）
- **请求示例**:
```
GET /ai/chat?message=你好&chatId=chat123
```
- **响应示例**:
```json
{
  "code": 0,
  "data": "你好！我是AI助手，有什么可以帮助你的吗？",
  "message": "ok"
}
```

### 2. 对话聊天-流式调用（方法一）

使用Flux实现流式聊天

- **URL**: `/ai/chat/stream`
- **方法**: `GET`
- **参数**:
  - `message`: 用户消息（必填）
  - `chatId`: 会话ID（必填）
- **请求示例**:
```
GET /ai/chat/stream?message=你好&chatId=chat123
```
- **响应**:
  - 返回文本流，每个部分包含部分响应结果

### 3. 对话聊天-流式调用（方法二）

使用Server-Sent Events (SSE)实现流式聊天

- **URL**: `/ai/chat/stream-sse`
- **方法**: `GET`
- **参数**:
  - `message`: 用户消息（必填）
  - `chatId`: 会话ID（必填）
- **请求示例**:
```
GET /ai/chat/stream-sse?message=你好&chatId=chat123
```
- **响应**:
  - 返回SSE流，每个事件包含部分响应结果

### 4. 对话聊天-流式调用（方法三）

使用SseEmitter实现流式聊天

- **URL**: `/ai/chat/stream-sse-emitter`
- **方法**: `GET`
- **参数**:
  - `message`: 用户消息（必填）
  - `chatId`: 会话ID（必填）
- **请求示例**:
```
GET /ai/chat/stream-sse-emitter?message=你好&chatId=chat123
```
- **响应**:
  - 返回SSE流，每个事件包含部分响应结果

### 5. 结构化输出聊天报告

处理用户消息并返回结构化的聊天报告

- **URL**: `/ai/chat/report`
- **方法**: `GET`
- **参数**:
  - `message`: 用户消息（必填）
  - `chatId`: 会话ID（必填）
- **请求示例**:
```
GET /ai/chat/report?message=分析北京旅游景点&chatId=chat123
```
- **响应示例**:
```json
{
  "code": 0,
  "data": {
    "title": "北京旅游景点分析",
    "content": [
      "1. 故宫：中国明清两代的皇家宫殿",
      "2. 长城：中国古代伟大的防御工程",
      "3. 颐和园：清代皇家园林",
      "..."
    ]
  },
  "message": "ok"
}
```

### 6. 流式聊天，使用工具

处理用户消息并返回流式聊天结果，支持工具调用

- **URL**: `/ai/chat/tools`
- **方法**: `GET`
- **参数**:
  - `message`: 用户消息（必填）
  - `chatId`: 会话ID（必填）
- **请求示例**:
```
GET /ai/chat/tools?message=北京今天天气&chatId=chat123
```
- **响应**:
  - 返回SSE流，每个事件包含部分响应结果

### 7. 流式聊天，使用工具和回调

处理用户消息并返回流式聊天结果，支持工具调用和回调

- **URL**: `/ai/chat/tools/callback`
- **方法**: `GET`
- **参数**:
  - `message`: 用户消息（必填）
  - `chatId`: 会话ID（必填）
- **请求示例**:
```
GET /ai/chat/tools/callback?message=帮我预订北京的酒店&chatId=chat123
```
- **响应**:
  - 返回SSE流，每个事件包含部分响应结果

## 知识库问答 API

### 1. 流式回答知识库问答

使用RAG模型进行知识库问答，支持流式响应

- **URL**: `/rag/chat`
- **方法**: `GET`
- **参数**:
  - `message`: 用户问题（必填）
  - `chatId`: 会话ID（必填）
- **请求示例**:
```
GET /rag/chat?message=北京有哪些景点&chatId=chat123
```
- **响应**:
  - 返回SSE流，每个事件包含部分响应结果

## 前端调用示例

### 同步调用示例（使用fetch）

```javascript
async function callSyncApi() {
  try {
    const response = await fetch('/agent/execute?prompt=帮我规划一次北京三日游');
    const data = await response.json();
    
    if (data.code === 0) {
      // 成功处理
      console.log('执行结果:', data.data.result);
    } else {
      // 错误处理
      console.error('错误:', data.message);
    }
  } catch (error) {
    console.error('请求失败:', error);
  }
}
```

### 流式调用示例（使用EventSource）

```javascript
function callStreamApi() {
  const eventSource = new EventSource('/agent/stream?prompt=帮我规划一次北京三日游');
  
  eventSource.onmessage = function(event) {
    // 处理每个返回的数据块
    console.log('收到数据:', event.data);
    // 将数据显示在页面上
    document.getElementById('result').textContent += event.data;
  };
  
  eventSource.onerror = function(error) {
    console.error('流式请求错误:', error);
    eventSource.close();
  };
  
  // 在适当的时候关闭连接
  // eventSource.close();
}
```

### 高级同步调用示例（使用axios）

```javascript
async function callAdvancedSyncApi() {
  try {
    const requestData = {
      prompt: '帮我规划一次北京三日游',
      sessionId: 'user123-session456',
      systemPrompt: '你是一个专业的旅游规划师',
      maxSteps: 10
    };
    
    const response = await axios.post('/agent/execute/advanced', requestData);
    
    if (response.data.code === 0) {
      // 成功处理
      console.log('执行结果:', response.data.data.result);
    } else {
      // 错误处理
      console.error('错误:', response.data.message);
    }
  } catch (error) {
    console.error('请求失败:', error.response?.data || error);
  }
}
``` 