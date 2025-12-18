# Human Evaluation Protocol for Visual Pun Recognition

## Overview

This document describes the human evaluation protocol used to validate the automatic MLLM-based evaluation metric in our visual pun generation framework. The study assesses whether human judgments of idiom recognition align with our automatic metric.

## 1. Sample Selection

### 1.1 Random Sampling Procedure

We randomly sampled 100 images from our generated dataset using the following procedure:

```python
import random

# Set seed for reproducibility
RANDOM_SEED = 42
random.seed(RANDOM_SEED)

# Full list of 1,000 idiom identifiers
all_idiom_ids = list(range(1000))

# Random sample of 100 idioms
sampled_ids = sorted(random.sample(all_idiom_ids, 100))
```

The complete list of sampled idiom IDs is provided in `sampled_idioms.csv`.

### 1.2 Image Selection

For each sampled idiom, we use the final generated image from our iterative pipeline (i.e., the image from the last iteration before stopping, whether due to successful recognition or reaching the 5-iteration limit).

## 2. Annotator Recruitment

### 2.1 Eligibility Criteria

- Native or fluent English speakers
- No prior involvement in this research project
- Familiarity with common English idioms

### 2.2 Annotator Information

| Annotator ID | Background | English Proficiency |
|--------------|------------|---------------------|
| A1 | [To be filled] | [Native/Fluent] |
| A2 | [To be filled] | [Native/Fluent] |
| A3 | [To be filled] | [Native/Fluent] |

## 3. Annotation Task Design

### 3.1 Task Description

Annotators are shown an image and asked to identify which idiom the image represents. This is designed as an **open-ended recognition task** (not multiple choice) to mirror our automatic evaluation setup.


### 3.2 Annotation Interface

The updated Idiom Annotator interface provides a split-view workspace designed for high-efficiency labeling and easy dataset navigation.

