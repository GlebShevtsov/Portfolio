---
# For reference on model card metadata, see the spec: https://github.com/huggingface/hub-docs/blob/main/modelcard.md?plain=1
# Doc / guide: https://huggingface.co/docs/hub/model-cards
{}
---

# Model Card for Model ID

<!-- Provide a quick summary of what the model is/does. -->

Ссылка на модель: https://huggingface.co/GlebShevtsov/tlite-ft-gguf

Учебный проект. \
Дообученная и квантизированная с Lora-адаптерами модель [T-lite](https://huggingface.co/AnatoliiPotapov/T-lite-instruct-0.1) на собственном датасете, для вычленения ключевой информации о деятельности контрагента по описанию с собственного сайта контрагента.

## Dataset

Для обучения был использован собственный датасет очищенных текстов с сайтов контрагентов об описании их деятельности и вручную созданные краткие описания деятельности.\
Чтобы модель не потеряла изначальный функционал помимо собственного датасета был также использован [alpaca датасет](https://huggingface.co/datasets/freQuensy23/ru-alpaca-cleaned)

## Evaluation

Для измерения производительности модели использовались метрики семейства [rouge](https://huggingface.co/spaces/evaluate-metric/rouge).\
Дообученная модель сравнивалась с базовой стратегией (брать первые предложения из текста с сайта контрагента в разделе "о компании")

|               | rouge1 | rougeL | rougeLsum |
| :--- | :---: | :---: | :---: |
| Baseline | 6.19 | 6.18 | 6.12 |
| T-lite без дообучения | 5.50 | 5.53 | 5.57 |
| T-lite fine-tuned | 7.53 | 7.50 | 7.47 |

Таким образом модель показала лучшие результаты, чем без дообучения и чем базовая стратегия.

## Examples of usage

Для запуска квантизированной модели нужно установить llama-cpp

```
pip install llama-cpp-python
```
Далее можно использовать стандартный пайплайн
```python
from llama_cpp import Llama

llm = Llama.from_pretrained(
    repo_id="SanyaP4elkin/tlite-ft-gguf",
    filename="*tlite-ft-model-Q4_K_M.gguf",
    verbose=False
)

comp_descr = input()

output = llm(
    f"Q: По тексту с сайта компании вкратце опиши, чем она занимается\n{comp_descr}",
    max_tokens = 1000
)

print(output)
```