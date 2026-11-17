# Kansai Annotated Travel Reviews (2020–2024)

**A dataset of 216,132 travel-review records from 48 attractions across six areas of Japan's Kansai region, with human-annotated sentiment and LLM-assigned tourism topics.**

This repository accompanies the data article **“An annotated travel review dataset from 48 attractions across the Kansai region of Japan, 2020–2024”** (Tai Dinh; manuscript). It contains the consolidated review workbook used in the study.

## Dataset at a glance

| Characteristic | Description |
| --- | --- |
| Number of review records | **216,132** |
| Attractions | **48** |
| Geographic areas | **Kyoto, Osaka, Kobe, Nara, Mie, Shiga** |
| Recorded review years | **2020–2024** |
| Text | English-language reviews, including translated non-English reviews |
| Star ratings | 1–5 |
| Human sentiment | Positive, neutral, negative (one consensus label per review) |
| Tourism topics | 12 predefined categories (one per review, assigned with GPT-5 mini) |
| Fields | 12 columns in the analysis workbook |
| Unit of observation | One review record |

**Geographic terminology:** Kyoto, Osaka, Kobe, and Nara are city labels in the data; Mie and Shiga are prefecture labels. The six values are therefore described as **areas**, not six equivalent cities.

## Repository contents

| File | Purpose |
| --- | --- |
| [Kansai_full.xlsx](Kansai_full.xlsx) | Combined annotated review dataset |
| [README.md](README.md) | Dataset description and usage notes |
| [.gitignore](.gitignore) | Git tracking exclusions |

The repository currently supplies the combined workbook. The six area-specific working spreadsheets and original-language review texts are **not listed as separate downloadable files here**.

## Dataset schema

The manuscript describes the following fields in the consolidated analysis workbook.

| Column | Description |
| --- | --- |
| review_ID | Record identifier, unique in the combined workbook |
| reviewer_profile | Link to the original reviewer profile when available; **potentially identifying** |
| rating | Observed star rating, integer from 1 to 5 |
| review_text | Review text as stored in English |
| year | Recorded review year (2020–2024); not a complete timestamp |
| human_sentiment | Consensus human sentiment label: positive, neutral, or negative |
| location | Attraction or point-of-interest name |
| city | Area label: Kyoto, Osaka, Kobe, Nara, Mie, or Shiga |
| assigned_topic | Single tourism topic label selected from the 12-category taxonomy |
| chatgpt_prediction | Separately stored machine-generated, three-class sentiment prediction |
| sentiment_score | Numeric sentiment-related output; its scoring formula is not encoded in the workbook |
| confidence | Numeric machine-related output; do not assume it is a calibrated probability |

The original workbook was preserved for analysis. **Some human_sentiment entries have inconsistent capitalization**; normalize case in downstream analysis rather than treating such variants as different classes.

## Data collection and preparation

The study collected online reviews associated with selected tourist attractions in the Kansai region from **Google Maps and Tripadvisor**. Attraction selection considered visitor prominence, variation in attraction types, and the availability of review records.

The preparation workflow comprised collection, translation into English when applicable, handling empty texts, consolidation of source records, and duplicate removal. The final review text retains mixed capitalization and common stopwords; it should not be treated as a uniformly lowercased or stopword-filtered corpus.

The consolidated table does **not** include per-record source-platform identifiers, full publication timestamps, or the corresponding original-language texts.

## Annotation methodology

### Human sentiment labels

A manual sentiment-annotation campaign involved **24 annotators**. Reviews were partitioned into non-overlapping subsets, with each subset assigned to a pair of annotators. Each annotator independently assigned one sentiment label — **positive, neutral, or negative** — based on the review text. AI-assisted sentiment annotation was not permitted. Paired annotators compared their judgments and resolved disagreements through discussion, retaining one consensus label in **human_sentiment**.

The downloadable combined table does not include individual pre-consensus decisions or disagreement logs; an independent pre-adjudication inter-annotator agreement measure cannot be recovered from that file.

### Taxonomy-based topic labels

Topic labels were assigned using **GPT-5 mini through the OpenAI Batch API**, rather than human sentiment annotators or unsupervised topic discovery. Each review received **one** category corresponding to its principal subject from the following predefined taxonomy:

1. Cultural Heritage & Historical Sites
2. Nature & Scenic Spots
3. Crowds & Accessibility
4. Cleanliness & Maintenance
5. Atmosphere & Ambience
6. Service & Hospitality
7. Price & Value for Money
8. Local Cuisine & Food Experience
9. Shopping & Souvenirs
10. Events & Festivals
11. Transportation & Connectivity
12. Entertainment & Nightlife

