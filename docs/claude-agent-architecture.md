# Claude Code Agents: Architecture and Constraints

## 核心概念

### Agent 定义 vs Agent 调用

| 概念 | 说明 | 位置 |
|------|------|------|
| **Agent 定义文件** | 描述子代理行为的 Markdown 文件 | `agents/*.md` |
| **Agent 调用** | 使用 `Agent` 工具创建子代理会话 | 由父代理执行 |

## 关键规则

### ❌ 子代理不能嵌套调用 Agent

```python
# 错误的：子代理内部调用 Agent
def harness_planner():  # 子代理
    # ... 做规划工作 ...
    Agent(  # ❌ 子代理不应该这样做！
        description="调用另一个代理",
        subagent_type="harness-evaluator"
    )
```

### ✅ 正确模式：父代理控制所有调度

```python
# 正确的：父代理控制整个流程
def parent_agent():
    # 步骤 1: 调用规划器
    plan = Agent(
        description="规划任务",
        subagent_type="harness-planner"
    )

    # 父代理解析结果，决定下一步
    if plan.dispatch_gate.required:
        # 步骤 2: 调用调度门
        gate = Agent(
            description="确定实现代理",
            subagent_type="harness-dispatch-gate"
        )

    # 步骤 3: 调用生成器
    result = Agent(
        description="实现功能",
        subagent_type="harness-generator"
    )

    # 步骤 4: 调用评估器
    evaluation = Agent(
        description="验证质量",
        subagent_type="harness-evaluator"
    )
```

## 为什么这样设计

1. **避免深度嵌套** - 防止代理调用代理调用代理的无限链
2. **简化调试** - 调用关系扁平，易于追踪
3. **父代理控制** - 父代理保有完整的决策和协调权
4. **资源管理** - 避免同时运行过多子代理

## 子代理需要另一个角色时怎么办

子代理返回特殊状态，让父代理处理：

```yaml
# 子代理返回值
status: NEEDS_CONTEXT
requested_role: "harness-evaluator"
reason: "当前实现需要独立评估"
required_inputs:
  - "contract: docs/contracts/sprint-001.md"
  - "handoff: docs/handoffs/generator-001.md"
```

父代理解析后决定：

```python
def parent_agent():
    result = Agent(subagent_type="harness-generator")

    if result.status == "NEEDS_CONTEXT":
        # 父代理决定调度请求的角色
        eval_result = Agent(
            subagent_type=result.requested_role,
            prompt=f"评估工作: {result.reason}"
        )
```

## Agent 定义文件结构

```markdown
---
name: harness-planner
description: |
  什么时候使用这个代理...
model: claude-opus-4-6
sandbox_mode: read-only
---

# Agent 名称

## 工作模式
1. 步骤 1
2. 步骤 2

## 返回格式
```yaml
status: DONE | BLOCKED | NEEDS_CONTEXT
result: "..."
```

## 规则
- 不调用其他 Agent
- 返回 NEEDS_CONTEXT 如果需要其他角色
```

## 总结

| 操作 | 谁来做 | 说明 |
|------|--------|------|
| 创建 Agent 定义 | 开发者 | 编写 `.md` 文件放在 `agents/` |
| 调用 Agent | 父代理 | 使用 `Agent` 工具 |
| 请求其他角色 | 子代理 | 返回 `NEEDS_CONTEXT` 状态 |
| 决定调度 | 父代理 | 根据子代理返回决定下一步 |

## 对比其他平台

### Codex
- 子代理可以使用 `codex exec` 或 `codex review` 嵌套调用
- 实际上也存在深度嵌套的风险

### Claude Code
- 明确禁止子代理嵌套调用
- 所有调度必须通过父代理
- 更扁平、更易管理的架构

## 最佳实践

1. **Agent 文件保持纯描述性** - 只定义行为，不执行调用
2. **父代理保有控制权** - 所有 `Agent` 工具调用都在父代理中
3. **清晰的返回契约** - 子代理明确返回状态和下一步建议
4. **避免递归** - 不要让子代理返回自己来循环
