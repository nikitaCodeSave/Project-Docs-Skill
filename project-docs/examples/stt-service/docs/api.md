# API: Audio Transcription Service

## Общие сведения

**Команда:** `stt`
**Версия:** 1.0.0

## Глобальные опции

| Флаг | Описание |
|------|----------|
| `--help, -h` | Справка |
| `--version, -V` | Версия |
| `--verbose, -v` | Подробный вывод |

---

## Команды

### stt transcribe

Транскрипция аудиофайла в текст.

**Синтаксис:**
```bash
stt transcribe <FILE> [OPTIONS]
```

**Аргументы:**
| Аргумент | Тип | Обязательный | Описание |
|----------|-----|--------------|----------|
| `FILE` | path | Да | Путь к аудиофайлу |

**Опции:**
| Флаг | Тип | По умолчанию | Описание |
|------|-----|--------------|----------|
| `--language, -l` | string | auto | Язык аудио (ru, en, auto) |
| `--output, -o` | path | ./{filename} | Путь для результата |
| `--format, -f` | string | json | Форматы вывода (json,txt,srt) |
| `--model, -m` | string | large-v3 | Модель (tiny, base, small, medium, large-v3) |
| `--no-diarize` | flag | false | Отключить определение спикеров |

**Примеры:**
```bash
# Базовая транскрипция
stt transcribe meeting.mp3

# С указанием языка и выходного файла
stt transcribe interview.wav --language ru --output ./results/interview

# Множественные форматы
stt transcribe podcast.m4a --format json,srt,txt

# Быстрая обработка без diarization
stt transcribe memo.mp3 --model base --no-diarize
```

---

### stt batch

Пакетная обработка директории с аудиофайлами.

**Синтаксис:**
```bash
stt batch <DIRECTORY> [OPTIONS]
```

**Аргументы:**
| Аргумент | Тип | Обязательный | Описание |
|----------|-----|--------------|----------|
| `DIRECTORY` | path | Да | Директория с аудиофайлами |

**Опции:**
| Флаг | Тип | По умолчанию | Описание |
|------|-----|--------------|----------|
| `--output-dir, -o` | path | ./output | Директория для результатов |
| `--format, -f` | string | json | Форматы вывода |
| `--pattern, -p` | glob | *.mp3,*.wav,*.flac,*.m4a,*.ogg | Паттерн файлов |
| `--continue-on-error` | flag | true | Продолжать при ошибке файла |

**Примеры:**
```bash
# Обработка всех файлов в директории
stt batch ./recordings/

# С указанием паттерна и формата
stt batch ./audio/ --pattern "*.wav" --format json,srt

# Остановка при первой ошибке
stt batch ./files/ --continue-on-error=false
```

---

### stt info

Информация о системе и доступных моделях.

**Синтаксис:**
```bash
stt info [OPTIONS]
```

**Опции:**
| Флаг | Тип | По умолчанию | Описание |
|------|-----|--------------|----------|
| `--models` | flag | false | Показать загруженные модели |
| `--gpu` | flag | false | Информация о GPU |

**Примеры:**
```bash
# Общая информация
stt info

# Проверка GPU
stt info --gpu
```

---

## Коды возврата

| Код | Константа | Описание |
|-----|-----------|----------|
| 0 | SUCCESS | Успешное выполнение |
| 1 | ERROR_FILE | Файл не найден или повреждён |
| 2 | ERROR_ARGS | Ошибка аргументов |
| 3 | ERROR_MODEL | Модель не загружена |
| 4 | ERROR_GPU | GPU недоступен (для large моделей) |
| 5 | ERROR_PROCESSING | Ошибка обработки |

---

## Переменные окружения

| Переменная | Описание | По умолчанию |
|------------|----------|--------------|
| `STT_MODEL_PATH` | Путь к моделям | `~/.cache/whisperx/` |
| `STT_DEFAULT_LANGUAGE` | Язык по умолчанию | auto |
| `STT_DEVICE` | Устройство (cuda/cpu) | auto |

---

## Конфигурация

**Путь:** `~/.config/stt/config.yaml`

```yaml
default_model: large-v3
default_language: auto
output_format: json
diarization: true
```

---

## Формат вывода

Подробное описание выходных форматов см. в [data-formats.md](data-formats.md).
