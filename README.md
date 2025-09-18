# 🔖 文献标签自动生成器（Controlled-Tagger）

> 从论文**摘要/全文**中，基于**受控词表**（Field / Object / Method / Type）自动抽取并返回 **3–5 个标准化标签（JSON 数组）**。支持独立命令行 & Zotero 内联触发。

<img width="2639" height="1517" alt="image" src="https://github.com/user-attachments/assets/d66829c6-df12-4c20-8cda-bd97e13dd7c3" />
---

## ✨ 特性

* **受控词表 + 标准输出**：严格输出命名空间 `#Field/… #Object/… #Method/… #Type/…` 的标签，减少歧义，便于长期协同维护。
* **双语抽取**：同时识别中英文关键词段落（Keywords/关键词），并结合全文上下文增强召回。
* **可嵌入 Zotero**：支持在 Zotero 内以**悬浮通知**提示进度、无阻塞执行，将结果回填到条目标签/笔记。
* **可扩展**：词表、正则规则、停用词、切片策略、输出过滤器均可配置。
---

## 🧰 环境需求

* 基于Awesome GPT Command Tag功能，详细开始步骤见https://github.com/MuiseDestiny/zotero-gpt?tab=readme-ov-file#hi-command-tag

---

## 🚀 快速开始

### 方式 A：命令行（CLI）

```bash
# 1) 安装
npm i -g controlled-tagger   # 或者：pnpm add -g controlled-tagger

# 2) 运行（输入任意论文 txt/markdown/pdf 提取后的纯文本）
controlled-tagger \
  --input ./samples/paper_001.txt \
  --schema ./configs/controlled_list.json \
  --output ./out/tags.json
```

* `--input`：论文**摘要或全文纯文本**路径
* `--schema`：受控词表与命名空间配置（示例见下）
* `--output`：输出 JSON 文件（形如 `["#Field/IndoorAirQuality","#Object/Furniture",...]`）

#### 受控词表示例 `./configs/controlled_list.json`

```json
{
  "namespaces": ["#Field", "#Object", "#Method", "#Type"],
  "allowed": [
    "#Field/BuildingEnergyConsumption",
    "#Field/PassiveEnergy-savingTech",
    "#Field/RadiantFloorHeating",
    "#Field/IndoorThermalComfort",
    "#Field/EnvelopeAging",
    "#Field/MaterialDegradation",
    "#Field/EnergySavingRetrofit",
    "#Field/OccupantBehavior",
    "#Field/IndoorAirQuality",
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
  ],
  "maxTags": 5,
  "minTags": 3
}
```

### 方式 B：作为库（Library）

```ts
import { extractTags } from "controlled-tagger";
import fs from "node:fs/promises";

const text = await fs.readFile("./samples/paper_001.txt", "utf8");
const schema = JSON.parse(await fs.readFile("./configs/controlled_list.json", "utf8"));

const tags = extractTags(text, { schema, langHint: "auto" });
// => ["#Field/IndoorAirQuality","#Object/Furniture","#Method/Experiment"]

console.log(tags);
```

### 方式 C：Zotero 内联触发（可选）

1. 将 `zotero/trigger.js` 复制到你的 Zotero 脚本/插件环境中（支持 Zotero 7 内置脚本或插件）。
2. 在条目笔记/快捷命令里输入触发词（例如：`生成标签`）。
3. 运行后将通过**悬浮通知**显示进度，并把标签（JSON 数组）回写到条目标签或笔记中。

> 截图占位：
>
> * ![screenshot-zotero](./docs/screenshot-zotero.png)
> * ![screenshot-toast](./docs/screenshot-toast.png)

---

## 🧪 输入/输出示例

**输入（片段）**：

```
… Investigation of airflow patterns in a microclimate by PIV …
… mould growth behind closets … poor insulation … indoor air quality …
```

**输出**：

```json
["#Field/IndoorAirQuality","#Object/Furniture","#Method/Experiment"]
```

---

## ⚙️ 高级配置

`controlled-tagger.config.json`（可选）：

```json
{
  "schemaPath": "./configs/controlled_list.json",
  "keywordHints": ["Keywords:", "关键词", "Key words"],
  "stopHeadings": ["Abstract", "Introduction", "摘要", "引言", "参考文献"],
  "maxScanLines": 500,
  "postFilters": {
    "dedupe": true,
    "keepTopK": 5,
    "namespaceOrder": ["#Field", "#Object", "#Method", "#Type"]
  },
  "llm": {
    "enabled": false,
    "provider": "openai",
    "model": "gpt-4o-mini",
    "temperature": 0.0
  }
}
```

* `keywordHints`：定位“关键词/Keywords”段落的启发式标记
* `stopHeadings`：遇到即停止拼接的下一节标题
* `llm.enabled`：可选启用 LLM 进行召回增强（默认关闭）

---

## 📊 评测与批处理（可选）

```bash
# 批量处理一个目录
controlled-tagger \
  --input-dir ./papers_txt \
  --schema ./configs/controlled_list.json \
  --output-dir ./out

# 统计覆盖率/一致性（需要 python，可选）
python scripts/eval_consistency.py --pred ./out --gold ./gold_standard.json
```

---

## 🧩 集成建议

* **Zotero**：将结果写入“标签”或在“笔记”中同步 JSON 数组，便于后续搜索与批量操作。
* **Obsidian/Notion**：把 JSON 标签存为前置 YAML 或文末元数据，配合 Dataview/自定义查询。
* **CI/CD**：通过 GitHub Actions 对新提交的文献文本自动生成标签并提交回仓库。

---

## ❓FAQ

* **Q：输出为什么严格是 JSON 数组？**
  A：为便于机器消费（管线/脚本）与跨工具粘贴，且便于单元测试与一致性校验。

* **Q：能否自定义领域？**
  A：可以。直接在 `controlled_list.json` 中新增或删减条目，或按项目拆分多个 schema。

* **Q：遇到 PDF 解析不全怎么办？**
  A：建议先用 `pdf2text`/`GROBID`/`pdfminer.six` 提前转为纯文本，再交给本工具处理；或启用 LLM 辅助召回。

---

## 🗺️ Roadmap

* [ ] 可视化审阅器（标注命中片段 → 标签）
* [ ] 多模型融合（规则 + LLM + 统计）
* [ ] 更丰富的 Zotero 回填选项（批量、撤销、只写笔记/只写标签）
* [ ] 评测脚本完善（Precision/Recall/F1、Inter-Annotator Agreement）

---

## 🤝 贡献

欢迎 PR / Issue！

1. Fork & 新建分支
2. `pnpm i && pnpm test`（或 `npm i && npm test`）
3. 提交前请通过 Lint 与单测，附上前后对比或截图

---

## 📜 许可证

本项目采用 **MIT License**（见 [LICENSE](./LICENSE)）。

---

## 🙌 致谢

* 社区用户与同领域研究者的测试反馈
* 开源解析与评测工具生态（pdfminer/GROBID 等）

---

> 如果你愿意，我也可以把以上内容直接整理成你的仓库文件结构（`README.md`、`configs/controlled_list.json`、`zotero/trigger.js`、`scripts/eval_consistency.py`、`.github/workflows/ci.yml` 的最小可用版本），你只需要复制粘贴即可用。
