# Indonesian LocalX Machine Translation

## Overview

Welcome to the documentation of the Indonesian LocalX Machine Translation project, an innovative endeavor aimed at developing a robust machine translation system for the diverse local languages of Indonesia. Given Indonesia's rich linguistic landscape, characterized by over 17,000 islands, 360 ethnic groups, and 840 regional languages, communication poses a significant challenge, hindering socialization and understanding among its people. Our project seeks to mitigate these challenges by creating an advanced translation engine designed to facilitate seamless daily interactions across the myriad of local languages.

## Datasets

We use datasets from [NusaX](https://huggingface.co/datasets/indonlp/NusaX-MT) and [CC100](https://metatext.io/datasets/cc100) for Sundanese and Javanese, as well as pretrained models from Transformers. The NusaX dataset includes 12 labeled languages, including Indonesian, English, and 10 regional Indonesian languages, namely Acehnese, Balinese, Banjarese, Bugis, Madurese, Minangkabau, Javanese, Ngaju, Sundanese, and Toba Batak. The CC100 dataset, which includes around 100 languages, but we just use Javanese and Sundanese for Translation on Javanese to Sundanese vise versa, given the limitations of the dataset for other regional languages on CC100.


## Exploratory Data Analysis (EDA)

<p align="center">
  <img src="https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/e09e7dd8-7b83-4a39-aa32-40560056550a" alt="EDA-english" width="300" />
  <img src="https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/c314833f-4eaa-4088-81b5-de8ff54ec1e6" alt="EDA-indonesian" width="300" />
  <img src="https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/138b90b9-9469-4ccb-9ad0-4f6466a832c4" alt="EDA-acehnese" width="300" />
  <img src="https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/2c4e0c08-b09f-403d-8c57-bea02ba36bac" alt="EDA-balinese" width="300" />
  <img src="https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/587ce208-795e-4f86-9dc9-9215e2ba758f" alt="EDA-banjarese" width="300" />
  <img src="https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/11ba4125-379c-4b57-9454-892b192dde80" alt="EDA-buginese" width="300" />
  <img src="https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/de9935e1-b827-4542-864c-de20dc0df221" alt="EDA-javanese" width="300" />
  <img src="https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/a31f129d-e2f4-4822-a357-7da909620023" alt="EDA-madurese" width="300" />
  <img src="https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/8147477f-83ca-483d-af8c-bb7232a9767d" alt="EDA-minangkabau" width="300" />
  <img src="https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/2002c4e2-81f3-4a75-a33a-bee11ab384db" alt="EDA-ngaju" width="300" />
  <img src="https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/c183bb25-830a-4add-b9ed-0d528f055665" alt="EDA-sundanese" width="300" />
  <img src="https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/9223824a-25f6-443d-90fb-6d69f125c96e" alt="EDA-toba_batak" width="300" />
</p>

## Training T5tokenizer

After several iterations and learning from initial setbacks, we concluded that training the T5 tokenizer was essential. The original T5 tokenizer lacked the vocabulary necessary for accurately capturing the nuances of Indonesian local languages. Therefore, we expanded the tokenizer's vocabulary by adding 1,000 new entries using the NusaX dataset, enhancing its ability to understand and process these languages.

## Dataset Pre-processing

Our preprocessing pipeline includes appending specific prefixes (e.g., "Translate Sundanese-Javanese:") to input texts to guide the T5 model in understanding the translation task. We divided the dataset into training, testing, and validation sets with an 80/10/10 split and analyzed the language distribution within these splits to ensure a balanced representation.

<p align="center">
  <img src="https://github.com/zanuura/Indonesian-LocalX-Machine-Translation/assets/73764446/d78ba9ba-ef9e-4743-950f-dc61bec537f6" alt="TRAIN" width="200" />
  <img src="https://github.com/zanuura/Indonesian-LocalX-Machine-Translation/assets/73764446/cac10a6a-efb3-49f7-9247-526fa28aaac9" alt="TEST" width="200" />
  <img src="https://github.com/zanuura/Indonesian-LocalX-Machine-Translation/assets/73764446/7b047509-bec5-42c9-94da-f8bb107d9cdc" alt="VAL" width="200" />
</p>

Subsequently, we tokenized the dataset using the newly trained T5 tokenizer and conducted tests to verify the functionality of the encoder and decoder.
```
encoded: {'input_ids': tensor([[ 3425,   128, 17921,  9309,   114,  5676,   137,  1510,  5676,   104,
           379,   707,  2711,   608,   108,   115,   262, 13179,   104,   430,
           104, 14590,   106,  1255,   122,   109,   106,   103,     1]]), 'attention_mask': tensor([[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
         1, 1, 1, 1, 1]])}
decoded: Translate Acehnese to Balinese : Lon cemburu meueu awak droen jeuet neurasa bahgia.</s>
```

## Model Development

In the process of creating the Indonesian LocalX Machine Translation engine, we prioritized efficiency and effectiveness. Our journey led us to adopt the T5-small model, renowned for its balance between performance and computational efficiency, making it an ideal choice for our project's goals.

### Selection of the Pretrained T5-Small Model

The decision to utilize the T5-small model was driven by its proven capabilities in understanding and generating human-like text. This model, while compact, packs a powerful punch, offering a sweet spot in terms of performance and resource requirements. Its architecture is designed to handle a variety of text-based tasks, which made it a compelling option for tackling the intricate nuances of Indonesian local languages.

### Unsupervised Learning Adaptations

Our venture into unsupervised learning techniques marked a significant pivot in our approach. We leveraged the CC100 dataset, focusing on Sundanese and Javanese languages, to train the T5-small model in an unsupervised manner. This strategy was born out of necessity, as our initial supervised learning attempts stumbled upon challenges, notably producing outputs that inadvertently blended unrelated languages, such as English, into the translations.

The unsupervised learning approach allowed the model to explore the linguistic structures and vocabularies of Sundanese and Javanese without explicit parallel text examples. By doing so, it aimed to develop an intrinsic understanding of the translation task, relying on the inherent similarities and differences between the languages.

[Notebook](machine-translation/T5_small_Unsupervised_Learning_CC100_SU_JV.ipynb)

### Challenges and Solutions

The journey was not without its hurdles. One significant challenge was the model's occasional inclination to generate translations that included fragments of languages not relevant to the task at hand. To mitigate this, we embarked on a series of iterative training and testing cycles, fine-tuning the model's parameters, and enhancing the pre-processing steps to better align with our translation objectives.

We also encountered limitations related to dataset quality and computational resources. These constraints sometimes hindered the model's ability to capture the full complexity of the languages involved. In response, we adopted a pragmatic approach, focusing on incremental improvements and seeking out additional datasets that could enrich the model's training environment.

### Forward Steps

Despite these challenges, the preliminary outcomes are promising. The model has demonstrated a capacity to facilitate translations between Sundanese and Javanese, albeit with room for refinement. Our ongoing efforts are directed towards expanding the dataset, incorporating more sophisticated unsupervised learning techniques, and exploring the potential of scaling up the model architecture.


## Predict

Our system was put to the test with translation tasks between Sundanese and Javanese. The evaluation metrics used were BLEU, METEOR, and BLEURT scores, which provided insights into the translation quality. Although the results indicate that our system's accuracy needs improvement, primarily due to dataset limitations and computational constraints, we are optimistic about the model's potential with access to more extensive resources and advanced computational power.

Translation Sundanese-Javanese
|index|id|input|predict|target|bleu|meteor|bleurt|
|---|---|---|---|---|---|---|---|
|0|330|Karya bakti bca finance posisi relationship officer muka lowongan kanggo panempatan Pekanbaru|Karya bakti bca finance posisi relationship officer muka lowongan kanggo kusabab pekanbaru|Karya bakti bca finance posisi relationship officer mbukak lowongan dingge manggon pekanbaru|54\.91004867761124|0\.7361111111111112|\[0\.39940304\]|
|1|204|Ku meser morinaga chil kid sareng chil school saharga rp 500\. 000, bunda bakal nampi voucher gopay rp 50\. 000\. |Ku meser morinaga chil kid lan chil school saharga rp 500\. 000, bunda bakal nampi voucher gopay rp 50\. 000\.|Kanthi tuku morinaga chil kid lan chil school rega rp 500\. 000, ibu isa entuk voucher gopay rp 50\. 000\.|58\.15025407036991|0\.748263888888889|\[0\.05238612\]|
|2|391|Punten perhatosanna admin\. Jaringan di cileungsi bogor awon pisan kualitasna\. Padahal mayarna awis\.|Pangan perhatosane admin\. Jaringan ning cileungsi bogor awis banget kualitase\. Padahal mayarna awis\.|Tulung perhatiane admin\. Jaringan ning cileungsi bogor elek banget kualitase\. Padahal bayare larang\.|46\.892438882117254|0\.6805268595041322|\[0\.08063303\]|
|3|200|Tuangeun india numutkeun abdi teu raos\. Seueur teuing rempah|Panganan indi aku ora enak\. Ora ora rempah|Panganan india miturut ku ora enak\. Kakehan bumbon|19\.070828081828378|0\.3506944444444444|\[-0\.37893215\]|
|4|462|Tagihan internet abdi teu lebet kanu akal 250% leuwih awis kakak\! Peryogi penjelasan\.|Tagihan internet aku ora akal 250% leuwih saka\! Peryogi penjelasan\.|Tagihan internetku ora mlebu akal 250% luwih larang kang\! Njaluk penjelasan\.|15\.05770286076146|0\.505283273381295|\[0\.18163721\]|
|5|122|baheula resep pisan tuang di dieu dina sami waktosna di Jakarta, nembe terang yen aya oge di Bandung\. Tempatna sae pisan\. Aya seueur pilihan suasana anu tiasa dipilih, naha di luar, di jero rohangan, ngaroko atanapi henteu, aya tempat pikeun sadaya jinis tamu\. Dahareunana oge raos\. Siga pasta-pasta ala urang Indonesia\.|Aku seneng banget mangan ning kene ning karo wektu, nembe terang ana uga ning bandung\. Panggonane apik banget\. Aya seueur pilihan suwasane sing isa dipilih, nanging jero rohangan, nggawe akeh, ana panggonane sadaya jinis tamu\. Dahareunane uga enak\. Siga pasta-pasta enak\.|Biyen aku seneng banget mangan ning kene wektu podo ning jakarta, lagek eruh lak ning bandung uga ana\. Panggone enak banget\. Akeh pilihan kahanan sing isa dipilih, sir ning jaba, jero, ngrokok utawa ngge ora, ana panggon dinggo kabeh jenis wong moro\. Panganane uga enak seh\. Ya pasta-pasta ala wong indonesia\.|13\.997172942497256|0\.4161971820190063|\[-0\.17970386\]|

The accuracy from Sundanese-Javanese/Javanese-Sundanese is more good that other task translation dor local languages since we doing unsupervised learning to T5-small model.


```python
display(predict_eval_df['bleu'].mean())
display(predict_eval_df['meteor'].mean())
display(predict_eval_df['bleurt'].mean())
```
```powershell
15.334266604564634

0.3900363024364832

array([-0.13708009])
```

As can be seen, the accuracy of our system is still not optimal, which is caused by the limited number of datasets we have and device limitations. However, we believe that with a larger dataset and more sophisticated tools, this model can achieve much better accuracy. We hope this project can be a valuable first step in efforts to overcome language barriers in Indonesia, and we are open to collaboration and support to expand the scope and effectiveness of our machine translation system.

Thank you for visiting our project. For more detailed information about the dataset, references and how to use the system, please visit the following links:

# Notebooks
- [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1R1LuIVanYgrU2nTWoY_qWEfTglu68-9E?usp=sharing]) If you want to try several languages.
- [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1x4Cf1YJwgAbMd1EpbvafBdB4IC_w-94j?usp=sharing]) If you want train all availabel languages in NusaX.

# Datasets
- https://huggingface.co/datasets/indonlp/NusaX-MT
- https://github.com/IndoNLP/nusax
- https://ar5iv.labs.arxiv.org/html/2205.15960
- https://data.statmt.org/cc-100/

# References

- https://github.com/huggingface/transformers/tree/main/examples/pytorch/translation
- https://colab.research.google.com/github/huggingface/notebooks/blob/main/examples/translation.ipynb#scrollTo=X4cRE8IbIrIV
- https://huggingface.co/docs/transformers/tasks/translation
- https://huggingface.co/docs/transformers/model_doc/t5
- https://github.com/EliasK93/transformer-models-for-domain-specific-machine-translation
- https://huggingface.co/docs/transformers/model_doc/marian#old-style-multi-lingual-models
- https://www.mdpi.com/2227-7390/11/4/1006
- https://huggingface.co/spaces/evaluate-metric/bertscore#:~:text=Metric%20description,token%20in%20the%20reference%20sentence
- https://huggingface.co/spaces/evaluate-metric/bleurt

We hope this documentation helps you understand more about our project and how you can get involved. Thank you for being part of this journey.

<h3 align="center">Sampurasun🙏</h3>


