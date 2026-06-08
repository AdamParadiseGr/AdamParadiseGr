<div align="center">
<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f0c29,50:302b63,100:24243e&height=200&section=header&text=Adam&fontSize=80&fontColor=ffffff&fontAlignY=38&desc=AI%20%2F%20LLM%20Engineer&descSize=22&descAlignY=60&descColor=a78bfa" width="100%"/>
</div>

<div align="center">

[![Typing SVG](https://readme-typing-svg.demolab.com?font=Fira+Code&size=18&pause=1000&color=A78BFA&center=true&vCenter=true&width=650&lines=RAG+pipelines+%E2%80%94+prototype+to+evaluated+system;Dialogue+agents+with+tool+calling+%2B+memory;Prompt+versioning+with+measurable+quality+delta;LLM+evaluation+frameworks+from+scratch)](https://git.io/typing-svg)

</div>

---

## 🧠 О себе

Строю LLM-системы **с измеримым результатом** — не "работает", а "работает, и вот данные".

Что умею решать:
- 🔗 **Агентные системы** — LangGraph ReAct, function calling, multi-turn диалоги
- 📚 **RAG-пайплайны** — от чанкинга до RAGAS evaluation, итеративное улучшение промптов
- 📐 **Structured output** — Pydantic-схемы, LLM judge, воспроизводимые эксперименты
- 🏦 **Финтех-контекст** — консультирование по банковским продуктам, квалификация лидов

**Принцип работы:** каждый проект содержит реальные метрики из логов, обоснование технологических решений с trade-offs, задокументированные fail cases.

---

## ⚙️ Стек

<div align="center">

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![LangChain](https://img.shields.io/badge/LangChain-1C3C3C?style=for-the-badge&logo=langchain&logoColor=white)
![OpenAI](https://img.shields.io/badge/OpenAI_GPT--4o-412991?style=for-the-badge&logo=openai&logoColor=white)
![Groq](https://img.shields.io/badge/Groq_Llama-FF6B35?style=for-the-badge&logo=meta&logoColor=white)
![ChromaDB](https://img.shields.io/badge/ChromaDB-FF6B6B?style=for-the-badge&logo=databricks&logoColor=white)
![Pydantic](https://img.shields.io/badge/Pydantic-E92063?style=for-the-badge&logo=pydantic&logoColor=white)

</div>

| Область | Технологии |
|---|---|
| **LLM Orchestration** | LangGraph · LangChain · ReAct · Function Calling · Tool Use |
| **RAG** | ChromaDB · OpenAI Embeddings · MMR Retrieval · Query Decomposition |
| **Evaluation** | RAGAS · Pydantic LLM Judge · двухуровневая оценка · JSONL Logging |
| **Prompt Engineering** | Zero-shot → CoT → Expert Persona · версионирование · A/B тестирование |

---

## 🚀 Проекты

---

### 🤖 [AI Sales Assistant](https://github.com/AdamParadiseGr/sales-assistant) — LangGraph ReAct Agent

**Зачем:** снижает нагрузку на операторов на типовых консультациях — агент закрывает стандартные запросы самостоятельно, передавая нестандартные кейсы человеку.

```
User query
    │
    ▼
LangGraph ReAct Agent
    ├── Tool: product_search()    → ChromaDB semantic search
    ├── Tool: calculate_terms()   → бизнес-логика расчёта условий
    └── Tool: qualify_client()    → проверка соответствия критериям
    │
    ▼
ConversationBufferMemory  (multi-turn контекст)
    │
    ▼
Pydantic LLM Judge  →  JSONL log
```

**Почему такие технологии:**
- **ReAct вместо chain** — агент сам выбирает порядок инструментов, цепочка сломалась бы на нестандартных запросах
- **Groq вместо OpenAI inference** — меньше latency, бесплатный tier покрывает прототип
- **ChromaDB вместо Qdrant** — zero-infra для MVP, заменяется при необходимости масштабирования

**Fail cases (в проекте):**
- агент зависает в петле при противоречивых требованиях клиента
- `qualify_client()` даёт false positive при неполных входных данных
- ChromaDB теряет релевантность на коротких запросах (< 5 слов)

`LangGraph` `ChromaDB` `Function Calling` `LLM Evaluation`

---

### 📄 [RAG Assistant](https://github.com/AdamParadiseGr/RAG-Assistant) — Document Q&A with Iterative Prompt Improvement

**Зачем:** автоматизирует ответы по внутренним документам — договорам, регламентам, продуктовым условиям.

```
Document ingestion:
PDF → chunks → OpenAI Embeddings → ChromaDB

Query pipeline:
User query
    │
    ▼
Query Decomposition  →  sub-queries
    │
    ▼
MMR Retrieval  →  top-k diverse chunks  (без дублей)
    │
    ▼
Versioned prompt (v1 → v2 → v3)
    │
    ▼
RAGAS Evaluation  →  faithfulness / relevancy / context precision
```

**Эволюция промптов — 3 версии, результаты в репо:**

| Версия | Изменение |
|---|---|
| v1 | baseline RAG-промпт |
| v2 | negative constraint + source anchoring |
| v3 | chain-of-thought + Pydantic structured output |

**Почему такие технологии:**
- **MMR вместо similarity search** — устраняет дублирование контекста при похожих чанках
- **Query decomposition** — составные вопросы разбиваются перед retrieval, иначе релевантность падает
- **OpenAI Embeddings** — качество retrieval важнее экономии на этом этапе

**Fail cases (в проекте):**
- качество падает на вопросах, требующих синтеза из 3+ чанков одновременно
- query decomposition избыточен для простых вопросов — добавляет latency без пользы
- RAGAS не ловит галлюцинации в числах и датах — нужна отдельная проверка

`RAG` `RAGAS` `Prompt Versioning` `Query Decomposition` `ChromaDB`

---

### 📊 [AI Prompt Evaluation System](https://github.com/AdamParadiseGr/ai-prompt-evaluation-system) — A/B тестирование стратегий промптинга

**Зачем:** воспроизводимая методология выбора промпта — вместо "нам кажется, этот лучше" появляются данные двухуровневой оценки.

```
4 стратегии  ×  20 банковских тест-кейсов
        │
        ▼
Level 1: Rule-based scoring  (format, completeness)
        │
        ▼
Level 2: LLM-as-judge        (semantic quality, relevance)
        │
        ▼
SQLite  +  Typer CLI  +  JSON export
```

**Ключевой инсайт:** Structured output выиграл по rule-based метрикам (format compliance), но CoT победил после LLM-judge по семантическому качеству. Один слой метрик вводит в заблуждение — это и есть главный вывод системы.

`Prompt Engineering` `A/B Testing` `LLM Judge` `SQLite` `Typer CLI`

---

### 🔬 [Tool-Calling-Agent](https://github.com/AdamParadiseGr/Tool-Calling-Agent) — Tool Calling Agent на чистом Anthropic API

**Зачем:** демонстрирует работу агентного цикла без фреймворков — LangChain не используется, только нативный Anthropic API. Понимание механики важнее абстракций.

```
User query
    │
    ▼
Claude claude-sonnet (Anthropic API напрямую)
    │
    ▼
Tool use loop:
    ├── get_stock_price()     → финансовые данные
    ├── calculate_metrics()   → P/E, ROI, волатильность
    └── compare_companies()   → сравнительный анализ
    │
    ▼  (loop until no more tool calls)
Final structured response
```

**Почему без LangChain:**
- LangChain абстрагирует agentic loop — здесь он реализован вручную, что показывает понимание механики
- 3 версии system prompt с задокументированными изменениями и причинами каждого

**Fail cases (в проекте):**
- агент вызывает лишние инструменты при простых запросах — проблема system prompt v1
- без явного stop condition модель продолжает вызывать инструменты без необходимости

`Anthropic API` `Tool Calling` `Agentic Loop` `Financial Analysis` `No LangChain`

---

### 📡 [Telegram Lead Parser](https://github.com/AdamParadiseGr/tg-lead-parser-public) — Real-time квалификация лидов

**Зачем:** автоматически фильтрует нецелевые обращения из Telegram, снижая время менеджера на обработку входящих.

```
Telegram channels
    │
    ▼
Telethon listener  →  keyword pre-filter  (без LLM-вызова)
    │
    ▼
Groq Llama classifier  →  relevance score + category
    │
    ▼
score > threshold  →  Telegram Bot  →  уведомление / авто-офер
```

**Почему keyword pre-filter перед LLM:** отсекает нерелевантные сообщения без LLM-вызова — экономит токены и снижает latency на очевидных кейсах.

`Telethon` `Groq` `Real-time Classification` `Telegram Bot` `Render`

---

## 📬 Связь

<div align="center">

[![Telegram](https://img.shields.io/badge/Telegram-@somnium_001-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/somnium_001)
[![Email](https://img.shields.io/badge/shirinovit@yandex.ru-D14836?style=for-the-badge&logo=gmail&logoColor=white)](shirinovit@yandex.ru)

</div>

<div align="center">
<img src="https://capsule-render.vercel.app/api?type=waving&color=0:24243e,50:302b63,100:0f0c29&height=100&section=footer" width="100%"/>
</div>
