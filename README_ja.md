# Emotion Transcription in Conversation Dataset

[![License: CC BY-NC 4.0][cc-by-nc-shield]][cc-by-nc]

Emotion Transcription in Conversation Dataset は，対話中の各発話に対して話者自身が記述した心情文を含む，約1,000 件の対話からなる日本語対話データセットです．また，心情文に基づく感情ラベルや，話者の性格特性（TIPI-J）も含まれています．

本データセットは，発話の背後にある話者の心情を自然言語で記述するタスク「対話における心情記述 (Emotion Transcription in Conversation; ETC)」のためのベンチマークデータセットとして構築されました．

> [!NOTE]
> 本ページで公開されているデータは，収集した対話に対して品質チェックを行い，倫理的観点から問題があると考えられる対話を除外したものです．
> [論文](#-引用)では，上記の不適切な内容が含まれる対話を除外する前のデータセットに基づく分析結果が報告されており，公開版の統計情報とは異なることにご注意ください．また，話者名はデータセット作成者が付与した匿名IDに置き換えられています．

> [!CAUTION]
> 本データセットに含まれる対話の内容は，クラウドソーシングにより収集されたものであり，データセット作成者やその所属機関の信条や意見を表すものではありません．

## 🌟 統計情報

|  | ETC Dataset |
| --- | --- |
| 対話数 | 997 対話 |
| 話者数 | 198 名 |
| 発話数 / 心情文数 | 9,970 |
| 対話あたりの発話数 | 10 発話 |
| 発話の平均長（文字数） | 42.72 文字 |
| └ スピーカー | 44.65 文字 |
| └ リスナー | 40.79 文字 |
| 心情文の平均長（文字数） | 28.88 文字 |
| └ スピーカー | 28.91 文字 |
| └ リスナー | 28.85 文字 |
| 感情カテゴリ数 | 7（Ekman の 6 基本感情 + 該当なし）|
| 言語 | 日本語 |

## 📁 データ構成

``etc/`` 内に，対話データ ``dialogues/*.json`` と，話者のTIPI-J[^1]に基づく性格特性データ ``personality_traits.json`` が格納されています．

```
etc/
├── dialogues/              // 対話データ（1ファイルにつき1対話）
│   ├── 0001.json
│   ├── 0002.json
│   ├── ...
│   └── 0997.json
├── personality_traits.json // 話者の性格特性データ
└── split.json              // Train/Valid/Test 分割情報
```


### 💬 対話データ

対話データには，参加者ID，発話，心情文，感情ラベルが含まれます．
各対話はスピーカーの発話から始まり，スピーカーとリスナーが交互に発話します（1対話あたり合計10発話）．

対話の収集にあたり，EmpatheticDialoguesの対話設定[^3]を採用しました．話者には特定の感情ラベル（「感動する」「がっかりする」「自信がある」など，全32種類）が指定されました．スピーカーはその感情を感じた体験に関してリスナーに語り，リスナーはスピーカーの話に反応する形式で対話が進行します．

感情ラベルは Ekman の6基本感情[^2]（喜び・悲しみ・恐怖・怒り・驚き・嫌悪）に「該当なし」を加えた7カテゴリで構成されています．各心情文には3名のアノテータがマルチラベル形式でアノテーションを行いました．

| キー | 型 | 説明 |
| --- | --- | --- |
| dialogue_id | int | 対話ID |
| dialogue_emotion | str | 対話実施時に指定された感情ラベル |
| participants | dict | 話者IDの辞書 |
| participants.speaker | str | スピーカーのID |
| participants.listener | str | リスナーのID |
| dialogue | list (dict) | 発話情報のリスト |
| dialogue.turn | int | ターン番号（1始まり） |
| dialogue.role | str | 話者の役割．`speaker` または `listener` |
| dialogue.utterance | str | 発話テキスト |
| dialogue.emotion_transcription | str | 発話時の話者の心情文 |
| dialogue.emotions | list (list (str)) | 心情文が表す感情ラベルのリスト（3名のアノテータによるマルチラベル形式） |

**例: `etc/dialogues/0945.json`**

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


### 👤 話者の性格特性データ

性格特性データには，TIPI-J（日本語版Ten-Item Personality Inventory）[^1]の質問項目と話者による回答，および回答から算出されたBig Fiveのスコアが含まれます．

| キー | 型 | 説明 |
| --- | --- | --- |
| item | dict | 質問項目（i01〜i10） |
| personality | dict | 話者IDをキーとした性格特性データ |
| personality.*.participant_id | str | 話者ID |
| personality.*.response | dict | 各質問項目への回答 |
| personality.*.score | dict | Big Five の各次元のスコア |
| personality.*.score.openness | int | 開放性（2〜14） |
| personality.*.score.conscientiousness | int | 勤勉性（2〜14） |
| personality.*.score.extraversion | int | 外向性（2〜14） |
| personality.*.score.agreeableness | int | 協調性（2〜14） |
| personality.*.score.neuroticism | int | 神経症傾向（2〜14） |

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

### 🗂️ 分割情報

`split.json` には，[論文](#-引用)の実験で使用した Train / Valid / Test の分割情報が記録されています．なお，論文の実験で使用したデータセットには，倫理的観点から不適切と判断し本データセットから除外された対話データも含まれています．

## 🛡️ 本データセットの使用にあたって

> [!CAUTION]
> **本データセットの使用にあたっては，次のことに十分注意してください．**
> - 本データセットのデータから個人を特定しようとしないこと．
> - 本データセットを，特定の話者へのなりすましに用いないこと．
> - 本データセットを話者の性格特性の推定などに用いる際は，自身の情報を推定されたくない話者の権利についても留意すること．


## 📄 引用

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

## 🙇 謝辞

本研究は，科研費 学術変革領域研究（B）（25H01382）の支援を受けました．


## ⚖️ ライセンス

本データセットは [CC BY-NC 4.0][cc-by-nc] の下で提供されます．

[![CC BY-NC 4.0][cc-by-nc-image]][cc-by-nc]



[^1]: Atsushi Oshio, ABE Shingo, and Pino Cutrone. Development, reliability, and validity of the japanese version of ten item personality inventory (tipi-j). Japanese Journal of Personality, Vol. 21, No. 1, 2012.
[^2]: P. Ekman, W. V. Friesen, M. J. O'Sullivan, A. K. Chan, I. Diacoyanni-Tarlatzis, K. G. Heider, R. Krause, W. A. LeCompte, T. K. Pitcairn, P. E. Ricci-Bitti, K. R. Scherer, M. Tomita, and A. Tzavaras. Universals and cultural differences in the judgments of facial expressions of emotion. Vol. 53, pp. 712–717, 1987.
[^3]: Hannah Rashkin, Eric Michael Smith, Margaret Li, and Y-Lan Boureau. Towards empathetic open-domain conversation models: A new benchmark and dataset. In Anna Korhonen, David Traum, and Lluís Màrquez, editors, Proceedings of the 57th Annual Meeting of the Association for Computational Linguistics, pp. 5370–5381, Florence, Italy, July 2019. Association for Computational Linguistics.

[cc-by-nc]: https://creativecommons.org/licenses/by-nc/4.0/
[cc-by-nc-image]: https://licensebuttons.net/l/by-nc/4.0/88x31.png
[cc-by-nc-shield]: https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg
