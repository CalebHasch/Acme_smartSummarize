# Acme SmartSummarize: Dialogue Summarization Proof of Concept

## Project Overview

Acme Communications is exploring automated conversation summarization to help users manage information overload in group messaging platforms.

When users return to conversations containing numerous unread messages, identifying important information can be time-consuming. An automated summarization feature could help users quickly understand the main topics, decisions, and follow-up actions without reviewing every message.

This project develops and evaluates two dialogue summarization approaches using the SAMSum dataset:

1. **BERT2BERT:** A lightweight neural encoder-decoder architecture.
2. **Refined TextRank:** An extractive summarization approach using TF-IDF similarity and PageRank.

The goal is to identify an effective, computationally practical solution while evaluating both technical performance and potential business usefulness.

## Business Problem and Objectives

**Problem:** Users may miss important information when catching up on lengthy group conversations.

**Business objective:** Reduce information overload and improve the conversation catch-up experience through automated summarization.

The project evaluates whether summarization models can:

- Identify the main topic of a conversation.
- Preserve important information and follow-up actions.
- Avoid unsupported or misleading statements.
- Generate concise summaries efficiently.
- Operate within CPU-only computing constraints.

## Dataset

The project uses the **SAMSum dataset**, which contains informal messenger-style conversations paired with human-written summaries.

SAMSum provides a suitable environment for investigating dialogue summarization because it includes conversational language, multiple speakers, and concise reference summaries.

For this proof of concept, the BERT2BERT model was trained using 1,000 conversations and validated using 100 conversations.

Final model comparisons were conducted on 100 held-out test conversations.

A reproducible sample of 20 test conversations was selected for manual qualitative evaluation.

## Technical Approach

### 1. BERT2BERT Encoder-Decoder

The neural summarization model uses a lightweight BERT-based encoder-decoder architecture.

The implementation uses the `google/bert_uncased_L-2_H-128_A-2` configuration for both the encoder and decoder, with approximately 8.9 million parameters.

The model was trained on CPU for 10 epochs.

Training included loss monitoring, validation, checkpointing, and model evaluation.

The best checkpoint was selected using validation performance.

The objective was to investigate whether a compact neural architecture could learn useful abstractive summaries with limited computational resources.

### 2. Refined TextRank Baseline

The extractive baseline uses:

- TF-IDF representations of dialogue messages.
- Cosine similarity to measure message relationships.
- PageRank to identify important messages.
- Filtering of file attachments and very short utterances.
- A small information-density adjustment.
- Selection of three relevant utterances.

The original TextRank implementation was refined using validation results.

The final version was selected based on improved validation ROUGE-L performance.

Unlike the neural model, TextRank selects existing dialogue statements rather than generating new text.

## Evaluation Methodology

Both approaches were evaluated using the same 100 test conversations.

### Quantitative Evaluation

The following metrics were used:

- **ROUGE-1:** Unigram overlap with reference summaries.
- **ROUGE-2:** Bigram overlap with reference summaries.
- **ROUGE-L:** Longest common subsequence overlap.
- **Compression:** Percentage reduction in text length.
- **CPU inference time:** Average processing time per conversation.

### Qualitative Evaluation

A sample of 20 conversations was manually reviewed using four binary criteria:

1. Main topic captured.
2. Important details preserved.
3. Unsupported information present.
4. Excessive repetition present.

The review emphasized the original dialogue rather than relying exclusively on reference summaries.

## Results

### Quantitative Model Comparison

| Metric | BERT2BERT | Refined TextRank |
|---|---:|---:|
| ROUGE-1 | 0.2095 | **0.2876** |
| ROUGE-2 | 0.0384 | **0.0775** |
| ROUGE-L | 0.1761 | **0.2242** |
| Compression | 81.89% | 56.32% |
| CPU inference time | 0.2092 s | **0.0023 s** |

Refined TextRank achieved higher ROUGE scores across all three metrics.

It was also approximately 91 times faster during CPU inference.

Although BERT2BERT generated more compressed summaries, this did not result in better information preservation.

### Qualitative Model Comparison

| Criterion | BERT2BERT | Refined TextRank |
|---|---:|---:|
| Main topic captured | 10% | **75%** |
| Important details preserved | 0% | **25%** |
| Unsupported information | 95% | **0%** |
| Excessive repetition | 65% | **0%** |

Refined TextRank demonstrated substantially better factual reliability and topic coverage.

However, its ability to preserve important details remained limited.

### Key Findings

**BERT2BERT**

The lightweight neural model frequently produced unsupported information, repetitive wording, and incorrect descriptions of conversations.

