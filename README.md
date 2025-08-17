# 智能旅行规划Agent

一个基于LangGraph和大语言模型的智能旅行规划系统，能够根据用户需求自动生成个性化的旅行行程，包含景点推荐、天气分析、预算控制和路线优化等功能。

## ✨ 核心功能

- 🤖 **智能对话**: 多轮对话收集用户需求，自动补全缺失信息
- 🎯 **个性化推荐**: 基于用户偏好和景点热度智能推荐景点
- 🌤️ **天气适配**: 根据实时天气调整行程，避免恶劣天气影响
- 👨‍👩‍👧‍👦 **团队约束**: 考虑团队成员构成（成人/儿童/老人）调整行程强度
- 💰 **预算控制**: 智能控制总预算，包含景点、住宿、餐饮、交通费用
- 🗺️ **路线优化**: 基于地理位置优化每日行程路线，确保顺路高效
- ⏰ **时间管理**: 考虑景点开放时间和建议游玩时长，合理安排时间

## 🏗️ 项目架构

```
旅行Agent/
├── src/                      # 核心源码
│   ├── main.py              # 主程序入口
│   ├── workflow.py          # LangGraph工作流定义
│   ├── models.py            # Pydantic数据模型
│   ├── llm_utils.py         # LLM工具函数
│   ├── poi_utils.py         # 景点处理工具
│   └── weather_classifier.py # 天气分类器
├── tools/                    # 外部API工具集
│   ├── base_tool.py         # 工具基类
│   ├── weather.py           # 和风天气API
│   ├── hotel.py             # 酒店查询工具
│   └── routeinf.py          # 路线查询工具
├── data/                     # 数据文件
│   └── beijing_poi.json     # 北京景点数据库
├── tests/                    # 测试套件
│   ├── run_tests.py         # 测试运行器
│   ├── test_preference_filter.py  # 偏好筛选测试
│   ├── test_weather_filter.py     # 天气过滤测试
│   └── ...                  # 其他测试文件
├── config.py                # 配置管理
├── requirements.txt         # Python依赖
└── README.md               # 项目文档
```

## 🚀 快速开始

### 1. 安装依赖

```bash
pip install -r requirements.txt
```

### 2. 环境配置

创建`.env`文件并配置必要的API密钥：

```bash
# OpenAI API (必需)
OPENAI_API_KEY=your_openai_api_key_here

# 沃卡平台API (可选，作为OpenAI备选)
WOKA_API_BASE=https://4.0.wokaai.com/v1
WOKA_MODEL_NAME=gpt-3.5-turbo

# 和风天气API (可选，用于天气查询)
HEFENG_API_HOST=your_hefeng_host
HEFENG_API_KEY=your_hefeng_api_key

# 其他API密钥 (预留)
WEATHER_API_KEY=your_weather_api_key
HOTEL_API_KEY=your_hotel_api_key
TRANSPORT_API_KEY=your_transport_api_key
```

### 3. 运行程序

```bash
python src/main.py
```

或者在代码中调用：

```python
from src.main import run_travel_agent_multi_turn

# 单次规划
user_input = "我带孩子从上海到北京玩两天，时间是2025-08-17至2025-08-18，想去故宫和环球影城，预算8000元"
result = run_travel_agent_multi_turn(user_input)
print(result)
```

## 🧠 工作流节点

项目采用LangGraph构建的复杂工作流，包含以下关键节点：

### 信息收集阶段
- **parse_input**: 解析用户输入，提取结构化信息
- **check_fields**: 检查必需字段完整性
- **ask_question**: 智能追问缺失信息

### 约束处理阶段
- **preference_filter**: 基于用户偏好和景点热度筛选候选景点
- **weather_filter**: 根据天气条件过滤室外景点
- **team_constraints**: 根据团队构成设置时间和住宿约束

### 行程规划阶段
- **route_feasibility**: 规划景点、餐厅、酒店和交通路线
- **time_window_check**: 检查景点开放时间匹配性
- **intensity_check**: 控制每日行程强度
- **budget_check**: 预算可行性验证

## 🎯 使用示例

### 基础对话示例

