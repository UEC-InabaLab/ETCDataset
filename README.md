# Emotion Transcription in Conversation Dataset

[![License: CC BY-NC 4.0][cc-by-nc-shield]][cc-by-nc]

The Emotion Transcription in Conversation (ETC) Dataset is a Japanese dialogue dataset of approximately 1,000 conversations. Each utterance is paired with an emotion transcription, a natural language description of the speaker's internal emotional state at the time of the utterance. The dataset also includes emotion labels corresponding to the emotion transcriptions, as well as speakers' personality traits (TIPI-J).

This dataset was constructed as a benchmark for the task of Emotion Transcription in Conversation (ETC): describing the emotional states behind speakers' utterances in natural language.

> [!Note]
> A Japanese version of this README is available [here](./README_ja.md).

> [!NOTE]
> The published data has been quality-checked, and dialogues considered ethically problematic have been excluded.
> Please note that the analysis reported in the [paper](#-citation) is based on the dataset prior to the exclusion of such dialogues and may differ from the statistics of the published version. Additionally, speaker names have been replaced with anonymous IDs assigned by the dataset creators.

> [!CAUTION]
> The dialogue content in this dataset was collected via crowdsourcing and does not represent the beliefs or opinions of the dataset creators or their affiliated institutions.


## 🌟 Statistics

|  | ETC Dataset |
| --- | --- |
| # Dialogues | 997 |
| # Speakers | 198 |
| # Utterances / emotion transcriptions | 9,970 |
| Utterances per dialogue | 10 |
| Avg. utterance length (characters) | 42.72 |
| └ Speaker | 44.65 |
| └ Listener | 40.79 |
| Avg. emotion transcription length (characters) | 28.88 |
| └ Speaker | 28.91 |
| └ Listener | 28.85 |
| # Emotion categories | 7 (Ekman's 6 basic emotions + Neutral) |
| Language | Japanese |

## 📁 Data Structure

The `etc/` directory contains the dialogue data (`dialogues/*.json`) and speaker personality trait data based on TIPI-J[^1] (`personality_traits.json`).

```
etc/
├── dialogues/              // Dialogue data (one file per dialogue)
│   ├── 0001.json
│   ├── 0002.json
│   ├── ...
│   └── 0997.json
├── personality_traits.json // Speaker personality traits data
└── split.json              // Train/Valid/Test split information
```


### 💬 Dialogue Data

The dialogue data includes participant IDs, utterances, emotion transcriptions, and emotion labels.
Each dialogue begins with the Speaker's utterance, and the Speaker and Listener take turns alternately (10 utterances per dialogue in total).

For dialogue collection, we adopted the dialogue setup from EmpatheticDialogues[^3]. For each dialogue, a specific emotion label (e.g., "impressed," "disappointed," "confident"—32 types in total) was assigned. The Speaker talks about an experience related to that emotion, while the Listener responds to the Speaker's utterances.

Emotion labels consist of 7 categories: Ekman's 6 basic emotions[^2] (joy, sadness, fear, anger, surprise, and disgust) plus "Neutral." Each emotion transcription was annotated by 3 annotators in a multi-label format.


| Key | Type | Description |
| --- | --- | --- |
| dialogue_id | int | Dialogue ID |
| dialogue_emotion | str | Emotion label assigned to the participant pair for the dialogue |
| participants | dict | Dictionary of speaker IDs |
| participants.speaker | str | Speaker ID |
| participants.listener | str | Listener ID |
| dialogue | list (dict) | List of utterance information |
| dialogue.turn | int | Turn number (1-indexed) |
| dialogue.role | str | Role: `speaker` or `listener` |
| dialogue.utterance | str | Utterance text |
| dialogue.emotion_transcription | str | The participant's emotion transcription for the utterance |
| dialogue.emotions | list (list (str)) | List of emotion labels for the emotion transcription (multi-label format by 3 annotators)  |


**Example: `etc/dialogues/0945.json`**

```json
{
    "dialogue_id": 945,
    "dialogue_emotion": "信頼する",
    "participants": {
        "speaker": "FQ",
        "listener": "BN"
    },
    "dialogue": [
        {
            "turn": 1,
            "role": "speaker",
            "utterance": "信頼がないと、人間関係って構築できないものかなと思いますが、そうは言っても裏切られることもあるし、難しいですよね。",
            "emotion_transcription": "いきなり深い質問をし、相手は困るかなと思いつつも、人柄を知るために聞いてみたくなりました。",
            "emotions": [
                ["恐怖"],
                ["恐怖"],
                ["該当なし"]
            ]
        },
        {
            "turn": 1,
            "role": "listener",
            "utterance": "人との関係って本当に難しいですよね。良かれと思ってした事が相手からすれば迷惑だっり、仲が良いと思っていたのに裏で悪口を言われていたり正解がなくて手探りで構築していくしかありませんよね。",
            "emotion_transcription": "自分は人間関係の複雑さに大して深く共感し、難しい事も多いからこそ誠実に向き合って信頼関係を築くことが大切だと伝えたかったです。",
            "emotions": [
                ["悲しみ"],
                ["悲しみ"],
                ["該当なし"]
            ]
        }
        // ...
    ]
}
```


### 👤 Participant Personality Trait Data

The personality trait data includes TIPI-J (Japanese version of the Ten-Item Personality Inventory)[^1] questionnaire items, speaker responses, and Big Five scores computed from those responses.

| Key | Type | Description |
| --- | --- | --- |
| item | dict | Questionnaire items (i01–i10) |
| personality | dict | Personality trait data keyed by speaker ID |
| personality.*.participant_id | str | Participant ID |
| personality.*.response | dict | Responses to each questionnaire item |
| personality.*.score | dict | Scores for each Big Five dimension |
| personality.*.score.openness | int | Openness (2–14) |
| personality.*.score.conscientiousness | int | Conscientiousness (2–14) |
| personality.*.score.extraversion | int | Extraversion (2–14) |
| personality.*.score.agreeableness | int | Agreeableness (2–14) |
| personality.*.score.neuroticism | int | Neuroticism (2–14) |


```json
{
    "item": {
        "i01": "活発で，外向的だと思う",
        "i02": "他人に不満をもち，もめごとを起こしやすいと思う",
        "i03": "しっかりしていて，自分に厳しいと思う",
        // ...
    },
    "personality": {
        "AA": {
            "participant_id": "AA",
            "response": {
                "i01": "2. おおよそ違うと思う",
                "i02": "2. おおよそ違うと思う",
                // ...
            },
            "score": {
                "openness": 10,
                "conscientiousness": 2,
                "extraversion": 7,
                "agreeableness": 11,
                "neuroticism": 9
            }
        }
        // ...
    }
}
```

### 🗂️ Split Information

`split.json` contains the Train / Valid / Test split information used in the experiments reported in the [paper](#-citation). Note that the dataset used in the paper's experiments includes dialogues that were later excluded from this published dataset due to ethical concerns.

## 🛡️ Guidelines for Use

> [!CAUTION]
> **Please observe the following guidelines when using this dataset:**
> - Do not attempt to identify individuals from the data in this dataset.
> - Do not use this dataset to impersonate any specific speaker.
> - When using this dataset for purposes such as predicting speakers' personality traits, be mindful of the rights of speakers who may not wish to have their personal information inferred.


## 📄 Citation

```bibtex
@inproceedings{tanaka-etal-2026-etcdataset,
  title = "Emotion Transcription in Conversation: A Benchmark for Capturing Subtle and Complex Emotional States through Natural Language",
  author = "Tanaka, Yoshiki and 
    Uehara, Ryuichi and 
    Inoue, Koji and 
    Inaba, Michimasa",
  booktitle = "Proceedings of the Fifteenth Language Resources and Evaluation Conference (LREC 2026)",
  year = "2026",
  pages = "9692--9709",
  publisher = "European Language Resources Association (ELRA)"
}

@inproceedings{tanaka-etal-2026-etcdataset-ja,
    title = "対話における心情記述: 自然言語による機微かつ複雑な心情理解のためのベンチマーク",
    author = "田中 義規 and 上原 隆一 and 井上 昂治 and 稲葉 通将",
    booktitle = "言語処理学会第32回年次大会発表論文集",
    year = "2026",
    pages = "1328--1333"
}
```

## 🙇 Acknowledgments

This work was supported by JSPS KAKENHI Grant Number 25H01382.


## ⚖️ License

This dataset is licensed under [CC BY-NC 4.0][cc-by-nc].

[![CC BY-NC 4.0][cc-by-nc-image]][cc-by-nc]



[^1]: Atsushi Oshio, ABE Shingo, and Pino Cutrone. Development, reliability, and validity of the japanese version of ten item personality inventory (tipi-j). Japanese Journal of Personality, Vol. 21, No. 1, 2012.
[^2]: P. Ekman, W. V. Friesen, M. J. O'Sullivan, A. K. Chan, I. Diacoyanni-Tarlatzis, K. G. Heider, R. Krause, W. A. LeCompte, T. K. Pitcairn, P. E. Ricci-Bitti, K. R. Scherer, M. Tomita, and A. Tzavaras. Universals and cultural differences in the judgments of facial expressions of emotion. Vol. 53, pp. 712–717, 1987.
[^3]: Hannah Rashkin, Eric Michael Smith, Margaret Li, and Y-Lan Boureau. Towards empathetic open-domain conversation models: A new benchmark and dataset. In Anna Korhonen, David Traum, and Lluís Màrquez, editors, Proceedings of the 57th Annual Meeting of the Association for Computational Linguistics, pp. 5370–5381, Florence, Italy, July 2019. Association for Computational Linguistics.

[cc-by-nc]: https://creativecommons.org/licenses/by-nc/4.0/
[cc-by-nc-image]: https://licensebuttons.net/l/by-nc/4.0/88x31.png
[cc-by-nc-shield]: https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg
