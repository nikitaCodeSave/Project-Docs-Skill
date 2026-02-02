# Data Formats: Audio Transcription Service

## Версия формата
**Текущая:** 1.0
**Совместимость:** 1.0+

---

## Входные форматы

### Аудиофайлы

**Поддерживаемые форматы:**

| Расширение | MIME-type | Описание |
|------------|-----------|----------|
| `.wav` | audio/wav | PCM аудио |
| `.mp3` | audio/mpeg | MPEG Audio Layer III |
| `.flac` | audio/flac | Lossless сжатие |
| `.m4a` | audio/mp4 | AAC аудио |
| `.ogg` | audio/ogg | Ogg Vorbis |

**Ограничения:**

| Параметр | Значение | Описание |
|----------|----------|----------|
| Макс. размер | 2 GB | Размер файла |
| Макс. длительность | 4 часа | Длина аудио |
| Мин. sample rate | 8000 Hz | Частота дискретизации |
| Каналы | 1-2 | Моно/стерео |

**Валидация:**
- Файл должен существовать и быть читаемым
- Расширение должно соответствовать формату
- Файл должен содержать аудиопоток

---

## Выходные форматы

### JSON (TranscriptionResult)

**Расширение:** `.json`
**MIME-type:** application/json
**Назначение:** Машиночитаемый формат с полными метаданными

**Schema:**
```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "version": {
      "type": "string",
      "description": "Версия формата (1.0)"
    },
    "metadata": {
      "type": "object",
      "properties": {
        "source_file": { "type": "string" },
        "duration_seconds": { "type": "number" },
        "language": { "type": "string" },
        "model": { "type": "string" },
        "processed_at": { "type": "string", "format": "date-time" }
      },
      "required": ["source_file", "duration_seconds", "language", "model", "processed_at"]
    },
    "segments": {
      "type": "array",
      "items": { "$ref": "#/$defs/Segment" }
    }
  },
  "required": ["version", "metadata", "segments"],
  "$defs": {
    "Segment": {
      "type": "object",
      "properties": {
        "start": { "type": "number", "description": "Начало в секундах" },
        "end": { "type": "number", "description": "Конец в секундах" },
        "text": { "type": "string", "description": "Транскрипция" },
        "speaker": { "type": ["string", "null"], "description": "ID спикера (SPEAKER_00, SPEAKER_01, ...)" },
        "confidence": { "type": "number", "minimum": 0, "maximum": 1 }
      },
      "required": ["start", "end", "text"]
    }
  }
}
```

**Поля:**

| Поле | Тип | Обязательное | Описание |
|------|-----|--------------|----------|
| `version` | string | Да | Версия формата |
| `metadata` | object | Да | Метаданные обработки |
| `metadata.source_file` | string | Да | Имя исходного файла |
| `metadata.duration_seconds` | number | Да | Длительность в секундах |
| `metadata.language` | string | Да | Определённый язык (ru, en) |
| `metadata.model` | string | Да | Использованная модель |
| `metadata.processed_at` | string | Да | ISO 8601 timestamp |
| `segments` | array | Да | Массив сегментов |
| `segments[].start` | number | Да | Начало сегмента (секунды) |
| `segments[].end` | number | Да | Конец сегмента (секунды) |
| `segments[].text` | string | Да | Текст сегмента |
| `segments[].speaker` | string\|null | Нет | ID спикера или null |
| `segments[].confidence` | number | Нет | Уверенность [0..1] |

**Пример:**
```json
{
  "version": "1.0",
  "metadata": {
    "source_file": "meeting.mp3",
    "duration_seconds": 3600.5,
    "language": "ru",
    "model": "large-v3",
    "processed_at": "2024-01-15T10:30:00Z"
  },
  "segments": [
    {
      "start": 0.0,
      "end": 3.5,
      "text": "Добрый день, начинаем совещание.",
      "speaker": "SPEAKER_00",
      "confidence": 0.95
    },
    {
      "start": 3.8,
      "end": 7.2,
      "text": "Здравствуйте, готов представить отчёт.",
      "speaker": "SPEAKER_01",
      "confidence": 0.92
    }
  ]
}
```

---

### TXT (Plain Text)

**Расширение:** `.txt`
**MIME-type:** text/plain
**Назначение:** Простой текст для чтения

**Формат:**
- Без временных меток
- Спикеры указаны в квадратных скобках (если diarization включён)
- Один абзац на смену спикера

**Пример:**
```
[SPEAKER_00] Добрый день, начинаем совещание.

[SPEAKER_01] Здравствуйте, готов представить отчёт.
```

**Без diarization:**
```
Добрый день, начинаем совещание. Здравствуйте, готов представить отчёт.
```

---

### SRT (SubRip Subtitle)

**Расширение:** `.srt`
**MIME-type:** application/x-subrip
**Назначение:** Субтитры для видеоплееров

**Формат:**
```
{номер}
{HH:MM:SS,mmm} --> {HH:MM:SS,mmm}
{текст}

```

**Пример:**
```
1
00:00:00,000 --> 00:00:03,500
[SPEAKER_00] Добрый день, начинаем совещание.

2
00:00:03,800 --> 00:00:07,200
[SPEAKER_01] Здравствуйте, готов представить отчёт.
```

---

## Коды ошибок (в JSON)

При ошибке stderr содержит JSON:

```json
{
  "error": {
    "code": "ERROR_FILE",
    "message": "File not found: meeting.mp3",
    "details": {}
  }
}
```

| Код | Описание |
|-----|----------|
| `ERROR_FILE` | Файл не найден или недоступен |
| `ERROR_FORMAT` | Неподдерживаемый формат |
| `ERROR_CORRUPTED` | Файл повреждён |
| `ERROR_TOO_LARGE` | Превышен лимит размера |
| `ERROR_TOO_LONG` | Превышен лимит длительности |
| `ERROR_PROCESSING` | Ошибка при транскрипции |
