# Indonesian Local Machine Translation

This is experimental project to build Machine Translation for Indonesian Local Languages.

Indonesia has around 17,000 islands with 360 ethnic groups and around 840 regional languages. With so many tribes and languages, it makes communication and socialization difficult for us. That's why we created this translation machine in the hope that it can be a solution for our daily communication and socialization.

This project uses datasets from [Nusax](https://huggingface.co/datasets/indonlp/NusaX-MT) and [CC100](https://metatext.io/datasets/cc100) and uses pretrained models from Transformers. The NusaX dataset itself has 12 languages ​​that have been labeled, namely Indonesian, English, and 10 Indonesian local languages, namely Acehnese, Balinese, Banjarese, Buginese, Madurese, Minangkabau, Javanese, Ngaju, Sundanese, and Toba Batak. And the CC100 dataset has around 100 languages, but we only use the sub-dataset, namely Javanese and Sundanese, because currently we are still focusing on translation from Sundanese to Javanese and vice versa.

## Exploratory Datasets Anaylis

### [NusaX-MT](https://huggingface.co/datasets/indonlp/NusaX-MT) Sundanese & Javanese

#### Language Distribution
![image](https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/45409578-49f8-4fb1-8cef-65d8af605363)
<!-- ![image](https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/6b740fc1-ba3d-4aa0-8b9b-81a1eaf548b6)-->

#### Most Common Words
```git
Most Common Words in Javanese:
[('sing', 667), ('lan', 555), ('ora', 450), ('karo', 328), ('aku', 302), ('ing', 285), ('ning', 265), ('kanggo', 240), ('mangan', 219), ('iki', 205)]

Most Common Words in Sundanese:
[('nu', 615), ('sareng', 542), ('di', 457), ('teu', 375), ('abdi', 314), ('ka', 247), ('aya', 216), ('anu', 214), ('kanggo', 198), ('ku', 192)]
```

![image](https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/ad85fa79-134a-47ee-814f-29ae46f37b8b)

#### N-Gram Analyis
Bigrams : 
```git
[('ora ana', 34),
 ('enak lan', 30),
 ('mangan ning', 27),
 ('ora isa', 26),
 ('panganan sing', 24),
 ('ana ing', 24),
 ('lan ora', 21),
 ('ing kene', 20),
 ('sing apik', 20),
 ('sing ora', 20)]
####################
[('di dieu', 40),
 ('tuang di', 37),
 ('teu aya', 37),
 ('ka dieu', 27),
 ('sumping ka', 27),
 ('tempat ieu', 24),
 ('tuangeun nu', 24),
 ('teu tiasa', 24),
 ('di tempat', 23),
 ('di dieu.', 21)]
```

Trigrams : 
```git
[('ora ana', 34),
 ('enak lan', 30),
 ('mangan ning', 27),
 ('ora isa', 26),
 ('panganan sing', 24),
 ('ana ing', 24),
 ('lan ora', 21),
 ('ing kene', 20),
 ('sing apik', 20),
 ('sing ora', 20)]
####################
[('di dieu', 40),
 ('tuang di', 37),
 ('teu aya', 37),
 ('ka dieu', 27),
 ('sumping ka', 27),
 ('tempat ieu', 24),
 ('tuangeun nu', 24),
 ('teu tiasa', 24),
 ('di tempat', 23),
 ('di dieu.', 21)]
```

#### WordClouds
Javanese : 
![image](https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/0c982cfe-df2a-41ca-a30c-7158323cbdfc)

Sundanese : 
![image](https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/3fa8d94a-7d01-4720-9079-202b903c02bb)

### CC100 Sundanese & Javanese

#### Most Common Words

```git
Sundanese:
Most common 10 words: [('nu', 155200), ('jeung', 144216), ('anu', 120012), ('ka', 100481), ('di', 100409), ('dina', 96300), ('ku', 79068), ('teu', 66070), ('anjeun', 66057), ('ieu', 66034)]

Javanese:
Most common 10 words: [('ing', 149417), ('lan', 101946), ('sing', 67811), ('kang', 64528), ('ora', 44236), ('iku', 42325), ('kanggo', 40798), ('iki', 37851), ('ingkang', 37518), ('wong', 34040)]
```

#### WordClouds
Sundanese : 
![image](https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/21e51678-264a-4128-a8e9-fd300516d271)

Javanese : 
![image](https://github.com/zanuura/Indonesian-Local-Machine-Translation/assets/73764446/89f27658-dd0a-4216-95d8-9f75db647e8f)


### Training

```python
training_args = Seq2SeqTrainingArguments(
    output_dir="T5_NusaX_Jav_Sun_checkpoints",
    evaluation_strategy="epoch",
    learning_rate=2e-5,
    per_device_train_batch_size=16,
    per_device_eval_batch_size=16,
    weight_decay=0.01,
    save_total_limit=5,
    num_train_epochs=200,
    predict_with_generate=True,
    fp16=True,
)

trainer = Seq2SeqTrainer(
    model=model,
    args=training_args,
    train_dataset=tokenized_nusax_train,
    eval_dataset=tokenized_nusax_valid,
    tokenizer=tokenizer,
    data_collator=data_collator,
    compute_metrics=compute_metrics,
)

trainer.train()
```

### Test Predict Evaluate

```python

def predict_evaluate(data):
    try:
        sources = data['text_1']
        targets = data['text_2']

        predictions = [translator(prefix + source)[0]['translation_text'] for source in sources]

        # Initialize lists to store results
        predicts, accuracies, bleus, meteors, bertscore_precisions, bertscore_recalls, bertscore_f1s = [], [], [], [], [], [], []

        for prediction, target in zip(predictions, targets):
            predicts.append(prediction)

            # Calculate accuracy for each prediction
            accuracies.append(int(prediction == target))

            # Compute sacreBLEU
            bleu_result = sacrebleu_metric.compute(predictions=[prediction], references=[target])
            bleus.append(bleu_result["score"])

            # Compute METEOR
            meteor_result = meteor_metric.compute(predictions=[prediction], references=[target])
            meteors.append(meteor_result["meteor"])

            # Compute BERTScore
            bertscore_result = bertscore_metric.compute(predictions=[prediction], references=[target], lang="en")
            bertscore_precisions.append(bertscore_result["precision"])
            bertscore_recalls.append(bertscore_result["recall"])
            bertscore_f1s.append(bertscore_result["f1"])

        # Return a dictionary with lists as values
        return {
            "input": data['text_1'],
            "predict": predicts,
            "target": targets,
            "accuracy": accuracies,  # Now a list of accuracies
            "bleu": bleus,
            "meteor": meteors,
            "bertscore_precision": bertscore_precisions,
            "bertscore_recall": bertscore_recalls,
            "bertscore_f1": bertscore_f1s
        }

    except Exception as e:
        print(f"Error during prediction or evaluation: {e}")
        return None

# Assuming test_dataset is correctly defined
data_test_predict_eval = test_dataset.map(predict_evaluate, batched=True)

```

|index|id|text\_1|text\_2|text\_1\_lang|text\_2\_lang|text\_1\_length|text\_2\_length|input|predict|target|accuracy|bleu|meteor|bertscore\_precision|bertscore\_recall|bertscore\_f1|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0|192|Piye ora nesu ora, kena php indosat terus kie\. Pulsane kepotong ludes ora ana turahe\.|Kumaha teu ambek coba, keuna php indosat wae euy\. Pulsa sok kapotong seep teu aya sesa\.|jav|sun|15|16|Piye ora nesu ora, kena php indosat terus kie\. Pulsane kepotong ludes ora ana turahe\.|Tempat teu teu teu teu, kena php indosat teu kieu\. Pulsana kepotong teu teu aya turahe\.|Kumaha teu ambek coba, keuna php indosat wae euy\. Pulsa sok kapotong seep teu aya sesa\.|0|6\.809398432036521|0\.33223684210526316|\[0\.88012719\]|\[0\.85503322\]|\[0\.86739874\]|
|1|359|Rega ora patia larang, rasane uga enak, akeh pilian panganan khas Banjar liyane\.|Pangaosna teu awis teuing, rasana oge raos, seueur pilihan tuangeun khas Banjar nu sanes\.|jav|sun|13|14|Rega ora patia larang, rasane uga enak, akeh pilian panganan khas Banjar liyane\.|Abdi teu patia awis, rasana oge raos, abdi pilihan tuangeun khas Banjar liyana\.|Pangaosna teu awis teuing, rasana oge raos, seueur pilihan tuangeun khas Banjar nu sanes\.|0|38\.71493850247494|0\.6843770545693622|\[0\.91857773\]|\[0\.91643155\]|\[0\.91750336\]|
|2|386|Hypermart saiki ngenehi diskon 20% kanggo produk seger saben dina senen - kemis kanthi mandiri kartu hypermart|Hypermart nuju masihan diskon 20% kanggo produk seger unggal dinten senen - kemis ku mandiri kartu hypermart|jav|sun|17|17|Hypermart saiki ngenehi diskon 20% kanggo produk seger saben dina senen - kemis kanthi mandiri kartu hypermart|Hypermart ayeuna ngeunah diskon 20% kanggo produk seger saben dina senen - kemis ku mandiri kartu hypermart|Hypermart nuju masihan diskon 20% kanggo produk seger unggal dinten senen - kemis ku mandiri kartu hypermart|0|60\.28817681965138|0\.7739512471655329|\[0\.93737841\]|\[0\.94295597\]|\[0\.9401589\]|
|3|103|Resto iki aku rekomendasikake kanggo kowe tekani yen kowe pengen nyantap karo nikmati surup\. Amarga lokasine sing ana ing dhataran dhuwur, kowe iso nikmati kaendahan alam pegununungan lan suwasana kota\. Kanggo panganane dhewe ana sawetara cita rasa, saka masakan daerah, indonesia, nganti internasional\.|Resto ieu abdi sarankeun kanggo anjeun sumpingan mung anjeun hoyong tuang sabari nganikmatan sunset\. Kusabab letakna nu aya di dataran tinggi, anjeun tiasa nganikmatan kaendahan alam pagunungan sareng suasana kota\. Kanggo tuangeunnana ge aya sababaraha cita rasa, ti masakan daerah, Indonesia, nepi internasional\.|jav|sun|43|43|Resto iki aku rekomendasikake kanggo kowe tekani yen kowe pengen nyantap karo nikmati surup\. Amarga lokasine sing ana ing dhataran dhuwur, kowe iso nikmati kaendahan alam pegununungan lan suwasana kota\. Kanggo panganane dhewe ana sawetara cita rasa, saka masakan daerah, indonesia, nganti internasional\.|Resto ieu abdi rekomendasikakeun kanggo abdi tuangeun nyantap abdi nikmati surup\. Abdi lokasina nu aya di dhataran tuangeun, abdi iso nikmati kaendahan alam pagununungan sareng suasana kota\. Kanggo tuangeunna aya sawetara cita rasa, ti masakan daerah, Indosia, nganti internasional\.|Resto ieu abdi sarankeun kanggo anjeun sumpingan mung anjeun hoyong tuang sabari nganikmatan sunset\. Kusabab letakna nu aya di dataran tinggi, anjeun tiasa nganikmatan kaendahan alam pagunungan sareng suasana kota\. Kanggo tuangeunnana ge aya sababaraha cita rasa, ti masakan daerah, Indonesia, nepi internasional\.|0|26\.146956379552613|0\.5204599761051375|\[0\.89048052\]|\[0\.88968354\]|\[0\.89008188\]|
|4|61|Mangan sikil wedus bagian pupu sing empuk lan lumayan gedhi dinggo ukuran medium, pas dingge wong siji, mangan tanpa sega, karo es lemon tea\. Seger kurange mung panggone kebul-kebul, wajar kan jenenge uga wedus bakar hehehe|Tuang suku domba pingpingna hipu sareng gede pikeun ukuran sedeng, pas pikeun hiji jalmi, tuangna teu nganggo sangu, kalayan es lemon tea\. Seger, hiji-hijina kakurangannana nyaeta tempatna loba haseup, wajarlah namina oge domba bakar hehehe|jav|sun|36|35|Mangan sikil wedus bagian pupu sing empuk lan lumayan gedhi dinggo ukuran medium, pas dingge wong siji, mangan tanpa sega, karo es lemon tea\. Seger kurange mung panggone kebul-kebul, wajar kan jenenge uga wedus bakar hehehe|Tuang sikil wedus bagian pupu nu kusabab sareng lumayan kusabab ukuran medium, pas tuangeun tuangeun sahiji, tuangeun tanpa sae, ku es lemon tea\. Seger kurange ngan tuangeun kebul-kebul, wajar sareng na oge wedus bakar hehehe|Tuang suku domba pingpingna hipu sareng gede pikeun ukuran sedeng, pas pikeun hiji jalmi, tuangna teu nganggo sangu, kalayan es lemon tea\. Seger, hiji-hijina kakurangannana nyaeta tempatna loba haseup, wajarlah namina oge domba bakar hehehe|0|12\.415687674080344|0\.32763791259168706|\[0\.87924463\]|\[0\.86076641\]|\[0\.86990744\]|


```python
predict_eval_df['bleu'].mean()
predict_eval_df['meteor'].mean()
```
```powershell
18.092717876126102
0.4190095152615721
```
