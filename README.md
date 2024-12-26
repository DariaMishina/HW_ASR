# Проект по распознаванию речи (ASR)

<p align="center">
  <a href="#о-проекте">О проекте</a> •
  <a href="#установка"">Установка</a> •
  <a href="#как-использовать">Как использовать</a> •
  <a href="#дополнительные-возможности">Дополнительные возможности</a> •
  <a href="#вспомогательные-функции">Вспомогательные функции</a> •
  <a href="#как-запустить-инференс-на-финальной-модели">Как запустить инференс на финальной модели</a> •
  <a href="#метрики-wer-и-cer-для-финальной-модели">Метрики WER и CER для финальной модели</a> •
</p>


## О проекте

Этот репозиторий содержит пример решения задачи автоматического распознавания речи (ASR). 
Задание можно найти здесь. [here](https://github.com/NickKar30/SpeechAI/tree/main/hw2). 

## Установка

Проделайте шаги ниже:

0. Создайте и активируйте новое окружение через `venv`.

   ```bash
   # создание окружения
   python3 -m venv venv


   # активация окружения
   source venv/bin/activate
   ```

1. Установите необходимые библиотеки

   ```bash
   pip install -r requirements.txt
   ```

2. Установите `pre-commit`

   ```bash
   pre-commit install
   ```

## Как использовать

Для обучения модели:

```bash
python3 train.py -cn=CONFIG_NAME HYDRA_CONFIG_ARGUMENTS
```

Где `CONFIG_NAME` — это название конфигурации из папки `src/configs`, а `HYDRA_CONFIG_ARGUMENTS` — необязательные аргументы.

Перед запуском необходимо указать API-ключ для `W&B`. Сделать это можно двумя способами:

1. Через терминал:

   ```bash
   export WANDB_API_KEY=ХХХХХХХ
   echo $WANDB_API_KEY  # проверка переменной окружения
   ```
В файле wandb.yaml пропишите:

   ```bash
   api_key: ${oc.env:WANDB_API_KEY}

   ```
2. Указать ключ напрямую в файле wandb.yaml:

   ```bash
   api_key: "ХХХХХХХ"
   ```

Для инференса:

```bash
python3 inference.py HYDRA_CONFIG_ARGUMENTS
```

## Как воспроизвести лучшую модель

```bash
python3 train.py -cn=train_argmax
```

Логи обучения с использованием варианта train_argmax.yaml доступны [по ссылке.](https://wandb.ai/dariamishina2812/ASR_HW/runs/m3fo4txc/logs)
обучение с конфигурацией `-cn=train` требует 6 часов на GPU, c `-cn=train_argmax` - около 2х часов на GPU.

[Ссылка на `W&B`](https://wandb.ai/dariamishina2812/ASR_HW?nw=nwuserdariamishina2812)


## Дополнительные возможности

- Реализован декодер BeamSearch CTC с использованием предварительно обученной языковой модели (LM)
- Реализована BPE токенизация

## Вспомогательные функции

- *calc_wer_cer.py* вычисляет и выводит метрики (WER и CER) 
- *download_best_model.py* загружает лучшую модель по указанному пути (`-path custom/path/to/model.pth`) или по умолчанию в `data/models/best_model.pth`


## Как запустить инференс на финальной модели

```bash
python3 inference.py -cn=inference
```

в **inference.yaml** есть параметр *from_pretrained*. Если значение *from_pretrained* не равно None, оно указывает путь к модели, на которой будет выполняться инференс. По умолчанию *from_pretrained* указывает на путь к лучшей модели.

Параметр *metrics* содержит конфигурацию метрик, которые будут использоваться для оценки. По умолчанию используется *bs_lm_metrics*(предобученная LM с BeamSearch).

## Метрики WER и CER для финальной модели

test-clean CER_(Argmax): **0.07**
test-clean WER_(Argmax): **0.23**

Использование LM с BeamSearch значительно улучшало результаты:

test-clean CER_(LM-BeamSearch): **0.06**
test-clean WER_(LM-BeamSearch): **0.16**
