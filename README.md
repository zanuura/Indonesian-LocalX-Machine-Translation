# Indonesian Local Machine Translation

## Overview

This is an experimental project focused on building a Machine Translation system for Indonesian local languages. Indonesia, with around 17,000 islands, 360 ethnic groups, and 840 regional languages, faces challenges in communication and socialization due to this diversity. The goal of this project is to provide a solution for daily communication and socialization by developing a translation machine.

The project utilizes datasets from [Nusax](https://huggingface.co/datasets/indonlp/NusaX-MT) and [CC100](https://metatext.io/datasets/cc100), along with pretrained models from Transformers. The NusaX dataset includes 12 labeled languages, including Indonesian, English, and 10 Indonesian local languages, namely Acehnese, Balinese, Banjarese, Buginese, Madurese, Minangkabau, Javanese, Ngaju, Sundanese, and Toba Batak. The CC100 dataset, which has around 100 languages, is narrowed down to Javanese and Sundanese for the current focus on translation between these two languages, and because we have not found datasets for other local languages..


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

<!-- Trigrams : 
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
``` -->

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
    output_dir="T5_NusaX_Sun_Jav_checkpoints",
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

20 Row from 200
|index|id|input|predict|target|bleu|meteor|bleurt|
|---|---|---|---|---|---|---|---|
|0|330|Karya bakti bca finance posisi relationship officer muka lowongan kanggo panempatan Pekanbaru|Karya bakti bca finance posisi relationship officer muka lowongan kanggo kusabab pekanbaru|Karya bakti bca finance posisi relationship officer mbukak lowongan dingge manggon pekanbaru|54\.91004867761124|0\.7361111111111112|\[0\.39940304\]|
|1|204|Ku meser morinaga chil kid sareng chil school saharga rp 500\. 000, bunda bakal nampi voucher gopay rp 50\. 000\. |Ku meser morinaga chil kid lan chil school saharga rp 500\. 000, bunda bakal nampi voucher gopay rp 50\. 000\.|Kanthi tuku morinaga chil kid lan chil school rega rp 500\. 000, ibu isa entuk voucher gopay rp 50\. 000\.|58\.15025407036991|0\.748263888888889|\[0\.05238612\]|
|2|391|Punten perhatosanna admin\. Jaringan di cileungsi bogor awon pisan kualitasna\. Padahal mayarna awis\.|Pangan perhatosane admin\. Jaringan ning cileungsi bogor awis banget kualitase\. Padahal mayarna awis\.|Tulung perhatiane admin\. Jaringan ning cileungsi bogor elek banget kualitase\. Padahal bayare larang\.|46\.892438882117254|0\.6805268595041322|\[0\.08063303\]|
|3|200|Tuangeun india numutkeun abdi teu raos\. Seueur teuing rempah|Panganan indi aku ora enak\. Ora ora rempah|Panganan india miturut ku ora enak\. Kakehan bumbon|19\.070828081828378|0\.3506944444444444|\[-0\.37893215\]|
|4|462|Tagihan internet abdi teu lebet kanu akal 250% leuwih awis kakak\! Peryogi penjelasan\.|Tagihan internet aku ora akal 250% leuwih saka\! Peryogi penjelasan\.|Tagihan internetku ora mlebu akal 250% luwih larang kang\! Njaluk penjelasan\.|15\.05770286076146|0\.505283273381295|\[0\.18163721\]|
|5|122|baheula resep pisan tuang di dieu dina sami waktosna di Jakarta, nembe terang yen aya oge di Bandung\. Tempatna sae pisan\. Aya seueur pilihan suasana anu tiasa dipilih, naha di luar, di jero rohangan, ngaroko atanapi henteu, aya tempat pikeun sadaya jinis tamu\. Dahareunana oge raos\. Siga pasta-pasta ala urang Indonesia\.|Aku seneng banget mangan ning kene ning karo wektu, nembe terang ana uga ning bandung\. Panggonane apik banget\. Aya seueur pilihan suwasane sing isa dipilih, nanging jero rohangan, nggawe akeh, ana panggonane sadaya jinis tamu\. Dahareunane uga enak\. Siga pasta-pasta enak\.|Biyen aku seneng banget mangan ning kene wektu podo ning jakarta, lagek eruh lak ning bandung uga ana\. Panggone enak banget\. Akeh pilihan kahanan sing isa dipilih, sir ning jaba, jero, ngrokok utawa ngge ora, ana panggon dinggo kabeh jenis wong moro\. Panganane uga enak seh\. Ya pasta-pasta ala wong indonesia\.|13\.997172942497256|0\.4161971820190063|\[-0\.17970386\]|
|6|97|Abdi teu kuciwa ka produk apple|Aku ora kuciwa ka produk apple|Aku ora kuciwa karo produke apple|32\.46679154750991|0\.8066666666666668|\[0\.60135025\]|
|7|2|Kangkungna lumayan mung kepiting saos padangna nguciwakeun arurang dipasihan kepiting anu kopong ahirna arurang teu tuang kepitingna sareng dibalikkeun\.|Kanggonane lumayan nanging nguciwakeun awakdewe dipasihan awakdewe ora mangan awakdewe lan dibalikkeun\.|Kangkunge lumayan nanging yuyu saus padange nguciwakake dhewe dikei yuyu sing kopong akhir dhewe ora mangan yuyune lan dibalekake\.|5\.643423197451416|0\.26482440990213013|\[-0\.15498953\]|
|8|493|Parah pisan kuring mesen di bukalapak nganggo gosend kamari nepi ayeuna teu acan sumping kumaha ieu\. Barangna peryogi pisan|Abdi mesen ning bukalapak nganggo gosend nyaeta ora acan kumaha iki\. Panganane peryogi banget|Nemen pesen bukalapak nanggo gosend dekwingi sampek saiki durung teka piye iki\. Butuh barange banget|6\.1827411661290235|0\.23396226415094337|\[-0\.15670584\]|
|9|174|Mimiti ka dieu eta ngarayakeun kalulusan rai abdi, tempatna eta loh, keren pisan\. Disainna oge unik, keren, merenah, merenah, cocog pisan kanggo tuang wengi sareng kulawargi, pasangan, dulur, sareng rerencangan, pamandanganna oge keren, tuangeunna oge raos-raos\. Pangaos saluyu sareng palayananna, teu nyesel ka dieu\. Disarankeun pisan\.|Mimiti ning kene kulawargi kuwi, panggonane kuwi, keren tenan\. Disaine uga unik, tenan, tenan, cocok tenan kanggo mangan wengi lan kulawargi, pasangan, dulur, lan kanca, panganane uga tenan, panganane uga enak-enak\. Disaranke ning kene\.|Kapisan marang mrene kuwi ngerayakake kelulusan adikku, panggonane kuwi loh, apik tenan\. Desaine uga unik, apik, kapenak, kapenak, cocok tenan kanggo mangan mbengi bareng kaluwarga, pasangan, sedulur, lan kanca-kanca, pemandangane uga apik, panganane uga enak-enak\. Regane sebanding karo pelayanane, ora gela marang mrene\. Bener-bener direkomendasikake\.|18\.554984102985628|0\.3486780678163579|\[-0\.20813209\]|
|10|57|Rumah makan ieu tangtua kagolong mirah mung dibandikeun kanu rumah makan nu sanesna\. Mun tuangeun nu disayogikeun biasa wae teu aya nu istimewa\. Seseurna mah tuangeunna ngan lauk atawa hayam nu cara masak digoreng\. Tiasa disebat menu tuangeunna teu aya rupi-rupi sama sekali\.|Kanggonan iki tangtua kagolong nanging dibandikeun nanging rumah nanging enak\. Nanging panganan sing disayogikeun biasa nanging ora ana sing istimewa\. Seseure panganane nanging akeh nanging cara masak digoreng\. Isa disebat menu panganane ora ana enak sama sekali\.|Omah mangan iki mesthu kagolong murah yen dibandingake karo omah mangan sakjenise\. Nanging panganan sing disediakake biasa wae ora ana sing istimewa\. Akeh-akehe panganan mung iwak utawa oithik kanthi cara masak digoreng\. Bisa diomong menu panganane ora ana variasine blas\.|24\.78238686631454|0\.4625829755848479|\[-0\.13292158\]|
|11|127|Dina restoran eta aya seeur pilihan tuangeun, mimiti ti nu mirah nepi nu awis|Aku restoran ana pilihan panganan, mimiti sing mirah nepi sing awis|Ing restoran kuwi kasadia akeh pilian panganan, mulai saka sing murah nganti sing larang|6\.632729312157198|0\.25306122448979596|\[-0\.14577435\]|
|12|142|Kue patepang taun spesial nganggo endog sapiring ditambihan mozzarella kanggo anjeun|Kue patepang taun spesial nganggo endog sapiring ditambihan mozzarella kanggo ajeun|Roti ulang tahune spesial nganggo ndhog sakpiring ditambah mozzarella kanggo kowe|10\.600313379512592|0\.34090909090909094|\[-0\.0176141\]|
|13|29|Tuangen di dieu raos-raos ku seueur rupi-rupi\. Sadayana raos ku pamandangan sae\. Palayanan nu maksimal ti mbak diah sareng gina ti mimiti sumping nepi rengse dilayanan ku sae pisan\.|Panggonane enak-enak\. Panganane enak enak karo pamandangane apik\. Panganane sing maksimal saka mbak diah lan gina saka mimiti sumping ngse dilayanane saka enak banget\.|Panganan ing kene enak-enak kanthi akeh variasine\. Kabehane enak kanthi pemandangan apik\. Pelayanane sing maksimal saka mbak diah lan gina saka ngewiwiti teka nganti rampung, dilayani kanthi apik tenan\.|23\.930559017896012|0\.37167352537722914|\[-0\.1293731\]|
|14|244|Kafe ieu, ngagaduhan cemilan anu pang hadena, tahu ti lumboenk sareng volkano risols namina\. Sensasi unik tina panampilan anu sami ngajantenkeun hoyong deui hoyong deui, pikeun anu panasaran tiasa dicobian deh\.|Kafe iki, ngagaduhan cemilan sing hadena, tahu ora lumboenk lan volkano risols namine\. Sensasi unik sing panggonan sing ngajantenke sing nyaeta nyaeta nyaeta nyaeta nyaeta dicobian nyaeta\.|Kafe iki, nduwe pirang-pirang cemilan sing top banget, tahu ning lumboenk karo vulkano risols jenenge\. Sensasine unik saka wujude karo rasane nggarai sir maneh si maneh, dinggo sing penasaran oleh njajal wis\.|6\.312139612101272|0\.21669159243123737|\[-0\.20141259\]|
|15|306|Saleresna nya sapertosna abdi teu ditakdirkeun nikmatan indomie goreng, padahal ngan ngulub miena lho\. Tiasa-tiasana leuleus teuing\.|Aku aku ora ditakdirke nikmatan indomie goreng, padahal nanging ngulub miene lho\. Teu-etiasane luwih\.|Mesthi ya kayane aku ora ditakdirake nikmati indomie goreng, sanadyan mung nggodhok mine lho\. Isa-isane kaliwat lembek\.|10\.844080760155272|0\.38071065989847713|\[-0\.27489597\]|
|16|463|Kuciwa pisan\. Pesen sareng mayar tiket pesawat dienggalkeun tabuh satengah 1 enjing kanggo penerbangan jam 6 enjing\. E - tiket henteu dikirim\. Ngadadak dihubungi liwat email upami tiket nu tos dibayar tos seep dina waktos-waktos enjing\. Mung tiketna seep naha keneh bisa dibooking\.|Kuciwa banget\. Pesen lan mayar tiket pesawat satengah 1 enjing kanggo penerbangan jam 6 enjing\. E - tiket enjing dikirim\. Ngadadak dihubungi liwat email nanging tiket nanging dibayar nanging waktos-waktos enjing\. Nanging tikete akeh bisa dibooking\.|Kuciwa banget\. Pesen lan mbayar tiket motor mabur dilaksanakake jam setengah 1 isuk kanggo penerbangan jam6 isuk\. E - tiket ora dikirim\. Dumadakan dihubungi via email yen tiket sing uwis dibayar uwis entek jam-jam isuk\. Yen tikete entek ngapa egek isa dipesen\.|16\.33191451918006|0\.43979369011146585|\[-0\.1022194\]|
|17|68|Hese ngartos jalma anu ngarasa osok bener|Hese ngartos jalma sing ngarasa osok bener|Angel banget ngerteni wong sing terus rumangsa pener|5\.693025330278465|0\.06329113924050632|\[-0\.32003397\]|
|18|231|Satuju beuki naek fase wajar upami tiket naek\. Mung, pangaos dasar sawaktos fase panyisihan tos kaawisan\. Cobi bandingkeun pangaos lalajo di malaysia, thailand\.|Aku nyaeta fase wajar nanging tiket nyaeta\. Ngan, regane nyaeta fase nyisihan tiasa\. Cobi bandingke regane ning malaysia, thailand\.|Setuju saya munggah fase wajar yen tiket munggah\. Nanging, rega dasar ing pas fase penyisihan uwis kelarangan\. Coba bandingake rega nonton ing malaysia, thailand\.|11\.066171274883231|0\.35867446393762187|\[-0\.1297721\]|
|19|198|Sumping ka restoran ieu teu aya bosenna, komo dina dinten wengi arurang sakulawargi resep pisan tempat ieu kumargi aya live musikna\. Suasanana teu gentos ti tahun ka tahun\. Menu nu disayogikeun ge keneh teteup sami, ti mimiti menu eropa nepi menu khas indonesia keneh memikat\. Porsina ge teu eleuh seueur\.|Panggonane iki ora ana bosene, komo ing dina wengi seneng banget panggonane iki kumargi ana live musike\. Suwasanane ora gentos ing tahun ing tahun\. Menu sing disayogikeun karo teteup sami, ora mimiti menu eropa nepi menu khas indonesia keneh memikat\. Porsine uga ora enak\.|Teko marang restoran iki ora ana bosene, apa maneh ini mbengi minggu dhewe sakaluwarga seneng tenan panggonan iki amarga ana live musike\. Suwasanane ora owah nahunan\. Menu sing disajikake egek tetep padha, saka mulai menu eropa nganti menu khas indonesia tetep nyengsemake\. Porsine uga ora kalah akeh\.|25\.624487697618193|0\.5047220501103666|\[-0\.18800953\]|
|20|152|Hayang ka hotel nu di mongkok Hongkong, teu aya\! Sasih Juni kamari, kuring pesen sareng parantos mayar 2 kamar\. Tapi hotelna teu kapanggih\.|Aku hotel sing ning mongkok Hongkong, ora ana\! Sasih Juni kamari, kuring pesen lan pesen mayar 2 kamar\. Tapi hotelne ora kapanggih\.|Arep nyang hotel ning mongkok hongkong, ora ana\! Sasi juni wingi, aku mesen lan wis mbayar dingge 2 kamar\. Nanging hotele ora temu|15\.35259783865636|0\.5398582175925926|\[-0\.15853569\]|



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