The topic is stored in **assigned_topic**. The three-class human sentiment label and the machine-generated sentiment fields are distinct from this topic classification.

## Descriptive statistics and data checks

The following statistics were calculated for the finalized **216,132-row analysis workbook** described in the manuscript.

### Records by geographic area

| Area | Reviews |
| --- | ---: |
| Kyoto | 80,806 |
| Osaka | 66,040 |
| Mie | 22,816 |
| Nara | 21,385 |
| Kobe | 15,653 |
| Shiga | 9,432 |
| **Total** | **216,132** |

### Records by review year

| Year | Reviews |
| --- | ---: |
| 2020 | 39,230 |
| 2021 | 19,972 |
| 2022 | 29,025 |
| 2023 | 61,477 |
| 2024 | 66,428 |
| **Total** | **216,132** |

### Human sentiment distribution

| Sentiment | Reviews | Share |
| --- | ---: | ---: |
| Positive | 150,481 | 69.6% |
| Neutral | 39,356 | 18.2% |
| Negative | 26,295 | 12.2% |
| **Total** | **216,132** | **100%** |

In the manuscript's analysis workbook, the 11 non-profile fields contained no missing values, review_ID values were unique, and no duplicates remained under the five-field key **(rating, review_text, year, location, city)** after trimming outer whitespace. There were **41** case-inconsistent sentiment labels and **37** blank reviewer-profile fields. These are observations about the analyzed workbook; the uploaded public file should be checked independently if it has undergone further modification.

The recorded review and sentiment proportions describe **this collected corpus**; they are not population estimates for tourists, attractions, or visitor satisfaction in the Kansai region.

## Example: reading the workbook in Python

~~~python
import pandas as pd

df = pd.read_excel("Kansai_full.xlsx")

print("Rows and columns:", df.shape)
print("Area counts:")
print(df["city"].value_counts())

# Normalize case for analysis without changing the original file.
sentiment = df["human_sentiment"].astype("string").str.strip().str.lower()
print("Sentiment counts:")
print(sentiment.value_counts(dropna=False))

print("Topic counts:")
print(df["assigned_topic"].value_counts())
~~~

The examples above are exploratory checks only; they do not reproduce every analysis in the data article.

## Intended research uses and limitations

The dataset may support investigations of tourism-review sentiment, taxonomy-based topic classification, topic–sentiment relationships, geographic/year-based corpus partitions, and comparisons between stored human and machine labels.

Interpret the data with these constraints in mind:

- Reviews are self-selected and attractions were purposively selected; the corpus is not a probability sample of visitors.
- The **year** field does not preserve full review dates.
- The **city** column mixes city and prefecture labels.
- The combined file does not preserve source-platform labels or original-language texts.
- Human annotation consensus labels are available, but individual annotator decisions are not included.
- The provenance and calibration of the auxiliary machine sentiment-score fields are not established by the workbook alone.
- Exact agreement between **human_sentiment** and **chatgpt_prediction** must not be treated as independently verified model accuracy.

## Privacy, third-party rights, and reuse

**Important:** The study's working workbook included a **reviewer_profile** field containing links that can identify online reviewers. Before redistributing or publishing derived record-level files, inspect the workbook and remove direct reviewer identifiers, and check review texts for personal information. Do not assume a public upload is anonymized.

Google Maps and Tripadvisor reviews are third-party user-generated content. Public visibility on a platform does not, by itself, establish permission to redistribute that content. Users are responsible for complying with applicable law and platform terms. **No reuse license is specified in this repository at present; public access alone does not grant copyright or redistribution permissions.**

## Citation

**Dataset / repository (until a versioned archival DOI is available):**

Dinh, T. *Kansai Annotated Travel Reviews (2020–2024): 216,132 Reviews from 48 Attractions.* GitHub repository. https://github.com/taiduydinh/kansai-annotated-travel-reviews

**Accompanying data article:**

Dinh, T. *An annotated travel review dataset from 48 attractions across the Kansai region of Japan, 2020–2024.* Manuscript prepared for *Data in Brief*. A journal citation and DOI have not been assigned.

For reproducible scholarly citation, use a fixed Git commit or archived release when available rather than relying only on the moving default branch.

## Contact

**Tai Dinh**  
The Kyoto College of Graduate Studies for Informatics, Kyoto, Japan  
Email: [t_dinh@kcg.ac.jp](mailto:t_dinh@kcg.ac.jp)
