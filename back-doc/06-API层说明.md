# 06-API层说明

## FastAPI 应用 (api/app.py)

**入口**：`uvicorn server:app --reload`

**路由前缀**：`/api/v1`

### CORS 配置

```python
# 允许的来源
origins = [
    "http://localhost:3000",    # 开发
    "http://localhost:8000",
    # 生产环境配置
]

# 支持的方法
methods = ["GET", "POST", "PUT", "DELETE", "OPTIONS"]

# 支持的请求头
headers = ["*"]
```

### 静态文件托管

生产模式下托管前端构建产物：
```python
# static_dir = config.static_dir 或默认 ./static
```

### 健康检查

```
GET /health
```

---

## API 端点 (api/v1/endpoints/)

### analysis.py

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/analysis/analyze` | 触发股票分析 |
| GET | `/analysis/status/{task_id}` | 查询任务状态 |
| GET | `/analysis/history` | 获取历史分析 |
| GET | `/analysis/report/{id}` | 获取报告详情 |

**请求示例**：
```json
POST /api/v1/analysis/analyze
{
    "stocks": ["600519", "000001"],
    "force": false
}
```

**响应示例**：
```json
{
    "task_id": "abc123",
    "status": "pending",
    "message": "任务已提交"
}
```

### stocks.py

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/stocks/search` | 搜索股票 |
| GET | `/stocks/{code}` | 获取股票信息 |
| GET | `/stocks/quotes/realtime` | 实时行情 |
| GET | `/stocks/kline` | K线数据 |
| GET | `/stocks/chips/{code}` | 筹码分布 |

### portfolio.py

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/portfolio` | 获取持仓 |
| POST | `/portfolio` | 添加持仓 |
| PUT | `/portfolio/{id}` | 更新持仓 |
| DELETE | `/portfolio/{id}` | 删除持仓 |
| POST | `/portfolio/import` | 导入持仓 |

### history.py

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/history/reports` | 历史报告列表 |
| GET | `/history/reports/{id}` | 报告详情 |
| GET | `/history/backtest` | 回测历史 |

### backtest.py

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/backtest/run` | 运行回测 |
| GET | `/backtest/results` | 回测结果 |
| GET | `/backtest/results/{id}` | 详情 |

### agent.py

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/agent/chat` | Agent 对话 |
| GET | `/agent/history` | 对话历史 |
| POST | `/agent/strategies` | 策略查询 |

### auth.py

| 方法 | 路径 | 说明 |
|------|------|------|
| POST | `/auth/login` | 登录 |
| POST | `/auth/logout` | 登出 |
| GET | `/auth/status` | 认证状态 |

### system_config.py

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/system/config` | 获取配置 |
| PUT | `/system/config` | 更新配置 |
| GET | `/system/health` | 系统健康 |
| GET | `/system/diagnostics` | 诊断信息 |

### usage.py

| 方法 | 路径 | 说明 |
|------|------|------|
| GET | `/usage/summary` | 使用统计 |
| GET | `/usage/llm` | LLM 调用记录 |

### health.py

```
GET /health
GET /health/ready
```

---

## 中间件 (api/middlewares/)

| 中间件 | 职责 |
|--------|------|
| `error_handler.py` | 全局异常处理 |
| `auth.py` | 认证中间件 |

---

## 依赖注入 (api/deps.py)

```python
# 获取当前用户
async def get_current_user() -> User:

# 获取数据库会话
async def get_db() -> Generator:

# 获取配置
def get_config() -> Config:
```

---

## Schema 定义 (api/v1/schemas/)

| Schema | 用途 |
|--------|------|
| `analysis.py` | 分析相关请求/响应 |
| `stocks.py` | 股票相关 |
| `portfolio.py` | 持仓相关 |
| `history.py` | 历史记录 |
| `backtest.py` | 回测相关 |
| `system_config.py` | 系统配置 |
| `usage.py` | 使用统计 |
| `common.py` | 通用定义 |