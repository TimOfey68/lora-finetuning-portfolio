<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Дообучение стиля общения LLM-модели (Fine-tuning с LoRA)</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
    line-height: 1.6;
    color: #24292f;
    background: #ffffff;
    max-width: 900px;
    margin: 40px auto;
    padding: 0 24px;
  }
  h1 {
    font-size: 2em;
    border-bottom: 1px solid #d0d7de;
    padding-bottom: 0.3em;
    margin-bottom: 16px;
    margin-top: 24px;
  }
  h2 {
    font-size: 1.5em;
    border-bottom: 1px solid #d0d7de;
    padding-bottom: 0.3em;
    margin-top: 32px;
    margin-bottom: 16px;
  }
  h3 {
    font-size: 1.25em;
    margin-top: 24px;
    margin-bottom: 12px;
  }
  p { margin-bottom: 16px; }
  ul {
    padding-left: 2em;
    margin-bottom: 16px;
  }
  li { margin-bottom: 6px; }
  strong { font-weight: 600; }
  code {
    font-family: ui-monospace, SFMono-Regular, "SF Mono", Menlo, Consolas, monospace;
    background: #f6f8fa;
    padding: 2px 6px;
    border-radius: 6px;
    font-size: 0.9em;
  }
  pre {
    background: #f6f8fa;
    border: 1px solid #d0d7de;
    border-radius: 6px;
    padding: 16px;
    overflow-x: auto;
    margin-bottom: 16px;
    font-size: 0.9em;
  }
  pre code {
    background: none;
    padding: 0;
    border-radius: 0;
  }
  blockquote {
    border-left: 4px solid #d0d7de;
    padding: 4px 16px;
    margin: 16px 0;
    color: #57606a;
    background: #f6f8fa;
    border-radius: 0 6px 6px 0;
  }
  hr {
    border: none;
    border-top: 1px solid #d0d7de;
    margin: 32px 0;
  }
  .tree {
    font-family: ui-monospace, SFMono-Regular, "SF Mono", Menlo, Consolas, monospace;
    white-space: pre;
    line-height: 1.5;
  }
  .meta {
    color: #57606a;
    font-size: 0.95em;
  }
  .emoji { font-style: normal; }
</style>
</head>
<body>

<h1> Дообучение стиля общения LLM-модели (Fine-tuning с LoRA)</h1>

<p>Учебный кейс для портфолио, демонстрирующий адаптацию тональности (Tone of Voice) открытой языковой модели под конкретные задачи с использованием технологии LoRA (Low-Rank Adaptation).</p>

<h2>💼 О проекте и бизнес-ценность</h2>

<p>Дообучение (fine-tuning) позволяет «вшить» правила и стиль общения напрямую в веса модели. Это решает ключевые бизнес-задачи:</p>

<ul>
  <li><strong>Экономия ресурсов:</strong> Устраняет необходимость передавать длинные системные промпты и примеры при каждом запросе, экономя токены и время.</li>
  <li><strong>Стабильность бренда:</strong> Гарантирует единый корпоративный стиль (дружелюбный, официальный, с юмором) во всех автоматизированных ответах.</li>
  <li><strong>Локальность и безопасность:</strong> Весь процесс (от обучения до генерации) выполняется на локальном оборудовании без передачи данных во внешние API.</li>
</ul>

<h2>🛠 Технологический стек</h2>

<ul>
  <li><strong>Язык:</strong> Python</li>
  <li><strong>Фреймворки:</strong> PyTorch, Hugging Face (<code>transformers</code>, <code>peft</code>, <code>accelerate</code>, <code>datasets</code>)</li>
  <li><strong>Метод оптимизации:</strong> LoRA (дообучение только адаптеров без изменения базовых весов)</li>
</ul>

<h2>📂 Структура проекта</h2>

