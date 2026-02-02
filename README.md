# Скилл «Проектная документация» / Project-Docs Skill

Скилл для создания структурированной документации перед стартом разработки. Шаг 0 — превращает идею в технические документы, понятные AI-агентам.

Основан на принципах [Anthropic Claude Code documentation](https://docs.anthropic.com/en/docs/claude-code) и [MADR 4.0](https://adr.github.io/madr/).

---

## 📋 Содержание

- [Быстрый старт](#быстрый-старт)
- [Что делает скилл](#что-делает-скилл)
- [Процесс работы](#процесс-работы)
- [Философия](#философия)
- [Структура документации](#структура-документации)
- [Установка по платформам](#установка-по-платформам)
  - [Claude Code / Claude.ai](#claude-code--claudeai)
  - [Cursor](#cursor)
  - [Gemini CLI](#gemini-cli)
  - [OpenAI Codex CLI](#openai-codex-cli)
  - [VS Code + GitHub Copilot](#vs-code--github-copilot)
  - [Другие агенты](#другие-агенты)
- [Универсальный способ](#универсальный-способ)
- [Структура файлов скилла](#структура-файлов-скилла)
- [Лицензия](#лицензия)
- [Благодарности](#благодарности)

---

## Быстрый старт

### Вариант 1: Клонирование репозитория

```bash
git clone https://github.com/nikitaCodeSave/Project-Docs-Skill.git
cd Project-Docs-Skill
```

Все команды ниже выполняются из этой директории.

### Вариант 2: Скачать только нужные файлы

```bash
# Создаём структуру директорий
mkdir -p project-docs/templates project-docs/examples/stt-service/docs/decisions

# Основные файлы
curl -Lo project-docs/SKILL.md https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/SKILL.md
curl -Lo project-docs/checklist.md https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/checklist.md

# Шаблоны
curl -Lo project-docs/templates/CLAUDE.md.template https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/templates/CLAUDE.md.template
curl -Lo project-docs/templates/PRD.md.template https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/templates/PRD.md.template
curl -Lo project-docs/templates/SPEC.md.template https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/templates/SPEC.md.template
curl -Lo project-docs/templates/ADR.md.template https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/templates/ADR.md.template
curl -Lo project-docs/templates/API.md.template https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/templates/API.md.template
curl -Lo project-docs/templates/DATA-FORMATS.md.template https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/templates/DATA-FORMATS.md.template

# Примеры (stt-service)
curl -Lo project-docs/examples/stt-service/README.md https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/examples/stt-service/README.md
curl -Lo project-docs/examples/stt-service/CLAUDE.md https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/examples/stt-service/CLAUDE.md
curl -Lo project-docs/examples/stt-service/docs/_discovery-log.md https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/examples/stt-service/docs/_discovery-log.md
curl -Lo project-docs/examples/stt-service/docs/_prd-v1-draft.md https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/examples/stt-service/docs/_prd-v1-draft.md
curl -Lo project-docs/examples/stt-service/docs/PRD.md https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/examples/stt-service/docs/PRD.md
curl -Lo project-docs/examples/stt-service/docs/SPEC.md https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/examples/stt-service/docs/SPEC.md
curl -Lo project-docs/examples/stt-service/docs/api.md https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/examples/stt-service/docs/api.md
curl -Lo project-docs/examples/stt-service/docs/data-formats.md https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/examples/stt-service/docs/data-formats.md
curl -Lo project-docs/examples/stt-service/docs/decisions/ADR-001-whisperx.md https://raw.githubusercontent.com/nikitaCodeSave/Project-Docs-Skill/main/project-docs/examples/stt-service/docs/decisions/ADR-001-whisperx.md
```

---

## Что делает скилл

Создаёт набор документов, которые помогают AI-агенту понять проект ДО написания кода:

| Документ | Назначение |
|----------|------------|
| **CLAUDE.md** | Точка входа — навигация, контекст, границы для AI |
| **PRD.md** | Бизнес-требования — зачем, для кого, метрики успеха |
| **SPEC.md** | Функциональные требования — что делает система |
| **ADR-*.md** | Архитектурные решения — почему выбрали технологию X |
| **api.md** | Контракты интерфейсов — CLI, REST, форматы |
| **data-formats.md** | Структуры данных — JSON-схемы, примеры |

**Разница:**

> **До:** «Сделай мне сервис транскрипции» → агент выбирает произвольные технологии, структуру, API
>
> **После:** PRD + SPEC + ADR → агент следует зафиксированным требованиям и ограничениям

---

## Процесс работы

Скилл описывает не только **формат** документов, но и **процесс** их создания:

```
Discovery → PRD → ⏸️ валидация → SPEC → ⏸️ валидация → ADR → CLAUDE.md
```

### Ключевые принципы процесса

| Принцип | Описание |
|---------|----------|
| **Discovery First** | Не пиши документы, пока не проведёшь интервью с заказчиком |
| **Validation Loops** | После каждого документа — СТОП и подтверждение от заказчика |
| **MVP Scope** | Начинай с минимума, расширяй после валидации |
| **CLAUDE.md последним** | Он агрегирует решения из ADR |

Подробнее см. секцию "Процесс работы" в [SKILL.md](project-docs/SKILL.md).

---

## Философия

### Документация как инструкция для AI

Исследования показывают: структурированные спецификации повышают качество генерируемого кода на 71%, а явные требования сокращают циклы доработки на 68%.

### Ключевые принципы

| Принцип | Описание |
|---------|----------|
| **Разделение абстракций** | PRD (зачем) → SPEC (что) → ADR (почему так) → Код (как) |
| **Модульность** | Маленькие файлы экономят токены контекстного окна |
| **Отложенные решения** | Детали реализации фиксируются в ADR, не в SPEC |
| **Проверяемость** | Каждое требование имеет acceptance criteria (GIVEN/WHEN/THEN) |
| **Эксплицитность** | AI нужны явные границы, примеры, ограничения |

### Что нужно AI vs людям

| AI-агенты требуют | Люди предпочитают |
|-------------------|-------------------|
| Эксплицитные инструкции | Концептуальное понимание |
| Конкретные примеры I/O | Высокоуровневые обзоры |
| Чёткие границы и запреты | Гибкость интерпретации |
| Термины с определениями | Подразумеваемый контекст |

---

## Структура документации

После использования скилла в вашем проекте появится:

```
project/
├── CLAUDE.md                  # Точка входа для Claude Code
└── docs/
    ├── PRD.md                 # Бизнес-требования (зачем, для кого)
    ├── SPEC.md                # Функциональная спецификация (что)
    ├── api.md                 # Контракты интерфейсов
    ├── data-formats.md        # Форматы входных/выходных данных
    └── decisions/             # Architecture Decision Records
        ├── ADR-001-*.md
        └── ADR-002-*.md
```

### Порядок создания документов

> **Важно:** Сначала discovery, потом документация. Не генерируй "правдоподобные" документы — выясняй реальные требования.

| Этап | Документ | Действие |
|------|----------|----------|
| 0 | — | **Discovery:** интервью с заказчиком |
| 1 | PRD.md | Бизнес-цели, scope → ⏸️ валидация |
| 2 | SPEC.md | Функциональные требования → ⏸️ валидация |
| 2 | api.md, data-formats.md | Детализация (если нужно) |
| 3 | ADR-*.md | Технические решения |
| 4 | **CLAUDE.md** | Точка входа (создаётся **последним**) |

**Почему CLAUDE.md последним?** Он содержит "Технологический стек" и "Ключевые решения", которые берутся из ADR.

---

## Установка по платформам

### Claude Code / Claude.ai

Скилл использует формат [Agent Skills](https://agentskills.io) — открытый стандарт для AI-агентов.

> **Примечание:** Команды ниже предполагают, что ты находишься в корне склонированного репозитория.

#### Персональный скилл (для всех проектов)

```bash
mkdir -p ~/.claude/skills
cp -r project-docs ~/.claude/skills/
```

#### Проектный скилл (для команды)

В корне своего проекта:

```bash
mkdir -p .claude/skills
cp -r /путь/к/Project-Docs-Skill/project-docs .claude/skills/
git add .claude/
git commit -m "Добавлен скилл проектной документации"
```

Или через временное клонирование (без локальной копии репо):

```bash
git clone --depth 1 https://github.com/nikitaCodeSave/Project-Docs-Skill.git /tmp/project-docs-skill && \
  mkdir -p .claude/skills && \
  cp -r /tmp/project-docs-skill/project-docs .claude/skills/ && \
  rm -rf /tmp/project-docs-skill
git add .claude/
git commit -m "Добавлен скилл проектной документации"
```

Claude автоматически подхватит скилл при запросах типа «создай документацию для проекта», «подготовь PRD», «создай SPEC».

---

### Cursor

```bash
git clone --depth 1 https://github.com/nikitaCodeSave/Project-Docs-Skill.git /tmp/project-docs-skill && \
  mkdir -p .cursor/skills && \
  cp -r /tmp/project-docs-skill/project-docs .cursor/skills/ && \
  rm -rf /tmp/project-docs-skill
```

---

### Gemini CLI

Gemini CLI использует файлы `GEMINI.md` для контекста.

```bash
git clone --depth 1 https://github.com/nikitaCodeSave/Project-Docs-Skill.git /tmp/project-docs-skill && \
  cp -r /tmp/project-docs-skill/project-docs . && \
  rm -rf /tmp/project-docs-skill
```

Создай `GEMINI.md` в корне проекта:

```markdown
# Контекст проекта

@./project-docs/SKILL.md
@./project-docs/checklist.md
@./project-docs/templates/CLAUDE.md.template
@./project-docs/templates/PRD.md.template
@./project-docs/templates/SPEC.md.template
@./project-docs/templates/ADR.md.template
@./project-docs/templates/API.md.template
@./project-docs/templates/DATA-FORMATS.md.template
```

---

### OpenAI Codex CLI

#### Глобальная установка

```bash
git clone --depth 1 https://github.com/nikitaCodeSave/Project-Docs-Skill.git /tmp/project-docs-skill && \
  mkdir -p ~/.codex/skills && \
  cp -r /tmp/project-docs-skill/project-docs ~/.codex/skills/ && \
  rm -rf /tmp/project-docs-skill
```

#### Для проекта

```bash
git clone --depth 1 https://github.com/nikitaCodeSave/Project-Docs-Skill.git /tmp/project-docs-skill && \
  mkdir -p .codex/skills && \
  cp -r /tmp/project-docs-skill/project-docs .codex/skills/ && \
  rm -rf /tmp/project-docs-skill
```

---

### VS Code + GitHub Copilot

VS Code с GitHub Copilot поддерживает Agent Skills (в preview).

1. Включи `chat.useAgentSkills` в настройках VS Code
2. В корне проекта:

```bash
git clone --depth 1 https://github.com/nikitaCodeSave/Project-Docs-Skill.git /tmp/project-docs-skill && \
  mkdir -p .github/skills && \
  cp -r /tmp/project-docs-skill/project-docs .github/skills/ && \
  rm -rf /tmp/project-docs-skill
```

---

### Другие агенты

Сначала скачай весь скилл:

```bash
git clone --depth 1 https://github.com/nikitaCodeSave/Project-Docs-Skill.git /tmp/project-docs-skill && \
  cp -r /tmp/project-docs-skill/project-docs . && \
  rm -rf /tmp/project-docs-skill
```

#### Windsurf

```bash
mkdir -p .windsurf/skills
cp -r project-docs .windsurf/skills/
```

#### Continue.dev

```bash
mkdir -p .continue/skills
cp -r project-docs .continue/skills/
```

#### Aider

Создай `.aider.conf.yml`:

```yaml
read:
  - project-docs/SKILL.md
  - project-docs/checklist.md
  - project-docs/templates/CLAUDE.md.template
  - project-docs/templates/PRD.md.template
  - project-docs/templates/SPEC.md.template
  - project-docs/templates/ADR.md.template
  - project-docs/templates/API.md.template
  - project-docs/templates/DATA-FORMATS.md.template
```

---

## Универсальный способ

Если твой AI-инструмент не поддерживает специальные форматы:

1. Скачай весь скилл: `git clone https://github.com/nikitaCodeSave/Project-Docs-Skill.git`
2. Прикрепи к диалогу файлы из `project-docs/`:
   - `SKILL.md` — основные инструкции
   - `checklist.md` — чеклист готовности
   - Нужные шаблоны из `templates/`
   - Примеры из `examples/` для понимания формата
3. Работает с любым LLM: ChatGPT, Claude, Gemini, и т.д.

---

## Структура файлов скилла

```
Project-Docs-Skill/               # Корень репозитория
├── README.md                     # Этот файл
└── project-docs/                 # Папка со скиллом
    ├── SKILL.md                  # Основной файл скилла (процесс + шаблоны)
    ├── checklist.md              # Чеклист готовности документации
    ├── templates/                # Шаблоны документов
    │   ├── CLAUDE.md.template    # Точка входа
    │   ├── PRD.md.template       # Бизнес-требования (+ MVP Scope)
    │   ├── SPEC.md.template      # Функциональные требования
    │   ├── ADR.md.template       # Архитектурные решения
    │   ├── API.md.template       # Контракты интерфейсов
    │   └── DATA-FORMATS.md.template  # Форматы данных
    └── examples/                 # Примеры использования
        └── stt-service/          # Пример: сервис транскрипции
            ├── README.md         # Описание итеративного процесса
            ├── CLAUDE.md
            └── docs/
                ├── _discovery-log.md     # Пример discovery-интервью
                ├── _prd-v1-draft.md      # Пример итерации PRD
                ├── PRD.md
                ├── SPEC.md
                ├── api.md
                ├── data-formats.md
                └── decisions/
                    └── ADR-001-whisperx.md
```

**Важно:** При копировании в свой проект копируй только папку `project-docs/`.

### Формат Agent Skills

Скилл соответствует открытому стандарту [Agent Skills](https://agentskills.io), который поддерживают:

- Claude Code / Claude.ai
- OpenAI Codex
- Cursor
- VS Code + GitHub Copilot
- И другие агенты

**Принцип:** пиши один раз, используй везде.

---

## Лицензия

MIT — используй как хочешь.

---

## Благодарности

- [Anthropic](https://anthropic.com) — Claude Code documentation best practices
- [MADR 4.0](https://adr.github.io/madr/) — Architecture Decision Records format
