# BoardRecognizer (VLM + OCR)

Пайплайн для распознавания содержимого маркерных досок:
- **OCR строк** (CER/WER) по датасету `Text_dataset_lines_for_CER_WER`.
- **Детекция схем** (прямоугольники и стрелки) на полноразмерных кадрах из `dataset_for_BR` при помощи **GigaChat** (vision через `/files` + `/chat/completions`).
- Визуализация и метрики качества. Результаты складываются в `results_bord/`.

## Структура проекта

```
BOARDRECOGNIZER/
├─ dataset_for_BR/
├─ Text_dataset_lines_for_CER_WER/
├─ results_bord/
│  ├─ vis/
│  ├─ vis_detect/
│  ├─ detected_arrows.csv
│  ├─ detected_rectangles.csv
│  ├─ metrics_simple_per_image.csv
│  └─ metrics_simple_summary.csv
├─ arrows.csv
├─ rectangles.csv
├─ text.csv
├─ dataset_text_lines_CER_WER.csv
├─ main.ipynb
└─ .env
```

## Быстрый старт

1. **Python-окружение**
   ```bash
   conda create -n br_env python=3.10 -y
   conda activate br_env
   pip install opencv-python-headless numpy pandas matplotlib requests tqdm
   pip install gigachat
   ```

2. **Заполни `.env`**
   ```dotenv
   AUTHORIZATION_KEY=YOUR_KEY
   GIGACHAT_MODEL=GigaChat-2-Max
   VERIFY=false
   ```

3. **Запусти `main.ipynb`** — шаг за шагом.

## Как получить `AUTHORIZATION_KEY` и токен

Перейти на сайт https://developers.sber.ru/docs/ru/gigachat/quickstart/ind-create-project и следовать инструкции


## Метрики

### OCR
- **CER (Character Error Rate)** — доля ошибок по символам.
- **WER (Word Error Rate)** — доля ошибок по словам.

### Детекция схем
- **IoU (Intersection over Union)** — это метрика пересечения областей (часто используется для оценки качества детекции объектов, рамок и сегментаций).
- **Нормированная ошибка концов** (англ. Normalized Arrow End Error, NAEE) — это мера, которая показывает, насколько точно модель определила начало и конец стрелки относительно истинных координат, нормированная (т.е. приведённая) к размеру изображения.
- **Precision / Recall / F1** при пороге IoU ≥ 0.5 для прямоугольников.
- **Precision / Recall / F1** при нормированной ошибке концов ≤ 0.03 для стрелок.

**Precision, Recall и F1** выбраны, потому что они **позволяют оценить качество детекции даже при несбалансированных данных**, отражают **и точность локализации (precision), и полноту обнаружения (recall)**, а F1 служит универсальной интегральной метрикой для сравнения моделей.