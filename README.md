# 🔖 文献标签自动生成器（Controlled-Tagger）

本项目需 [zotero-gpt](https://github.com/MuiseDestiny/zotero-gpt) 搭配使用：
- 在 Zotero 中调用 GPT 助手批量生成笔记/摘要  
- 用 Controlled-Tagger 自动生成标准化标签  
👉 实现 **“笔记 + 标签” 一体化文献管理流程**

> 从论文**摘要/全文**中，基于**受控词表**（Field / Object / Method / Type）自动抽取并返回 **3–5 个标准化标签（JSON 数组）**。支持独立命令行 & Zotero 内联触发。

<img width="2639" height="1517" alt="image" src="https://github.com/user-attachments/assets/d66829c6-df12-4c20-8cda-bd97e13dd7c3" />


## ✨ 特性

* **受控词表 + 标准输出**：严格输出命名空间 `#Field/… #Object/… #Method/… #Type/…` 的标签，减少歧义，便于长期协同维护。
* **双语抽取**：同时识别中英文关键词段落（Keywords/关键词），并结合全文上下文增强召回。
* **可嵌入 Zotero**：支持在 Zotero 内以**悬浮通知**提示进度、无阻塞执行，将结果回填到条目标签/笔记。
* **可扩展**：优先使用自定义的词表，但也支持LLM生成符合格式的自定义标签。
* **标签规范化**：自动匹配库中已有标签（忽略大小写），避免重复添加。
---

## 🧰 适用插件

* 基于Awesome GPT Command Tag功能，详细开始步骤见https://github.com/MuiseDestiny/zotero-gpt?tab=readme-ov-file#hi-command-tag

---

## 🚀 快速开始

#### 定制受控词表
```markdown
## 🔧 配置受控词表（可自定义）

将下方代码复制到你的文档里。**中间那行绿色注释**标明了可增删自定义关键词的位置。

```js
// 受控词表（Controlled Vocabulary）
const CONTROLLED = new Set([
  "#Field/BuildingEnergyConsumption",
  "#Field/PassiveEnergy-savingTech",
  "#Field/RadiantFloorHeating",
  "#Field/IndoorThermalComfort",
  "#Field/EnvelopeAging",
  "#Field/MaterialDegradation",
  "#Field/EnergySavingRetrofit",
  "#Field/OccupantBehavior",
  "#Field/IndoorAirQuality",

  // ✅ 在此处增删你自己的关键词（GitHub 会把这一行显示为绿色）

  "#Object/Furniture",
  "#Object/RuralResidence",
  "#Object/Low-eMaterial",
  "#Method/MachineLearning",
  "#Method/ResidualAnalysis",
  "#Method/TimeSeriesAnalysis",
  "#Method/CFD",
  "#Method/BuildingEnergySimulation",
  "#Method/Experiment",
  "#Type/Review"
]);

```
## 🤝 贡献

欢迎 PR / Issue！

1. Fork & 新建分支
2. `pnpm i && pnpm test`（或 `npm i && npm test`）
3. 提交前请通过 Lint 与单测，附上前后对比或截图

---

## 📜 许可证

本项目采用 **MIT License**（见 [LICENSE](./LICENSE)）（GPT生成）。

---

## 🙌 致谢


---
