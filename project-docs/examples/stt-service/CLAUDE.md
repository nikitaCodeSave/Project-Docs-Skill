# Audio Transcription Service

Локальный сервис транскрипции аудио в текст с разделением по спикерам.

## Контекст проекта

Сервис решает проблему конфиденциальности при транскрипции. Облачные решения требуют отправки данных на внешние серверы, что неприемлемо для конфиденциальных записей. Целевая аудитория — разработчики и аналитики, работающие с CLI.

## Документация

| Документ | Когда читать |
|----------|--------------|
| [docs/PRD.md](docs/PRD.md) | Бизнес-цели и метрики успеха |
| [docs/SPEC.md](docs/SPEC.md) | Функциональные требования, acceptance criteria |
| [docs/api.md](docs/api.md) | CLI интерфейс, команды, флаги |
| [docs/data-formats.md](docs/data-formats.md) | JSON/SRT/TXT форматы |
| [docs/decisions/](docs/decisions/) | Почему выбран WhisperX |

## Ключевые решения

| Решение | Обоснование | ADR |
|---------|-------------|-----|
| WhisperX | Единый pipeline, 70x realtime | [ADR-001](docs/decisions/ADR-001-whisperx.md) |
| Локальные модели | Работа офлайн | [ADR-002](docs/decisions/ADR-002-local-models.md) |

## Технологический стек

- **STT Engine**: WhisperX
- **Audio**: ffmpeg
- **Runtime**: Python 3.10+

## Ограничения

- GPU обязателен для large-моделей (8+ GB VRAM)
- Только batch-режим
- Первоначальная загрузка требует интернет

## Границы для AI

### ✅ DO
- Следовать docs/SPEC.md
- Использовать acceptance criteria
- Создавать ADR при выборе технологий

### ❌ DO NOT
- Не менять STT engine без нового ADR
- Не добавлять облачные зависимости
- Не реализовывать real-time (out of scope)
- Не реализовывать web-интерфейс (out of scope)
