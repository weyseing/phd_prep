# Heng Wey Seing (Jeremy)

- Petaling Jaya, Selangor 47301, Malaysia
- +60 18-379 2007 &nbsp;·&nbsp; hengweyseing53@gmail.com
- [linkedin.com/in/jeremy-heng-98b95a228](https://www.linkedin.com/in/jeremy-heng-98b95a228/) &nbsp;·&nbsp; [github.com/weyseing](https://github.com/weyseing)

---

## Research interests

Counterfactual reasoning over foundation-model time-series · Self-supervised predictive models · Time-series forecasting with covariates · Applied AI for Malaysian public-health surveillance and infrastructure forecasting (dengue, electricity demand) · Decision-support systems for public-sector stakeholders.

---

## Education

**Master of Science in Data Science** — *Sunway University, Malaysia* &nbsp;&nbsp; *(dissertation submitted Apr 2026; final result pending release)*
Supervisor: **Prof. Dr Selina Low Yeh Ching**
CGPA 3.96 / 4.00 *(provisional — 11 of 12 modules graded; A+ in 11, A in 1)*
Top-grade modules (A+) include **Forecasting Analytics, Artificial Intelligence, Statistical Methods for Data Science, Research Methodology, Data Mining, Big Data Management, and Data Science Capstone Project 1**.
Dissertation: *Forecasting Mental Health Sentiment Surges: A Time-Series Analysis of Reddit Discourse.*

**Bachelor of Science in Electrical & Electronics Engineering** — *Wawasan Open University, Malaysia* &nbsp;&nbsp; *(Nov 2023)*
&nbsp;&nbsp;CGPA 3.64 / 4.00 (First-Class Honours)

**Diploma in Electrical & Electronics Engineering** — *KDU University College, Malaysia* &nbsp;&nbsp; *(Jan 2017)*
&nbsp;&nbsp;CGPA 3.66 / 4.00

---

## Research experience

**MSc Dissertation** — *Sunway University* &nbsp;&nbsp; *(2024 – Apr 2026)*
*Forecasting Mental Health Sentiment Surges: A Time-Series Analysis of Reddit Discourse.*
- Built an end-to-end pipeline collecting **138,058 Reddit posts** (2024–2025, 705 days) from four student-mental-health subreddits (r/college, r/Students, r/GradSchool, r/mentalhealth, student-context filtered) via the **Arctic Shift API**; classified each post as "stressed" or not using a hybrid **VADER + RoBERTa** scheme with disagreement flagging for manual review.
- Aggregated classified posts into a 705-day stress-count time-series; fitted **Poisson and Negative Binomial GLMs** with calendar covariates (day-of-week, exam period, semester break, subreddit composition) — Negative Binomial strongly preferred (ΔAIC = 550), confirming a weekend effect (Sat −18.0%, Sun −12.7% vs Monday, p<0.001) and an exam-period uplift (+7.2%, p<0.001).
- Compared **SARIMA(1,1,1)(1,1,1,7)** and **Prophet** via 4-window walk-forward cross-validation (21-day horizon, identical academic-calendar exogenous regressors): SARIMA won overall (mean MAE 21.61 vs Prophet 35.67), while Prophet became competitive once trained on a full year of seasonal history.
- Stack: Python, pandas, HuggingFace Transformers (RoBERTa), PyTorch, vaderSentiment, statsmodels, Prophet, scikit-learn. Code: [github.com/weyseing/sentiment_forecast](https://github.com/weyseing/sentiment_forecast).

---

## Publications and preprints

*None to date.* (PhD proposal targets first publications in time-series forecasting and applied counterfactual ML venues.)

---

## Professional experience

**Manager, AI Engineering** — *Fiuu (formerly Razer Merchant Services / MOLPay), Kuala Lumpur* &nbsp;&nbsp; *(Aug 2022 – Present)*
Lead the AI engineering function for one of Malaysia's largest payment infrastructure providers. Responsibilities relevant to research training:
- Architected large-scale data pipelines for corporate P&L, operational intelligence, and **strategic forecasting** — covariate-rich time-series at production scale.
- Designed agentic-AI frameworks combining LLM-driven reasoning with rule-based logic for autonomous diagnosis, predictive monitoring, and intelligent automation.
- Built knowledge-based AI platforms unifying enterprise data — relevant precedent for the data-integration work that any Malaysian public-sector decision-support prototype will require.

**Robotics (RPA) Engineer** — *Dell Technologies, Kuala Lumpur* &nbsp;&nbsp; *(Jan 2018 – Aug 2022)*
- Engineered enterprise-grade RPA solutions on UiPath, Blue Prism, and Automation Anywhere.
- Developed cognitive-automation bots integrating **OCR and NLP** for unstructured-data handling — early hands-on exposure to the same NLP techniques later applied in the MSc dissertation.
- Designed scalable robotic frameworks integrating APIs, databases, and legacy systems.
- Delivered orchestration, monitoring, and governance systems for production-grade bot fleets.

---

## Technical skills

- **Languages and runtime:** Python (primary), SQL, JavaScript/TypeScript, Node.js, Bash.
- **Machine learning / deep learning:** PyTorch, scikit-learn, HuggingFace Transformers (RoBERTa), statsmodels (Poisson / Negative Binomial GLMs), Prophet, SARIMA / SARIMAX.
- **NLP and text mining:** HuggingFace pipelines, RoBERTa-based sentiment classification, VADER, lexicon + transformer hybrid scoring, topic modelling.
- **Big data and streaming:** Apache Kafka, Apache Flink, AWS Redshift, lakehouse and data-warehouse architectures.
- **Cloud and AIOps:** AWS, GCP, Azure (multi-cloud experience).
- **Agentic AI and LLM tooling:** LangChain, LlamaIndex, LangGraph, custom agentic frameworks; memory and vector stores (mem0, Redis); prompt engineering and retrieval-augmented generation.
- **RPA platforms:** UiPath, Blue Prism, Automation Anywhere (A360 — certified).
- **Tools:** Git/GitHub, Docker, Linux, Jupyter, Pandas, Matplotlib.

---

## Certifications

- **Automation Anywhere Certified Advanced RPA Professional (A360)** &nbsp;&nbsp; *(Aug 2022)*
- **Certificate in Applied Data Science** — *Forward College, Malaysia* &nbsp;&nbsp; *(Apr 2022)*
- **Certified Dell RPA Developer** &nbsp;&nbsp; *(Oct 2019)*

---

## Languages

- **English** — Professional working (medium of instruction at MSc, BSc, Diploma)
- **Bahasa Malaysia** — Professional working
- **Mandarin Chinese** — Professional working

---

## References

**Prof. Dr Selina Low Yeh Ching** &nbsp;&nbsp; *(MSc supervisor)*
Associate Professor (Deputy Dean), School of Computing and Artificial Intelligence
Sunway University, Malaysia
Email: yehchingl@sunway.edu.my

*Additional references available on request.*