<pre class="tree"><code>.
── fine_tuning/
│   └── train.py          # Скрипт дообучения модели с LoRA
├── inference/
│   └── chat.py           # Скрипт интерактивного чата с дообученной моделью
├── example_dataset.json  # Учебный датасет (87 пар вопрос-ответ)
├── requirements.txt      # Зависимости проекта
└── README.md             # Документация проекта</code></pre>

<h2>🚀 Быстрый старт</h2>

<h3>1. Установка</h3>

<p>Рекомендуется использовать виртуальное окружение (venv):</p>

<pre><code class="language-bash">python -m pip install --upgrade pip
python -m pip install -r requirements.txt</code></pre>

<p>Для авторизации в Hugging Face:</p>

<pre><code class="language-bash">hf auth login</code></pre>

<h3>2. Дообучение (Fine-tuning)</h3>

<p>Запуск обучения на процессоре (CPU). Процесс занимает ~15-30 минут в зависимости от железа:</p>

<pre><code class="language-bash">python fine_tuning/train.py --model_name "sberbank-ai/rugpt3small" --dataset_path "example_dataset.json" --output_dir "fine_tuning/lora_model_cpu" --num_train_epochs 3 --per_device_train_batch_size 1 --gradient_accumulation_steps 1 --device cpu</code></pre>

<p>Для запуска на GPU добавьте флаг <code>--use_4bit</code> и <code>--device cuda</code>.</p>

<h3>3. Запуск чата (Инференс)</h3>

<p>Проверка работы модели после обучения:</p>

<pre><code class="language-bash">python inference/chat.py --base_model "sberbank-ai/rugpt3small" --lora_model "fine_tuning/lora_model_cpu"</code></pre>

<h2>📊 Результаты работы</h2>

<p><img src="Result-).jpg" alt="Результат работы модели" style="max-width: 100%; border: 1px solid #d0d7de; border-radius: 6px;"></p>

<h2>⚠️ Важное примечание к результату</h2>

<blockquote>
<p>В данном учебном кейсе используется ультра-легкая демонстрационная модель (~50 МБ), не имеющая глубокого предобучения на русском языке. Поэтому генерируемый текст может содержать фактические неточности или артефакты.</p>
<p><strong>Главная цель проекта</strong> — успешная демонстрация корректной работы пайплайна fine-tuning, снижения функции потерь (Loss) и изменения паттернов генерации. В реальных production-задачах для достижения высокого качества используются модели большего размера (от 500 МБ) в комбинации с RAG-системами.</p>
</blockquote>

<h2>📚 Что такое Fine-tuning?</h2>

<p>Fine-tuning — это процесс дообучения уже предобученной нейросети на специализированном наборе данных для адаптации под узкие задачи:</p>

<ul>
  <li>Улучшения стиля общения</li>
  <li>Соблюдения корпоративного tone of voice</li>
  <li>Понимания специфической терминологии</li>
</ul>

<p><strong>Важно:</strong> Fine-tuning не исправляет ошибки в фактах — для этого используются RAG-системы. Основная цель — корректное соблюдение тональности, правил и стиля при генерации.</p>

<h2> Рекомендации по датасету</h2>

<p>Рекомендованные объёмы для fine-tuning:</p>

<ul>
  <li><strong>100–300 пар</strong> — минимальный объём для первичной проверки результата</li>
  <li><strong>500–1000 пар</strong> — оптимальный объём для стабильного и качественного поведения модели</li>
  <li><strong>2000+ пар</strong> — профессиональный уровень</li>
</ul>

<p>В текущем примере используется 87 пар — этого достаточно для демонстрационного кейса с небольшой моделью.</p>

<hr>

<p class="meta">
  <strong>Автор:</strong> TimOfey68<br>
  <strong>Курс:</strong> Промпт-инжиниринг 3.0 — Карьера на фрилансе<br>
  <strong>Кейс:</strong> PEcf10 — Портфолио: дообучение стиля общения
</p>

</body>
</html>
