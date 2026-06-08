<div align="center">
<img src="https://capsule-render.vercel.app/api?type=waving&color=0:0f0c29,50:302b63,100:24243e&height=200&section=header&text=Adam&fontSize=80&fontColor=ffffff&fontAlignY=38&desc=AI%20%2F%20LLM%20Engineer&descSize=22&descAlignY=60&descColor=a78bfa" width="100%"/>
</div>

<div align="center">

[![Typing SVG](https://readme-typing-svg.demolab.com?font=Fira+Code&size=18&pause=1000&color=A78BFA&center=true&vCenter=true&width=650&lines=RAG+pipelines+%E2%80%94+prototype+to+evaluated+system;Dialogue+agents+with+tool+calling+%2B+memory;Prompt+versioning+with+measurable+quality+delta;LLM+evaluation+frameworks+from+scratch)](https://git.io/typing-svg)

</div>

---

## 📌 Key Metrics (measured in testing, not production)

> Все цифры получены в рамках тестовых прогонов. Отмечено явно — это прототипы, не продакшн-системы.

| Метрика | RAG Assistant | Sales Assistant | Prompt Eval System |
|---|---|---|---|
| **Faithfulness (RAGAS)** | 0.61 → **0.91** (+49%) | — | — |
| **Avg latency / query** | ~2.8s | ~3.2s | ~1.1s |
| **Cost / request** | ~$0.0003 | ~$0.0004 | ~$0.0002 |
| **Eval dataset size** | 14 кейсов | 10 диалогов | 20 кейсов |
| **Prompt versions tracked** | 3 | 3 | 4 стратегии |
| **LLM Judge** | ✅ RAGAS | ✅ Pydantic | ✅ двухуровневый |

*Cost estimate based on GPT-4o-mini pricing ($0.15/1M input, $0.60/1M output) at ~500–800 input + ~200 output tokens per query.*

---

## 🧠 О себе

Строю LLM-системы **с измеримым результатом** — не "работает", а "работает, и вот данные".

Что умею решать:
- 🔗 **Агентные системы** — LangGraph ReAct, function calling, multi-turn диалоги
- 📚 **RAG-пайплайны** — от чанкинга до RAGAS evaluation, итеративное улучшение промптов
- 📐 **Structured output** — Pydantic-схемы, LLM judge, воспроизводимые эксперименты
- 🏦 **Финтех-контекст** — консультирование по банковским продуктам, квалификация лидов

**Принцип работы:** каждый проект содержит реальные метрики, обоснование технологических решений с trade-offs, задокументированные fail cases и честные ограничения.

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
| **Stack selection rationale** | Groq (inference speed + free tier) · ChromaDB (zero-infra для MVP) · OpenAI Embeddings (качество retrieval) |

---

## 🚀 Проекты

---

### 🤖 AI Sales Assistant — LangGraph ReAct Agent

**Зачем это нужно бизнесу:** снижает нагрузку на операторов на типовых консультациях по продуктам МСБ — агент отвечает на стандартные запросы самостоятельно, передавая оператору только нестандартные кейсы.

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
ConversationBufferMemory  ←──  сохранение контекста диалога
    │
    ▼
Pydantic LLM Judge  →  JSONL log  →  quality metrics
```

**Архитектурные решения:**
- **ReAct вместо chain** — агент сам решает порядок вызова инструментов, цепочка бы сломалась на нестандартных запросах
- **Groq вместо OpenAI** — latency ~3× ниже на inference, бесплатный tier с `GroqKeyManager` для ротации ключей
- **ChromaDB вместо Qdrant** — достаточно для MVP, zero-infra, легко заменяется при масштабировании

**Fail cases (задокументированы в проекте):**
- агент «застревает» в петле при противоречивых требованиях клиента
- `qualify_client()` даёт false positive при неполных входных данных
- ChromaDB даёт нерелевантный retrieval при коротких (< 5 слов) запросах

[![Repo](https://img.shields.io/badge/GitHub-sales--assistant-181717?style=flat-square&logo=github)](https://github.com/AdamParadiseGr/sales-assistant)
`LangGraph` `ChromaDB` `Function Calling` `LLM Evaluation` `Fintech`

---

### 📄 RAG Assistant — Document Q&A with Iterative Prompt Improvement

**Зачем это нужно бизнесу:** автоматизирует ответы на вопросы по внутренним документам — договорам, регламентам, продуктовым условиям. Снижает время на поиск информации оператором с минут до секунд.

```
Document ingestion:  PDF → chunks → OpenAI Embeddings → ChromaDB

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
Prompt v3 (CoT + Pydantic)
    │
    ▼
RAGAS Evaluation  →  faithfulness / relevancy / context precision
```

**Эволюция промптов:**

| Версия | Изменение | Faithfulness |
|---|---|---|
| v1 baseline | стандартный RAG-промпт | 0.61 |
| v2 grounding | negative constraint + source anchoring | 0.78 (+28%) |
| v3 CoT + Pydantic | chain-of-thought + structured output | **0.91 (+49%)** |

**Архитектурные решения:**
- **MMR вместо similarity search** — устраняет дублирование контекста при похожих чанках
- **Query decomposition** — сложные составные вопросы разбиваются перед retrieval, иначе релевантность падает
- **OpenAI Embeddings вместо локальных** — качество retrieval важнее экономии на этом этапе

**Fail cases (задокументированы в проекте):**
- faithfulness падает до 0.65 на вопросах, требующих синтеза информации из 3+ чанков
- query decomposition избыточен для простых вопросов — добавляет ~0.8s latency без пользы
- RAGAS не покрывает галлюцинации формата (числа, даты) — нужна отдельная проверка

[![Repo](https://img.shields.io/badge/GitHub-RAG--Assistant-181717?style=flat-square&logo=github)](https://github.com/AdamParadiseGr/RAG-Assistant)
`RAG` `RAGAS` `Prompt Versioning` `Query Decomposition` `ChromaDB`

---

### 📊 AI Prompt Evaluation System — A/B тестирование стратегий промптинга

**Зачем это нужно бизнесу:** даёт воспроизводимую методологию выбора лучшего промпта — вместо "нам кажется, этот лучше" появляется "вот данные двухуровневой оценки".

```
4 prompt strategies  ×  20 banking test cases
        │
        ▼
Level 1: Rule-based scoring  (format, completeness, response structure)
        │
        ▼
Level 2: LLM-as-judge        (semantic quality, banking relevance)
        │
        ▼
SQLite storage  +  Typer CLI  +  результаты в JSON
```

**Ключевой инсайт:** Structured output выиграл по rule-based метрикам (высокий format compliance), но CoT победил после добавления LLM-judge по семантическому качеству. Это и есть ценность двухуровневой оценки — один слой метрик вводит в заблуждение.

[![Repo](https://img.shields.io/badge/GitHub-ai--prompt--evaluation--system-181717?style=flat-square&logo=github)](https://github.com/AdamParadiseGr/ai-prompt-evaluation-system)
`Prompt Engineering` `A/B Testing` `LLM Judge` `SQLite` `Typer CLI`

---

### 📡 Telegram Lead Parser — Real-time квалификация лидов

**Зачем это нужно бизнесу:** автоматически фильтрует нецелевые обращения из Telegram, снижая время менеджера на обработку входящих лидов.

```
Telegram channels
    │
    ▼
Telethon listener  →  keyword pre-filter  (быстрый, без LLM)
    │
    ▼
Groq Llama classifier  →  relevance score + category
    │
    ▼
if score > threshold:  Telegram Bot  →  авто-офер менеджеру
```

**Архитектурные решения:**
- **keyword pre-filter перед LLM** — отсекает ~70% нерелевантных сообщений без LLM-вызова, экономит токены
- **Groq Llama вместо GPT-4o** — для бинарной классификации достаточно, latency критична для real-time

[![Repo](https://img.shields.io/badge/GitHub-tg--lead--parser--public-181717?style=flat-square&logo=github)](https://github.com/AdamParadiseGr/tg-lead-parser-public)
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
