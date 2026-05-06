# qwen-fast-coder-4080 🤖⚡

Ультимативный Modelfile и конфигурация для **Qwen 2.5 Coder 14B**, оптимизированные под связку **NVIDIA RTX 4080** и **AMD Ryzen 7 9800X3D** 💻🔧.

Этот конфиг решает проблему медленной генерации («долго думает»), обеспечивая **100 % GPU offloading**, поддержку **32k контекста** и корректную работу инструментов (Tools) в Claude Code и Aider.

## 🎯 Особенности ✨

*   **100 % GPU Offloading** 🦾: Модель полностью помещается в 16 ГБ VRAM (с учётом контекста), максимально используя мощь RTX 4080.
*   **32k Context Window** 📜: Оптимизировано для работы с большими файлами и целыми репозиториями — больше не нужно дробить код на части!
*   **Инженерный промпт** 🛠️: Модель жёстко приучена использовать ChatML и обратные кавычки (```), что критично для функции Apply в IDE.
*   **Поддержка Tools** 🔌: Настроенный шаблон для вызова инструментов локальными агентами (Aider, Claude Code, Continue).
*   **Высокая скорость** 🚀: Оптимизация под топовое железо даёт максимальную скорость генерации токенов.

## 🛠 Пререквизиты 🧰

*   **Ollama**: последняя версия (установите с [официального сайта](https://ollama.ai)).
*   **Железо**: Минимум 16 ГБ видеопамяти (идеально: RTX 4080 / 4070 Ti Super).
*   **Софт**: [uv](https://github.com/astral-sh/uv) для быстрого запуска Python‑инструментов (опционально, но рекомендуется).
*   **Окружение**: Git Bash (для пользователей Windows) или любой современный терминал (macOS/Linux).
*   **Место на диске**: ~15–20 ГБ для модели в формате Q4_K_M.

## 📦 Установка и сборка 🏗️

1.  **Клонируйте репозиторий**:

    ```bash
    git clone https://github.com/ai-warevo/qwen-fast-coder-4080
    cd qwen-fast-coder-4080
    ```

2.  **Создайте локальную модель**:

    ```bash
    ollama create qwen-fast -f Modelfile
    ```

3.  **Проверьте статус**:

    Запустите модель и убедитесь в `ollama ps`, что она занимает ~12–13 ГБ и использует GPU на 100 %.

    ```bash
    ollama run qwen-fast
    # В другом терминале:
    ollama ps
    ```

## 💻 Использование 🖥️

### 1. Aider (лучший выбор для авторедактирования) 🪄

Запуск через `uvx` в режиме архитектора (наиболее стабильный вариант для локальных моделей):

```bash
OLLAMA_API_BASE=http://localhost:11434 uvx --from aider-chat aider.exe --model ollama/qwen-fast --no-gitignore --architect
```

### 2. Claude Code (Anthropic CLI) 🤖

Для работы через Git Bash на Windows используйте следующую команду (укажите ваш путь к `bash.exe`):

```bash
CLAUDE_CODE_GIT_BASH_PATH="G:\git\bin\bash.exe" claude --model ollama/qwen-fast
```

### 3. VS Code (Continue) 🧩

Добавьте модель в ваш `config.json` расширения Continue:

[config.json](.continue/config.json)


## ⚙️ Технические детали (Modelfile) ⚙�️

Ключевые настройки для максимальной производительности:

*   `use_mlock`: запрещает выгрузку модели в медленную RAM — всё работает из быстрой VRAM.
*   `num_thread 16`: использует все потоки 9800X3D для обработки промпта.
*   `num_ctx 32768`: баланс между объёмом памяти проекта и скоростью RTX 4080.
*   `gpu_layers 40`: переносит 40 слоёв модели на GPU для ускорения вычислений.
*   `temperature 0.2`: небольшая «креативность» без потери детерминированности.

## 🗂️ Настройка окружения (.env) 💼

Для удобства управления переменными окружения добавьте файл `.env` в корень проекта. Это особенно полезно, если вы используете разные модели или работаете в команде.

1. Создайте файл `.env`:

    ```bash
    touch .env
    ```

2. Добавьте в него следующие переменные (подставьте свои значения):

    ```env
    # Ollama API endpoint
    OLLAMA_API_BASE=http://localhost:11434

    # Path to Git Bash (Windows only)
    CLAUDE_CODE_GIT_BASH_PATH="G:\\git\\bin\\bash.exe"

    # Model name for use in scripts
    MODEL_NAME=qwen-fast

    # Context window size (shared across tools)
    NUM_CTX=32768

    # Max tokens to predict
    NUM_PREDICT=4096

    # Temperature for generation
    TEMPERATURE=0.2
    ```

3. **Использование в скриптах**:

   Чтобы использовать переменные из `.env`, установите утилиту `direnv` (рекомендуется) или загружайте их вручную в вашем shell‑скрипте:

   **Пример для bash‑скрипта (`run-aider.sh`)**:

    ```bash
    #!/bin/bash
    source .env

    $OLLAMA_API_BASE uvx --from aider-chat aider.exe \
      --model ollama/$MODEL_NAME \
      --no-gitignore \
      --architect
    ```

---

🚀 **Разработано для тех, кто ценит скорость и приватность.** Быстро, мощно, локально!

Если у вас есть вопросы или предложения по улучшению — смело открывайте Issue! 🤝