Its performance suggests that the available training data and computational resources were insufficient for reliable abstractive summarization with this architecture.

**Refined TextRank**

The extractive model performed better at identifying central topics and avoided unsupported information in the manually reviewed sample.

However, it frequently omitted decisions, deadlines, answers, and follow-up actions.

These omissions demonstrate that factual accuracy alone does not guarantee a useful conversation summary.

## Final Model Selection

**Refined TextRank was selected as the stronger proof-of-concept model.**

This decision was based on its higher ROUGE scores, better qualitative performance, and significantly faster CPU inference.

The results demonstrate that a simpler approach can outperform a more complex neural model when training data and computational resources are limited.

However, neither model is recommended for unrestricted production deployment in its current form.

## Business Implications

The results suggest that automated summarization could help Acme Communications reduce the effort required to review missed conversations.

Refined TextRank provides a practical starting point because it is computationally efficient and produces factually grounded extracts.

Nevertheless, the qualitative evaluation identified a significant business limitation: summaries sometimes captured the conversation topic without preserving the information users would need to act.

For example, summaries could omit meeting times, scheduling restrictions, answers to questions, or agreed-upon next steps.

Improving the preservation of actionable information should therefore be the primary focus of future development.

## Limitations

**Limited training resources:** The neural model was trained using a relatively small dataset and lightweight architecture in a CPU-only environment.

**Dataset representativeness:** SAMSum conversations may not fully represent longer, more complex conversations encountered on real communication platforms.

**Evaluation sample size:** Quantitative testing covered 100 conversations, while qualitative evaluation covered 20 conversations.

**Manual evaluation:** Binary qualitative ratings involve subjective judgment and were not independently verified by multiple reviewers.

**Reference quality:** Some dialogues and reference summaries contained inconsistencies.

**Test-set independence:** One test conversation was inspected during TextRank refinement, slightly limiting the independence of the final test evaluation.

**Information completeness:** Refined TextRank frequently omitted important decisions, deadlines, and answers.

## Future Improvements

Future development should prioritize:

1. **Decision and action-item extraction:** Identify commitments, deadlines, confirmations, and follow-up actions.
2. **Conversation structure:** Better account for relationships between questions and answers.
3. **Pretrained neural models:** Explore stronger summarization architectures such as T5 or BART when additional computing resources are available.
4. **Business-oriented evaluation:** Measure action-item recall, factual consistency, user comprehension, and time saved.
5. **Larger-scale testing:** Evaluate on more representative conversations with additional human reviewers.

A future pilot could present summaries as optional conversation previews while maintaining access to the original messages.

## Running the Project

### Requirements

- Python 3
- Jupyter Notebook or JupyterLab
- Required Python packages listed in `requirements.txt`
- CPU-based execution supported

### Setup

Clone the repository:

```bash
git clone https://github.com/CalebHasch/Acme_smartSummarize.git
cd Acme_smartSummarize
```

Create a virtual environment:

```bash
python -m venv .venv
```

Activate the environment.

On macOS or Linux:

```bash
source .venv/bin/activate
```

On Windows:

```bash
.venv\Scripts\activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Launch Jupyter:

```bash
jupyter lab
```

Open the project notebook in the `notebooks/` directory and execute the cells in order.

### Model Checkpoints

Model weights are optional for this submission.

If pretrained or fine-tuned checkpoints are not included in the repository, follow the notebook's training and checkpointing workflow to reproduce the neural model.

Training was designed to run on CPU, although execution time will vary depending on hardware.

## Conclusion

This project implemented and evaluated neural and extractive approaches to dialogue summarization for Acme Communications.

Refined TextRank outperformed the lightweight BERT2BERT model in quantitative metrics, factual reliability, topic coverage, and computational efficiency.

However, qualitative evaluation revealed that extractive summaries can still omit information necessary for users to understand decisions and follow-up actions.

The project establishes a practical baseline for automated dialogue summarization and identifies information completeness as the most important area for future development.

## References

- Gliwa, B., Mochol, I., Biesek, M., & Wawer, A. (2019). *SAMSum Corpus: A Human-annotated Dialogue Dataset for Abstractive Summarization.*
- Devlin, J., Chang, M.-W., Lee, K., & Toutanova, K. (2019). *BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding.*
- Mihalcea, R., & Tarau, P. (2004). *TextRank: Bringing Order into Text.*
- Hugging Face Transformers documentation: https://huggingface.co/docs/transformers
- Scikit-learn documentation: https://scikit-learn.org/stable/
