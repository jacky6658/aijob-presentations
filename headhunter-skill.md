# 獵頭顧問 AI 助手 (Headhunter Skill)

獵頭顧問工作流程的 AI 輔助工具，內建 8 個核心功能的 Prompt 模板。

## 使用方式

直接用自然語言觸發，例如：
- 「分析這份履歷跟 JD 的匹配度」
- 「幫我寫開發信」
- 「做人才畫像」
- 「生成面試問題」

## 功能列表

### 1. 人才畫像 (Persona)
**觸發詞**：人才畫像、persona、分析 JD
**輸入**：JD 內容
**輸出**：7 維度人才畫像（工作類型、特質、技能、價值觀、背景、管道、關鍵字）

### 2. 人才搜尋策略 (Talent Search)
**觸發詞**：搜尋策略、Boolean、找人
**輸入**：職稱、技能、地點
**輸出**：招募管道、目標公司名單、Boolean Search String

### 3. 履歷-JD 匹配 (Match Analysis)
**觸發詞**：匹配度、分析履歷、適合嗎
**輸入**：JD + 履歷
**輸出**：匹配分數、優缺點、推薦程度、建議問題

### 4. 開發信 (Outreach)
**觸發詞**：開發信、InMail、寫信給候選人
**輸入**：職位、候選人名、公司、履歷、JD
**輸出**：3 種風格的開發信（簡短/詳細/提問式）

### 5. 面試問題 (Interview)
**觸發詞**：面試問題、interview、怎麼問
**輸入**：JD、履歷（可選）
**輸出**：履歷深挖問題、技術問題、行為問題

### 6. JD 生成 (JD Generator)
**觸發詞**：寫 JD、生成職缺、Job Description
**輸入**：職稱、技能、資歷、文化
**輸出**：正式版（104 格式）+ 社群版（帶 emoji）

### 7. 候選人摘要 (Summary)
**觸發詞**：候選人摘要、整理履歷、推薦報告
**輸入**：履歷、JD（可選）、備註（可選）
**輸出**：姓名、現職、經驗、亮點、動機、期望薪資

### 8. 推薦信 (Recommend Email)
**觸發詞**：推薦信、發給客戶、推人選
**輸入**：候選人摘要、JD、客戶名稱
**輸出**：Email 主旨 + 正文

## Prompt 模板

所有模板存放在：`references/prompts.md`

## 自動化工具

### 1. 104 職缺搜尋
**位置**：`scripts/scraper-104.py`
**用途**：搜尋 104 人力銀行職缺，找出哪些公司在招人
**使用**：
```bash
python3 scripts/scraper-104.py "backend engineer" 20
```

### 2. GitHub 人才搜尋
**位置**：`scripts/github-talent-search.py`
**用途**：搜尋 GitHub 開發者，提取聯絡資訊
**使用**：
```bash
python3 scripts/github-talent-search.py taipei python 10
# 參數: 地點 程式語言 數量
```
**搜尋語法**：
- `location:taipei language:python` - Taipei 的 Python 工程師
- `location:taiwan repos:>10 followers:>50` - 活躍台灣開發者

### 3. 批量履歷匹配
**位置**：`scripts/batch-match.py`
**用途**：將多份履歷與 JD 批量比對，自動計算匹配度
**匹配度門檻**：
- 🟢 ≥90%：高匹配，建議直接推
- 🟡 70-89%：中匹配，需顧問確認
- ⚪ <70%：低匹配，放履歷池

**使用**：
```python
from batch_match import batch_match, match_resume_to_jd
result = match_resume_to_jd(resume_text, jd_text)
```

### 4. 履歷格式化模板
**位置**：`templates/resume-format.md`
**用途**：統一履歷格式，支援匿名化處理
**代號規則**：`{職能}-{年份}-{序號}`（如 BE-2026-001）

### 5. 總覽看板
**位置**：`scripts/dashboard.py`
**用途**：追蹤所有案子狀態，顯示 Pipeline 進度
**使用**：
```bash
python3 scripts/dashboard.py           # 顯示看板
python3 scripts/dashboard.py telegram  # 輸出 Telegram 格式
python3 scripts/dashboard.py add-job "客戶名" "職缺名"
python3 scripts/dashboard.py add-candidate "候選人名"
```

### 6. 自動跟進排程
**位置**：`scripts/auto-followup.py`
**用途**：自動設定各階段跟進提醒
**跟進規則**：
- 推薦後：3天、7天、14天
- 面試後：1天、3天
- Offer：每天
- 報到後：Day1、Week1、Month1、90天（保證期）

**使用**：
```bash
python3 scripts/auto-followup.py status   # 查看狀態
python3 scripts/auto-followup.py today    # 今天待辦
python3 scripts/auto-followup.py telegram # Telegram 格式
python3 scripts/auto-followup.py schedule CAN-001 "王小明" "ABC科技" recommended
```

## 目錄結構

```
headhunter/
├── SKILL.md                    # 技能說明
├── references/
│   └── prompts.md              # Prompt 模板
├── scripts/
│   ├── scraper-104.py          # 104 職缺爬蟲
│   ├── github-talent-search.py # GitHub 人才搜尋
│   ├── batch-match.py          # 批量履歷匹配
│   ├── dashboard.py            # 總覽看板
│   └── auto-followup.py        # 自動跟進排程
└── templates/
    └── resume-format.md        # 履歷格式化模板
```

## 資料目錄

```
~/clawd/data/headhunter/
├── jobs.json         # 職缺資料
├── candidates.json   # 候選人資料
├── followups.json    # 跟進排程
└── dashboard.json    # 看板快照
```
