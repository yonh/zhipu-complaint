# zhipu-complaint

Claude Code 插件，帮助用户生成针对智谱 AI（智谱华章科技有限公司）的正式消费者权益投诉信。

## 功能介绍

本插件将 Claude Code 变为一个对话式投诉信助手。通过结构化访谈，Claude 收集你的事实经过，适用相关消费者权益保护法律，生成可直接发送的 Markdown 格式投诉信。支持多次迭代修改，输出带版本号。

## 安装

```bash
# 第一步：添加 marketplace
/plugin marketplace add yonh/zhipu-complaint

# 第二步：安装插件
/plugin install zhipu-complaint@zhipu-complaint
```

## 使用方式

| 命令 | 语言 | 说明 |
|------|------|------|
| `/zhipu-complaint` | 中文（默认） | 启动新投诉信会话，Claude 全程引导 |
| `/zhipu-complaint-update` | 中文（默认） | 追加事实、更新诉求或完善已有草稿 |
| `/zhipu-complaint-en` | English | Same as above in English |
| `/zhipu-complaint-update-en` | English | Same as above in English |

## 使用前准备

开始前，请准备好以下信息：

- **关键日期和时间线** — 何时订阅？问题从何时开始？
- **承诺内容** — 公告、邮件或界面截图，记录了对方承诺的内容
- **实际发生的情况** — 与承诺有什么出入
- **你的具体诉求** — 退款、恢复权益、赔偿等
- **手头的证据** — 收据、截图、客服聊天记录等

## 输出格式

生成的投诉信为：

- **Markdown 格式** — 可直接用于邮件、打印或社交平台发布
- **带版本号** — 每次修订标注版本（v1、v2、v3……）
- **含变更记录** — 更新时附带变更摘要

## 法律依据

本插件引用以下中国消费者保护法律：

- 《消费者权益保护法》(Consumer Rights Protection Law)
- 《民法典》合同编 (Civil Code — Contract Section)
- 《网络交易监督管理办法》(Online Transaction Supervision Measures)

**重要提示：** 用户在正式提交投诉前，应核实所有法律引用的准确性。

## License

MIT

**English version:** See [README.en.md](./README.en.md)
