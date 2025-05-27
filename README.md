## 🔍 Введение

**Visual RAG Agent** — это интеллектуальный агент, реализующий семантический поиск по PDF-документам с использованием технологий визуального извлечения знаний. В основе системы лежит подход **Visual Retrieval-Augmented Generation**, объединяющий визуальные эмбеддинги, хранение в векторной базе **Vespa** и генеративные возможности модели **Gemma**.

Проект разработан для автоматического извлечения знаний из PDF-презентаций, научных публикаций и других документов с визуальным содержимым. На основе текстового запроса агент:
1. определяет релевантные страницы в документах с помощью векторного поиска,
2. извлекает изображения этих страниц,
3. анализирует их с помощью мультимодальной LLM, чтобы предоставить точный и обоснованный ответ.

Это решение применимо в задачах корпоративного поиска, аналитики документов, интеллектуальных помощников и образовательных систем.

# 🧠 Visual RAG Agent with Vespa & ColPali

Этот проект представляет собой **AI-агента для семантического поиска по PDF-документам**, реализованного по архитектуре **Visual RAG** (Retrieval-Augmented Generation). Используются встраивания изображений от ColPali и поисковый движок Vespa. Агент способен отвечать на сложные запросы, комбинируя извлечённые изображения и LLM (Gemma).

## 📌 Основные возможности

- 📥 Загрузка и разбор PDF-документов.
- 🖼️ Конвертация страниц в изображения (`pdf2image`) и извлечение текста (`pypdf`).
- 🔎 Извлечение эмбеддингов изображений с помощью модели `ColPali`.
- 💾 Сжатие эмбеддингов и загрузка в векторную базу Vespa.
- 🤖 Интерактивный агент: принимает текстовый запрос, ищет по базе и возвращает ответ через Gemma (via Ollama).

---

## ⚙️ Установка

1. Установите зависимости:

```bash
pip install colpali-engine==0.3.1 vidore_benchmark==4.0.0 pdf2image google-generativeai pypdf==5.0.1 pyvespa vespacli requests
```

2. Установите `poppler` для корректной работы `pdf2image`:

```bash
# Ubuntu
sudo apt install poppler-utils
# MacOS (через Homebrew)
brew install poppler
```

---

## 🚀 Быстрый старт

1. Убедитесь, что у вас есть модель ColPali:

```python
from colpali_engine.models import ColPali, ColPaliProcessor
model = ColPali.from_pretrained("vidore/colpali-v1.3")
processor = ColPaliProcessor.from_pretrained("vidore/colpali-v1.3")
```

2. Скачайте и преобразуйте PDF:

```python
from pdf2image import convert_from_path
from pypdf import PdfReader
```

3. Извлеките эмбеддинги страниц, используя `ColPali`.

4. Запишите результаты в формат Vespa JSON и загрузите в запущенный контейнер Vespa.

5. Запустите поиск по текстовому запросу и визуализируйте найденные страницы с помощью `Gemma` и `Ollama`.

---

## 📦 Запуск Vespa локально через Docker

```bash
docker run -d --name vespa -m 4g --cpus=2 \
  -p 8080:8080 -p 19071:19071 \
  -v /path/to/visionrag:/vespa-app \
  vespaengine/vespa:8
docker exec vespa vespa-deploy prepare /vespa-app
docker exec vespa vespa-deploy activate
```

---

## 🧠 Пример запроса

```python
user_q = "описание алгоритма Apriori и как рассчитать support, confidence и lift"
process_query_with_gemma_vespa(user_q)
```

Gemma возвращает подробный структурированный ответ на основе изображений страниц, извлечённых из PDF.

---

## 📁 Структура проекта

- `agent.ipynb` — основной ноутбук, содержащий пайплайн от загрузки PDF до ответа на запрос.
- `ColPali`, `pdf2image`, `pypdf` — для обработки данных и создания эмбеддингов.
- `vespa.application`, `pyvespa` — для создания схемы, загрузки и поиска в Vespa.
- `Ollama + Gemma` — для генерации ответа на основе изображений.

---

## 📌 Примечания

- Для запуска требуется локальный Ollama с моделью `gemma3:4b-it-q4_K_M`.
- Все вычисления можно выполнять на GPU, если доступно.
- Бинаризация векторов позволяет значительно снизить объём хранения и ускорить поиск.

---

## 📄 Лицензии

- ColPali: HuggingFace + Vidore
- Vespa: Apache License 2.0
- Gemma: [условия от Google](https://ai.google.dev/gemma/terms)