![platform](https://github.com/user-attachments/assets/b64d8195-9ea3-4b5b-be96-0fcf4e217bf4)


The interface consists of the following components:

* **Thumbnail Navigation Grid (Left)**: A scrollable $10 \times 10$ grid displaying all images in the loaded folder. Completed annotations are marked with a large green checkmark for clear progress tracking.
* **Active Editor Panel (Right)**:
    * **Image Metadata**: Displays the current image file name and numerical progress (e.g., `44 / 100`).
    * **Main Display**: The active visual pun image is centered for clear inspection.
    * **Annotation Input**: A text field where users enter the identified English idiom. Changes are saved automatically to the browser's local storage upon navigation.
* **Global Toolbar**: A persistent header containing the centered tool title and a **Download CSV** button to export the final $100$ ID/value pairs.
* **Navigation Controls**: Includes **Previous** and **Submit & Next** buttons, along with keyboard support (Enter key) to iterate through the folder.

### 3.3 Instructions Provided to Annotators

The following instructions are shown to annotators before beginning the task:

---

**Task: Idiom Recognition from Images**

You will be shown a series of images. Each image is designed to visually represent an English idiom (e.g., "a bull in a china shop," "break the ice," "spill the beans").

**Your task:** For each image, type the English idiom you believe the image represents.

**Guidelines:**
1. Write the idiom in its common form (e.g., "kill two birds with one stone" rather than "killing two birds").
2. If you are unsure, provide your best guess.
3. If you cannot identify any idiom, type "unrecognizable."
4. Do not use external resources (e.g., internet search).

**Time:** There is no strict time limit, but each image should take approximately 30-60 seconds.

---

### 3.4 Practice Round

Before the main annotation, annotators complete 5 practice examples (not from the sampled 100) to familiarize themselves with the task. Correct answers are revealed after the practice round.

![practice](https://github.com/user-attachments/assets/76930023-f6de-483f-ad05-b1e864ac66c6)



Practice idioms used:
1. "raining cats and dogs"
2. "elephant in the room"
3. "piece of cake"
4. "crocodile tears"
5. "let the cat out of the bag"
6. "break a leg"

## 4. Annotation Procedure

### 4.1 Session Structure

1. **Introduction** (5 min): Annotators read instructions and sign consent form
2. **Practice round** (5 min): 5 practice images with feedback
3. **Main annotation** (50-70 min): 100 images, self-paced
4. **Break**: Optional break after 50 images
5. **Debrief** (5 min): Optional feedback on task difficulty

### 4.2 Randomization

Image presentation order is randomized independently for each annotator using their annotator ID as a secondary seed:

```python
import random

def get_presentation_order(annotator_id, sampled_ids):
    random.seed(RANDOM_SEED + hash(annotator_id))
    order = sampled_ids.copy()
    random.shuffle(order)
    return order
```

### 4.3 Blinding

Annotators are not informed of:
- The target idiom for each image
- The MLLM recognition result
- Performance of other annotators

## 5. Response Normalization

### 5.1 Canonicalization Procedure

Human responses are normalized to enable comparison with target idioms:

1. **Lowercase conversion**: "Break The Ice" → "break the ice"
2. **Article removal**: "a bull in a china shop" → "bull in china shop"
3. **Punctuation removal**: "it's raining cats and dogs!" → "its raining cats and dogs"
4. **Common variant mapping**: Manual mapping of equivalent forms
   - "can't teach an old dog new tricks" ↔ "you cannot teach an old dog new tricks"
   - "the ball is in your court" ↔ "ball is in someone's court"

The complete variant mapping is provided in `idiom_variants.json`.

### 5.2 Match Criteria

A response is considered **correct** if, after canonicalization:
- Exact string match with target idiom, OR
- Listed as an equivalent variant in `idiom_variants.json`

Borderline cases are resolved by majority vote among the research team (excluding annotators).

## 6. Evaluation Metrics

### 6.1 Inter-Annotator Agreement

We compute Fleiss' kappa (κ) to measure agreement among three annotators:

$$\kappa = \frac{\bar{P} - \bar{P}_e}{1 - \bar{P}_e}$$

where $\bar{P}$ is the observed agreement and $\bar{P}_e$ is the expected agreement by chance.

Interpretation scale (Landis & Koch, 1977):
- κ < 0.00: Poor
- 0.00 ≤ κ < 0.20: Slight
- 0.20 ≤ κ < 0.40: Fair
- 0.40 ≤ κ < 0.60: Moderate
- 0.60 ≤ κ < 0.80: Substantial
- 0.80 ≤ κ ≤ 1.00: Almost perfect

### 6.2 Human Accuracy

For each image, we compute:
- **Individual accuracy**: Whether each annotator correctly identified the idiom
- **Majority accuracy**: Whether ≥2 of 3 annotators correctly identified the idiom
- **Unanimous accuracy**: Whether all 3 annotators correctly identified the idiom

### 6.3 Correlation with Automatic Metric

We compute correlation between human judgment and MLLM-based automatic evaluation:

| Metric | Description |
|--------|-------------|
| **Agreement rate** | Percentage of images where human majority vote matches MLLM judgment |
| **Cohen's kappa** | Agreement correcting for chance (human majority vs. MLLM) |

### 6.4 Error Analysis

We categorize disagreements between human and automatic evaluation:
1. **Human correct, MLLM incorrect**: MLLM false negatives
2. **Human incorrect, MLLM correct**: Potential MLLM hallucination or overly lenient matching
3. **Both incorrect**: Genuine generation failures

## 7. Data Recording

### 7.1 Raw Data Format

All responses are recorded in `annotations_raw.csv`:

| Column | Description |
|--------|-------------|
| `image_id` | Unique identifier for the image |
| `idiom_id` | Index of the target idiom (0-999) |
| `target_idiom` | Ground truth idiom string |
| `annotator_id` | Annotator identifier (A1/A2/A3) |
| `response_raw` | Annotator's original response |
| `response_normalized` | Response after canonicalization |
| `is_correct` | Boolean: whether response matches target |
| `timestamp` | Annotation timestamp |


### 7.2 Aggregated Data Format

Summary statistics are provided in `annotations_summary.csv`:

| Column | Description |
|--------|-------------|
| `image_id` | Unique identifier for the image |
| `idiom_id` | Index of the target idiom |
| `target_idiom` | Ground truth idiom string |
| `num_correct` | Number of annotators who identified correctly (0-3) |
| `majority_correct` | Boolean: ≥2 annotators correct |
| `mllm_correct` | Boolean: MLLM automatic evaluation result |
| `human_mllm_agree` | Boolean: majority vote matches MLLM |

## 8. Results

### 8.1 Inter-Annotator Agreement

| Metric | Value |
|--------|-------|
| Fleiss' κ (all three annotators) | [To be filled] |
| Cohen's κ (A1 vs A2) | [To be filled] |
| Cohen's κ (A1 vs A3) | [To be filled] |
| Cohen's κ (A2 vs A3) | [To be filled] |

### 8.2 Human Recognition Accuracy

| Metric | Value |
|--------|-------|
| Mean individual accuracy | [To be filled]% |
| Majority accuracy (≥2/3 correct) | [To be filled]% |
| Unanimous accuracy (3/3 correct) | [To be filled]% |

### 8.3 Human–MLLM Correlation

| Metric | Value |
|--------|-------|
| Agreement rate | [To be filled]% |
| Cohen's κ (human majority vs MLLM) | [To be filled] |

### 8.4 Error Analysis

|  | MLLM Correct | MLLM Incorrect |
|--|--------------|----------------|
| **Human Majority Correct** | [n] | [n] |
| **Human Majority Incorrect** | [n] | [n] |

### 8.5 Qualitative Examples

[PLACEHOLDER IMAGE: 2x2 grid showing example images from each cell of the confusion matrix above, with captions indicating target idiom, human responses, and MLLM response]

## 9. Ethical Considerations

- Annotators provided informed consent before participation
- No personally identifiable information is collected beyond annotator IDs
- Annotators may withdraw at any time without penalty
- Compensation: [Describe compensation, e.g., "$15 USD for approximately 1 hour"]

## 10. Files in This Directory

| File | Description |
|------|-------------|
| `README.md` | This protocol document |
| `sampled_idioms.csv` | List of 100 randomly sampled idiom IDs and strings |
| `idiom_variants.json` | Mapping of equivalent idiom phrasings |
| `annotations_raw.csv` | Raw annotation data |
| `annotations_summary.csv` | Aggregated results per image |
| `analysis.py` | Script to compute all metrics |
| `images/` | Directory containing the 100 sampled images |

## References

- Landis, J. R., & Koch, G. G. (1977). The measurement of observer agreement for categorical data. *Biometrics*, 33(1), 159-174.
- Fleiss, J. L. (1971). Measuring nominal scale agreement among many raters. *Psychological Bulletin*, 76(5), 378-382.
```