```
User: 我想去北京玩3天，有什么推荐吗？

Assistant: 好的！为了给您推荐最合适的行程，我需要了解一些信息：
- 您的出发城市是哪里？
- 计划什么时候去北京？（具体日期）
- 大概的预算是多少？
- 一共几个人？有老人或小孩吗？
- 对景点类型有什么偏好吗？（比如历史文化、现代娱乐、自然风光等）

User: 我从上海出发，8月17日到8月19日，预算8000元，我和我8岁的儿子两个人。喜欢历史文化，但也想带孩子去玩一些有趣的地方。

Assistant: 完美！我为您规划一个3天的北京亲子文化之旅...
[生成详细行程]
```

### 完整输入示例

```python
# 信息完整的用户输入
user_input = """
我带孩子从上海到北京玩两天，时间是2025-08-17至2025-08-18，
想去故宫和环球影城和北京野生动物园，只有我和孩子2个人，
两天预算8000元
"""

result = run_travel_agent_multi_turn(user_input)
print(result)
```

## 🧪 测试功能

项目包含完整的测试套件，可以单独测试各个功能模块：

```bash
# 运行所有测试
python tests/run_tests.py

# 测试偏好筛选功能
python tests/test_preference_filter.py

# 测试天气过滤功能  
python tests/test_weather_filter.py

# 调试景点数据加载
python tests/debug_poi_loading.py
```

## 📊 数据说明

### 景点数据库（beijing_poi.json）

包含北京50+热门景点的详细信息：

```json
{
  "name": "故宫博物院",
  "location": {"lat": 39.916345, "lng": 116.397155},
  "district": "东城区",
  "tags": ["历史", "博物馆", "古建筑"],
  "ticket_price": 60,
  "open_time": "08:30-17:00",
  "suggested_duration_hours": 4,
  "popularity_score": 0.95,
  "suitable_for": ["成人", "青少年", "儿童"],
  "indoor": "室内"
}
```

### 关键字段说明

- **popularity_score**: 景点热度评分（0-1），用于智能推荐排序
- **suggested_duration_hours**: 建议游玩时长，用于时间规划
- **suitable_for**: 适合人群，用于团队适配
- **indoor**: 室内/室外属性，用于天气过滤
- **tags**: 景点标签，用于偏好匹配

## 🔧 技术栈

- **LangGraph**: 构建复杂的AI工作流
- **LangChain**: LLM应用开发框架
- **Pydantic**: 数据验证和序列化
- **OpenAI API**: 大语言模型服务
- **Python 3.8+**: 编程语言

## 🔄 版本历史

- **V1**: 基础的LLM意图提取和信息补全
- **V2**: 增加景点偏好筛选和团队约束处理
- **V3**: 重构代码架构，增加路程时间和预算约束
- **V4**: 完善工作流，增加天气适配和全面测试

## 🛠️ 开发指南

### 添加新的工具

在`tools/`目录下创建新工具，继承`BaseTool`：

```python
from tools.base_tool import BaseTool
from typing import Dict, Any

class NewTool(BaseTool):
    def __init__(self, api_key: str):
        super().__init__(api_key)
    
    def execute(self, **kwargs) -> Dict[str, Any]:
        # 实现工具逻辑
        return {"result": "success"}
```

### 扩展工作流节点

在`src/workflow.py`中添加新节点：

```python
def new_node(state: AgentState) -> AgentState:
    """新节点功能"""
    # 实现节点逻辑
    return state

# 在workflow中注册节点
workflow.add_node("new_node", new_node)
```

### 添加测试

在`tests/`目录下创建新的测试文件：

```python
import sys
import os
sys.path.append(os.path.dirname(os.path.dirname(os.path.abspath(__file__))))

def test_new_feature():
    """测试新功能"""
    # 测试代码
    pass

if __name__ == "__main__":
    test_new_feature()
```

## ⚠️ 注意事项

1. **API密钥安全**: 请勿将真实API密钥提交到版本控制系统
2. **网络依赖**: 天气查询等功能需要网络连接
3. **数据更新**: 景点信息可能需要定期更新
4. **性能优化**: 大量景点数据可能影响响应速度

## 🤝 贡献指南

欢迎提交Issue和Pull Request！

1. Fork项目
2. 创建功能分支：`git checkout -b feature/new-feature`
3. 提交更改：`git commit -m 'Add new feature'`
4. 推送分支：`git push origin feature/new-feature`
5. 提交Pull Request

## 📄 许可证

本项目采用MIT许可证 - 详见[LICENSE](LICENSE)文件

## 📞 联系方式

如有问题或建议，请通过以下方式联系：

- 提交GitHub Issue
- 发送邮件至开发者
- 在项目讨论区交流

---

**Happy Traveling! 🎒✈️**