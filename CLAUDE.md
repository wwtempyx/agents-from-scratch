# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概览

这是一个使用LangGraph从零构建智能代理的教程仓库，重点构建一个能够管理邮件的"环境"代理，包含以下功能：
- 邮件分类
- 起草回复
- 执行操作（日程安排等）
- 整合人工反馈
- 从过往交互中学习

项目包含4个递进的部分，每部分都有对应的Jupyter笔记本和代码实现。

## 环境设置

### 依赖安装

**推荐使用uv（更快更可靠）：**
```bash
# 安装uv
pip install uv

# 安装包及开发依赖
uv sync --extra dev

# 激活虚拟环境
source .venv/bin/activate
```

**备选使用pip：**
```bash
python3 -m venv .venv
source .venv/bin/activate
python3 -m pip install --upgrade pip
pip install -e .
```

### 环境变量配置

复制环境变量示例文件：
```bash
cp .env.example .env
```

编辑`.env`文件，添加必需的API密钥：
```bash
LANGSMITH_API_KEY=你的langsmith_api_key
LANGSMITH_TRACING=true
LANGSMITH_PROJECT="interrupt-workshop"
OPENAI_API_KEY=你的openai_api_key
```

## 常用命令

### 开发和测试
```bash
# 运行所有测试
python tests/run_all_tests.py --all

# 运行笔记本测试
python tests/test_notebooks.py

# 使用pytest运行测试
pytest tests/test_notebooks.py -v

# 代码格式检查
ruff check .

# 类型检查
mypy .
```

### LangGraph Studio
该项目配置了多个可在LangGraph Studio中运行的图：
- `langgraph101` - LangGraph基础教程
- `email_assistant` - 基础邮件助手
- `email_assistant_hitl` - 带人机协作的邮件助手
- `email_assistant_hitl_memory` - 带记忆功能的人机协作邮件助手
- `email_assistant_hitl_memory_gmail` - Gmail集成版本
- `cron` - 定时任务图

## 代码架构

### 核心实现文件
- `src/email_assistant/email_assistant.py` - 基础邮件助手实现
- `src/email_assistant/email_assistant_hitl.py` - 人机协作版本
- `src/email_assistant/email_assistant_hitl_memory.py` - 带记忆功能版本
- `src/email_assistant/email_assistant_hitl_memory_gmail.py` - Gmail集成版本

### 关键模块
- `schemas.py` - 数据结构定义
- `prompts.py` - 系统提示词
- `tools/` - 工具实现目录
  - `default/` - 模拟工具（邮件、日历）
  - `gmail/` - Gmail API集成工具
- `eval/` - 评估框架和数据集

### 架构特点
1. **状态管理**：使用LangGraph的State定义和管理对话状态
2. **工具调用**：支持邮件发送、日历管理等工具
3. **人机协作**：使用Agent Inbox进行人工审核
4. **记忆系统**：使用LangGraph Store持久化记忆
5. **评估框架**：LLM-as-a-judge评估工具调用和回复质量

## 测试说明

### 测试覆盖范围
- 工具调用正确性验证
- 邮件回复质量评估
- 分类决策准确性测试
- 笔记本执行完整性检查

### 注意事项
- Gmail集成版本（`email_assistant_hitl_memory_gmail`）不包含在自动测试中
- 人机协作版本需要特殊处理，因为包含Question工具
- 测试结果通过LangSmith进行跟踪和可视化

## Gmail集成

Gmail集成需要额外设置Google API凭据，详见：
`src/email_assistant/tools/gmail/README.md`

该模块支持：
- 真实Gmail邮件处理
- 部署到LangGraph Platform
- 定时任务和邮件摄取