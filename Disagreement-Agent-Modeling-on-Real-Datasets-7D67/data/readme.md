## Dataset Overview

| Dataset Name | Category/Domain | # Annotators | # Models Evaluated | Label Type | License | Size | Source |
|-------------|----------------|--------------|-------------------|------------|---------|------|--------|
| **MT-Bench Human Judgments** | Code Generation, Multi-turn QA | 3,355 expert annotations (graduate students) | 6 models (GPT-4, GPT-3.5, Claude-v1, Vicuna-13B, Alpaca-13B, LLaMA-13B) | Pairwise preference (A vs B, or tie) | CC-BY-4.0 | 80 questions × 8 categories × multiple model pairs | [HuggingFace](https://huggingface.co/datasets/lmsys/mt_bench_human_judgments) |
| **ConvAbuse** | Conversational Abuse Detection | Multiple crowdworker annotations | 2 conversational agents (CarbonBot, Eliza) | 5-class abuse severity scale (-3 to 1) | CC-BY-4.0 | 1,360 conversations with multi-annotator labels | [Github](https://github.com/amandacurry/convabuse) |
| **QAGS** | Summarization Faithfulness | Multiple crowdworker annotations (MTurk) | 2 summarization systems (CNN/DailyMail, XSum) | Binary (yes/no) | MIT | Multiple articles with 3+ annotators per summary sentence | [GitHub](https://github.com/W4ngatang/qags) |
| **MSLR (Facet-Based Medical Summarization)** | Medical Evidence Summarization | ≥2 expert annotators per summary | Generated medical summaries | Facet-level judgments → 3-class |   Apache License Version 2.0 | Cochrane reviews (subset) | [GitHub](https://github.com/allenai/mslr-annotated-dataset/tree/main) |
---


### Transformation Details by Dataset

#### 1. MT-Bench Human Judgments
**Source Format:**
```json
{
  "question_id": 81,
  "model_a": "gpt-3.5-turbo",
  "model_b": "vicuna-13b-v1.2",
  "winner": "model_a",
  "judge": "author_2",
  "conversation_a": [...],
  "conversation_b": [...]
}
```

**Transformations:**
- Extract `question_id` → `item_id`
- Map `conversation_a[0]["content"]` → `prompt`
- Create two response objects for `model_a` and `model_b`
- Map `winner` → annotation label (convert "model_a"/"model_b"/"tie" to 0/1/2)
- Filter for coding category questions (questions 71-80)
- Add `domain: "code"` to metadata


**Dataset Citation:**
```bibtex
@misc{zheng2023judging,
      title={Judging LLM-as-a-judge with MT-Bench and Chatbot Arena}, 
      author={Lianmin Zheng and Wei-Lin Chiang and Ying Sheng and Siyuan Zhuang and Zhanghao Wu and Yonghao Zhuang and Zi Lin and Zhuohan Li and Dacheng Li and Eric. P Xing and Hao Zhang and Joseph E. Gonzalez and Ion Stoica},
      year={2023},
      eprint={2306.05685},
      archivePrefix={arXiv},
      primaryClass={cs.CL}
}
```

#### 2. ConvAbuse
**Source Format:**
```csv
example_no,annotator_id,conv_id,prev_agent,prev_user,agent,user,bot,is_abuse.1,is_abuse.0,is_abuse.-1,is_abuse.-2,is_abuse.-3,...
123,ann_001,conv_45,"How are you?","I'm fine","That's great","You're stupid",CarbonBot,0,0,1,0,0,...
```

**Original Label Scale:**
- `1`: Not abusive
- `0`: Ambiguous
- `-1`: Mildly abusive
- `-2`: Strongly abusive
- `-3`: Very strongly abusive

**Transformations:**
- Extract `conv_id` → `item_id` (prefixed with "convabuse_")
- Extract `bot` → `agent_id` (CarbonBot or Eliza)
- Map `annotator_id` → `annotator_id`
- Decode one-hot encoded `is_abuse.X` columns to get severity value
- Convert abuse scale to 3-class labels:
  - `1, 0` → `0` (no abuse/ambiguous - incorrect)
  - `-1` → `1` (mildly abusive - partial)
  - `-2, -3` → `2` (strongly/very strongly abusive - correct)
- Deduplicate by (annotator_id, conv_id) keeping first occurrence

**Dataset Citation:**
```bibtex
@inproceedings{cercas-curry-etal-2021-convabuse,
title = "{C}onv{A}buse: Data, Analysis, and Benchmarks for Nuanced Abuse Detection in Conversational {AI}",
author = "Cercas Curry, Amanda and
Abercrombie, Gavin and
Rieser, Verena",
booktitle = "Proceedings of the 2021 Conference on Empirical Methods in Natural Language Processing",
month = nov,
year = "2021",
address = "Online and Punta Cana, Dominican Republic",
publisher = "Association for Computational Linguistics",
url = "https://aclanthology.org/2021.emnlp-main.587",
doi = "10.18653/v1/2021.emnlp-main.587",
pages = "7388--7403"
}
```

#### 3. Measuring Hate Speech
**Source Format:**
```json
{
  "comment_id": "xyz123",
  "annotator_id": 456,
  "platform": "YouTube",
  "hate_speech_score": 0.75,
  "text": "example comment..."
}
```

**Original Label Scale:**
- Continuous `hate_speech_score` where:
  - `> 0.5` = hate speech
  - `0.0 to 0.5` = neutral/ambiguous
  - `< 0.0` = counter/supportive speech

**Transformations:**
- Extract `comment_id` → `item_id` (prefixed with "mhs_")
- Extract `platform` → `agent_id` (YouTube, Reddit, Twitter)
- Map `annotator_id` → `annotator_id`
- Convert continuous hate speech score to 3-class labels:
  - `< 0.0` → `0` (not hateful)
  - `0.0 - 0.5` → `1` (ambiguous)
  - `> 0.5` → `2` (hateful)
- **Balanced platform sampling:**
  1. Filter items with ≥10 distinct annotators per item
  2. Select items from each of the 4 platforms (300 items total)
  3. Keep exactly 5 annotators per item (first 5 annotator_id for consistency)
  4. Result: 300 items × 5 annotators

**Dataset Citation:**
```bibtex
@article{kennedy2020constructing,
  title={Constructing interval variables via faceted Rasch measurement and multitask deep learning: a hate speech application},
  author={Kennedy, Chris J and Bacon, Geoff and Sahn, Alexander and von Vacano, Claudia},
  journal={arXiv preprint arXiv:2009.10277},
  year={2020}
}
```
---

#### 4. QAGS (Question Answering and Generation for Summarization)
**Source Format:**
```json
{
  "article": "Article text...",
  "summary_sentences": [
    {
      "sentence": "Summary sentence 1.",
      "responses": [
        {"worker_id": 0, "response": "yes"},
        {"worker_id": 3, "response": "no"},
        {"worker_id": 25, "response": "yes"}
      ]
    },
    {
      "sentence": "Summary sentence 2.",
      "responses": [...]
    }
  ]
}
```

**Original Label Scale:**
- Binary judgments on whether summary sentence is supported by the article:
  - `"yes"`: Sentence is supported/faithful to the article
  - `"no"`: Sentence is not supported/unfaithful to the article

**Transformations:**
- Create unique `item_id` for each summary sentence: `{dataset}_{article_idx}_sent{sentence_idx}`
  - Example: `cnn_1_sent0`, `xsum_42_sent2`
- Extract dataset source → `agent_id` (either "CNN" or "XSUM")
- Map `worker_id` → `annotator_id` (formatted as `worker_{id}`)
- Convert binary response to label:
  - `"yes"` → `1` (faithful/supported)
  - `"no"` → `0` (unfaithful/not supported)
- Process both CNN/DailyMail and XSum datasets from separate JSONL files

**Dataset Citation:**
```bibtex
@article{wang2020asking,
   title={Asking and Answering Questions to Evaluate the Factual Consistency of Summaries},
   url={http://dx.doi.org/10.18653/v1/2020.acl-main.450},
   DOI={10.18653/v1/2020.acl-main.450},
   journal={Proceedings of the 58th Annual Meeting of the Association for Computational Linguistics},
   publisher={Association for Computational Linguistics},
   author={Wang, Alex and Cho, Kyunghyun and Lewis, Mike},
   year={2020}
}
```
#### 5. MSLR (Facet-Based Medical Summarization)

**Source Format:**
TSV expert annotation files

Each row = one expert annotator’s judgment of a generated medical summary.

- Facet Judgments Include:
- Fluency
- Population agreement
- Intervention agreement
- Outcome agreement
- Effect direction
- Strength of claim

**Transformations:**
- ReviewID → item_id (prefixed mslr_)
- Generated summary → agent_id
- ExpID → annotator_id
- Facets collapsed into 3-class label:
    - 0 = incorrect
    - 1 = partially correct
    - 2 = correct
- All expert annotations retained (no aggregation)

**Dataset Citation:**
```bibtex
@article{zhang2022mslr,
  title={Evaluating Factual Consistency of Medical Evidence Summaries},
  author={Zhang, Yanjun and Wallace, Byron and Nenkova, Ani and others},
  journal={Transactions of the Association for Computational Linguistics},
  year={2022}
}
```
---


## Data Loading and Conversion

### Installation
```bash
pip install datasets pandas
```

### Convert Datasets to Schema Format
```bash
# Convert MT-Bench
python convert_datasets.py --dataset mtbench

# Convert ConvAbuse
python convert_datasets.py --dataset convabuse

# Convert Measuring Hate Speech
python convert_datasets.py --dataset measuring-hate-speech

# Convert QAGS
python unified_dataset_converter.py --dataset qags

# Convert Mslr
python convert_datasets.py --dataset mslr-facet --mslr-annotations-dir raw/mslr-annotated-dataset/data/Annotations
```


---

## Task Types

Each converted example includes a `task_type` field indicating the underlying task:

The `task_type` values are extracted from the original datasets where possible:
- **MT-Bench**: inferred from the `category` field (e.g., coding, writing, math, reasoning, roleplay, extraction, STEM, humanities)
- **ConvAbuse**: `abuse_detection` (detecting and categorizing nuanced abuse in conversational AI interactions)
- **Measuring Hate Speech**: `hate_speech_detection` (rating severity of hate speech in social media comments across platforms)
- **QAGS**: `summarization_faithfulness` (evaluating whether generated summary sentences are supported by source articles)
- **MSLR (Facet-Based Medical Summarization)**: `medical_factual_consistency`  
  Assessing factual consistency of generated medical summaries against expert-written reference summaries, using **facet-level expert judgments** (population, intervention, outcome, effect direction, and claim strength)