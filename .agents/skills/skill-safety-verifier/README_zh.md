# Skill Safety Verifier

<p align="center">
  <img src="https://img.shields.io/badge/Risk-Assessment-green?style=for-the-badge" alt="Risk Assessment">
  <img src="https://img.shields.io/badge/OpenClaw-Skills-orange?style=for-the-badge" alt="OpenClaw Skills">
  <img src="https://img.shields.io/badge/License-MIT-blue?style=for-the-badge" alt="License">
</p>

**[English](README.md) | [中文](README_zh.md)**

> AI Agent的安全审查工具。在安装任何来自ClawdHub、GitHub或其他来源的Skill前进行安全检查。

## 为什么重要

AI Agent以高级权限运行，可以：
- 执行任意命令
- 访问文件系统
- 发起网络请求
- 读取环境变量

**未经审查的Skill = 安全风险**

## 功能特性

- **Socket/网络分析** - 检测可疑的外部连接
- **漏洞扫描** - 通过GitHub Advisory API检查依赖项已知CVE
- **危险模式检测** - 识别危险的代码模式
- **风险分类** - 将Skill分类为Safe/Low/Medium/High
- **标准化报告** - 清晰的风险评估和建议
- **异步获取** - 非阻塞的漏洞检查，本地缓存

---

## 快速开始

### 安装

```bash
# 通过ClawdHub
npx skills add ttttstc/skill-safety-verifier

# 或直接克隆
git clone https://github.com/ttttstc/skill-safety-verifier.git
```

### 使用

```bash
# 安装前检查Skill
skill-safety-check --source github.com/user/skill-repo

# 验证已安装的Skill
skill-safety-check --verify --skill-name <skill-name>
```

---

## 工作流程

```
用户请求
    |
    v
1. 获取Skill
   - 克隆/解压
   - 读取SKILL.md
    |
    v
2. 并行扫描
   - Socket检查 (外部连接)
   - 漏洞扫描 (GitHub Advisory API)
   - 代码模式扫描 (危险函数)
    |
    v
3. 风险评分
   - 计算总分 (网络+漏洞+权限)
   - 分类等级
    |
    v
4. 展示结果
   - 风险雷达
   - 警告信息
   - 建议
    |
    v
用户决定
```

---

## 风险分类

| 等级 | 分数 | 颜色 | 操作 |
|------|------|------|------|
| Safe | 0-10 | 绿色 | 自由安装使用 |
| Low | 11-30 | 黄色 | 谨慎安装 |
| Medium | 31-60 | 橙色 | 先审查代码 |
| High | 61-100 | 红色 | 禁止安装 |

## 评估标准

### Socket/网络风险

| 警报数量 | 分数 |
|----------|------|
| 0 | 0 |
| 1-2 | 10 |
| 3-5 | 25 |
| >5 | 50 |

### 代码漏洞风险

| 严重程度 | 分数 |
|----------|------|
| Critical | 25 |
| High | 15 |
| Medium | 10 |
| Low | 5 |

### 权限范围风险

| 范围 | 分数 |
|------|------|
| 只读 | 0 |
| 网络调用 | 10 |
| 文件写入 | 15 |
| 命令执行 | 25 |
| 完全系统权限 | 50 |

## 危险信号

### 网络
- 可疑域名（非标准TLD）
- 硬编码IP地址
- 对未知域名的DNS查询
- 过多外部连接

### 执行
- 未净化的os.system()/subprocess
- eval()/exec()使用
- Shell注入模式
- 下载并执行代码

### 数据
- 读取敏感环境变量
- 文件外泄
- 键盘记录
- 凭证收集

## GitHub Advisory API集成

```
1. 用户触发安装
2. 立即返回"扫描中..."
   并行执行:
   - 克隆Skill代码
   - 检查本地缓存
   - 异步请求GitHub Advisory API
3. 缓存命中? 是/否
4. 合并结果 - 展示风险雷达
```

### 缓存策略

| 数据 | TTL | 位置 |
|------|-----|------|
| 全量Advisory | 24h | ~/.cache/skill-safety/advisory.json |
| Skill依赖漏洞 | 6h | ~/.cache/skill-safety/{skill}.json |

---

## 输出示例

### 安全Skill

```
skill-name - 风险评估

风险雷达:
  网络风险    0/50  绿色
  漏洞风险    0/25  绿色
  权限风险    0/50  绿色
  总分        0/100 绿色

建议: 安全，可安装
```

### 中风险Skill

```
skill-name - 风险评估

风险雷达:
  网络风险    20/50 黄色
  漏洞风险    5/25  绿色
  权限风险    25/50 橙色
  总分        50/100 橙色

警告: 检测到网络调用
建议: 安装前先审查代码
```

### 高风险Skill

```
skill-name - 风险评估

风险雷达:
  网络风险    50/50 红色
  漏洞风险    25/25 红色
  权限风险    50/50 红色
  总分        100/100 红色

禁止安装
原因:
1. 多个严重漏洞
2. 执行shell命令
3. 无输入净化
```

---

## 集成

### 与ClawdHub集成

```bash
# 安装时自动验证
npx skills add <owner/repo> --verify
```

### 与OpenClaw集成

在配置中启用后，安装Skill时自动运行验证：

```yaml
# .openclaw/config.yaml
skill_safety:
  enabled: true
  auto_check: true
  block_high_risk: false
```

---

## 最佳实践

1. **始终验证** - 永远不要安装未经审查的Skill
2. **阅读代码** - 自动检查不够，需要人工审查
3. **最小权限** - 只授予必要的权限
4. **隔离运行** - 在容器中运行高风险Skill
5. **监控日志** - 记录所有Skill活动

---

## 相关链接

- [ClawdHub](https://clawhub.com) - Skill市场
- [OpenClaw文档](https://docs.openclaw.ai) - 平台文档
- [Skill编写指南](https://docs.openclaw.ai/skills/write) - 如何编写安全Skill

---

## License

MIT License - see LICENSE file for details.
