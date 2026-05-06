# qwen-fast-coder-4080 🤖⚡

Ультимативный Modelfile и конфигурация для **Qwen 2.5 Coder 14B**, оптимизированные под связку **NVIDIA RTX 4080** и **AMD Ryzen 7 9800X3D** 💻🔧.

Этот конфиг решает проблему медленной генерации («долго думает»), обеспечивая **100 % GPU offloading**, поддержку **32k контекста** и корректную работу инструментов (Tools) в Claude Code, Aider и **VS Code (с поддержкой Spec‑Kit)**.

## 🎯 Особенности ✨

*   **100 % GPU Offloading** 🦾: Модель полностью помещается в 16 ГБ VRAM (с учётом контекста), максимально используя мощь RTX 4080.
*   **32k Context Window** 📜: Оптимизировано для работы с большими файлами и целыми репозиториями — больше не нужно дробить код на части!
*   **Spec‑Kit Ready** 🗂️: Модель «запрограммирована» следовать методологии Spec‑Driven Development (SDD): `constitution → specify → clarify → plan → checklist → tasks → analyze → implement`.
*   **Инженерный промпт** 🛠️: Модель жёстко приучена использовать ChatML и обратные кавычки (```), что критично для функции Apply в IDE.
*   **Поддержка Tools** 🔌: Настроенный шаблон для вызова инструментов локальными агентами (Aider, Claude Code, Continue).
*   **Высокая скорость** 🚀: Оптимизация под топовое железо даёт максимальную скорость генерации токенов.

## 🛠 Пререквизиты 🧰

*   **Ollama**: последняя версия (установите с [официального сайта](https://ollama.ai)).
*   **Железо**: Минимум 16 ГБ видеопамяти (идеально: RTX 4080 / 4070 Ti Super).
*   **Софт**: [uv](https://github.com/astral-sh/uv) для быстрого запуска Python‑инструментов (опционально, но рекомендуется).
*   **Окружение**: Git Bash (для пользователей Windows) или любой современный терминал (macOS/Linux).
*   **Место на диске**: ~15–20 ГБ для модели в формате Q4_K_M.
*   **VS Code**: с установленным расширением [Continue](https://marketplace.visualstudio.com/items?itemName=continue.continue).

## 📦 Установка и сборка 🏗️

1.  **Клонируйте репозиторий**:
    ```bash
    git clone https://github.com/ai-warevo/qwen-fast-coder-4080
    cd qwen-fast-coder-4080
    ```

2.  **Создайте локальную модель**:
    ```bash
    ollama create qwen-fast-coder-4080 -f Modelfile
    ```

3.  **Проверьте статус**:
    Запустите модель и убедитесь в `ollama ps`, что она занимает ~12–13 ГБ и использует GPU на 100 %.
    ```bash
    ollama run qwen-fast-coder-4080
    # В другом терминале:
    ollama ps
    ```

## 💻 Использование 🖥️

### 1. Aider (лучший выбор для авторедактирования) 🪄

Запуск через `uvx` в режиме архитектора (наиболее стабильный вариант для локальных моделей):
```bash
OLLAMA_API_BASE=http://localhost:11434 uvx --from aider-chat aider.exe --model ollama/qwen-fast-coder-4080 --no-gitignore --architect
```

### 2. Claude Code (Anthropic CLI) 🤖

Для работы через Git Bash на Windows используйте следующую команду (укажите ваш путь к `bash.exe`):
```bash
CLAUDE_CODE_GIT_BASH_PATH="G:\git\bin\bash.exe" claude --model qwen-fast-coder-4080
```

### 3. VS Code (Continue + Spec‑Kit) 🧩


**Шаг 1.** Установите расширение **Continue** из Marketplace VS Code.

**Шаг 2.** Убедитесь, что в корне вашего проекта есть папка `.continue` с файлом `config.json`. Конфигурация уже подготовлена в репозитории:
```
.continue/config.json
```

**Шаг 3.** Откройте чат Continue в VS Code (`Ctrl+Shift+P` → «Continue: Open Chat»).

**Шаг 4.** Начните работу с любой команды Spec‑Kit, например: `/speckit.constitution`.

## ⚙️ Технические детали (Modelfile) ⚙�️

Ключевые настройки для максимальной производительности и работы по методологии Spec‑Kit:

*   `use_mlock`: запрещает выгрузку модели в медленную RAM — всё работает из быстрой VRAM.
*   `num_thread 16`: использует все потоки 9800X3D для обработки промпта.
*   `num_ctx 32768`: баланс между объёмом памяти проекта и скоростью RTX 4080.
*   `num_predict 4096`: максимальное количество токенов для генерации за один запрос.
*   `temperature 0.1`: минимальная «креативность» для сохранения детерминированности на этапах Spec‑Kit.
*   `top_p 0.9`: ограничивает выборку 90 % наиболее вероятных токенов.
*   `repeat_penalty 1.15`: снижает вероятность повторения фраз.
*   `frequency_penalty 0.3`: снижает вероятность повторения отдельных токенов.
*   **Spec‑Kit System Prompt**: Встроенный в модель промпт заставляет её следовать методологии на каждом этапе взаимодействия.

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
    MODEL_NAME=ollama/qwen-fast-coder-4080

    # Context window size (shared across tools)
    NUM_CTX=32768

    # Max tokens to predict
    NUM_PREDICT=4096

    # Temperature for generation
    TEMPERATURE=0.1
    ```

3. **Использование в скриптах**:
   Чтобы использовать переменные из `.env`, установите утилиту `direnv` (рекомендуется) или загружайте их вручную в вашем shell‑скрипте.


   **Пример для bash‑скрипта (`run-aider.sh`)**:
    ```bash
    #!/!/bin/bash
    source .env

    $OLLAMA_API_BASE uvx --from aider-chat aider.exe \
      --model ollama/$MODEL_NAME \
      --no-gitignore \
      --architect
    ```

---

🚀 **Разработано для тех, кто ценит скорость и приватность.** Быстро, мощно, локально!


Если у вас есть вопросы или предложения по улучшению — смело открывайте Issue! 🤝
