# Personalised Learning Recommendation System

> A hybrid recommendation engine that combines collaborative filtering and NLP to suggest tailored courses and study materials to students — designed with fairness and privacy as first-class constraints, not afterthoughts.

---

## Motivation

Most digital learning platforms treat all students the same. A student struggling with linear algebra and a student who aced it get the same "recommended for you" list. The result is disengagement — materials that are too easy feel like a waste of time, and materials that are too hard feel like a wall.

Personalisation is the obvious fix, but it comes with real risks: recommendation systems can encode and amplify existing inequalities, nudging students from under-represented groups toward less ambitious paths based on historical patterns rather than individual potential. This project took both problems seriously — building a system that adapts to each student while actively checking that its recommendations are fair across demographic groups.

---

## What It Does

- Recommends courses and study materials tailored to each student based on their academic performance, stated interests, and engagement history
- Analyses student feedback on previous recommendations to improve future suggestions over time
- Surfaces explanations for each recommendation ("suggested because you performed well in Statistics and showed interest in Data Analysis")
- Flags and corrects for demographic disparity in recommendations before they are shown to students
- Exposes recommendations via a lightweight REST API consumed by a web front-end

---

## Architecture

```
Student Profile
(performance history, interests, feedback)
        │
        ▼
┌───────────────────────────────────┐
│         Hybrid Recommender        │
│                                   │
│  ┌─────────────────────────────┐  │
│  │   Collaborative Filtering   │  │  ← "Students like you also liked..."
│  │   (TF Recommenders)         │  │
│  └─────────────┬───────────────┘  │
│                │                  │
│  ┌─────────────▼───────────────┐  │
│  │   Content-Based NLP Filter  │  │  ← "This matches your interests because..."
│  │   (SpaCy semantic matching) │  │
│  └─────────────┬───────────────┘  │
└────────────────┼──────────────────┘
                 │
                 ▼
        Fairness Audit Layer
        (demographic parity check)
                 │
                 ▼
        Ranked Recommendations
        + Explanations
                 │
                 ▼
        Flask API → Web UI
```

---

## Tech Stack

| Component | Technology |
|---|---|
| Collaborative Filtering | TensorFlow Recommenders |
| NLP / Semantic Matching | SpaCy |
| Backend API | Flask (Python) |
| Data Processing | Pandas, NumPy |
| Frontend | HTML / CSS / JavaScript |
| Storage | SQLite |

---

## How the Recommendation Works

### Step 1 — Collaborative Filtering
The model identifies students with similar performance profiles and interest patterns, then recommends materials that similar students found valuable. This captures the "wisdom of the crowd" signal — what works for students like you.

### Step 2 — Content-Based NLP Matching
SpaCy analyses the semantic content of course descriptions and compares them against the student's stated interests and past engagement. This handles the cold-start problem: new students without a history can still get relevant recommendations based on what they say they care about.

### Step 3 — Feedback Loop
After a student completes or rates a resource, that signal is fed back into the model. Positive feedback reinforces similar suggestions; negative feedback diversifies the recommendation pool. Over time, the system gets more accurate for each individual.

### Step 4 — Fairness Audit
Before recommendations are served, the system checks for demographic parity — ensuring students from different groups (gender, socioeconomic background, prior educational exposure) are not systematically routed toward lower-ambition materials. If disparity is detected above a threshold, the recommendation set is rebalanced.

---

## Fairness & Privacy Design

### Fairness
Recommendation systems trained on historical data can encode historical bias. A model trained on data where students from certain groups historically enrolled in fewer advanced courses might "helpfully" continue that pattern — not because those students lack capability, but because the training data reflects systemic inequality, not individual potential.

To address this:
- Demographic parity is checked across protected attributes before recommendations are surfaced
- The system is evaluated not just on accuracy but on **equal opportunity** — whether the highest-quality recommendations are equally accessible across groups
- Fairness metrics are logged alongside accuracy metrics in every evaluation run

### Privacy
- No personally identifiable information is stored beyond what is necessary for the recommendation function
- Student identifiers are hashed; the model never sees names or contact details
- All data remains within the institution's environment — no third-party API calls with student data
- Students can view and delete their stored preference data at any time

---

## Evaluation

The system was evaluated using a simulated A/B test comparing personalised recommendations against a baseline content-agnostic system (showing the same top-rated courses to everyone):

| Metric | Baseline | Personalised |
|---|---|---|
| Click-through rate | [X]% | [X]% |
| Course completion rate | [X]% | [X]% |
| Student satisfaction score | [X]/5 | [X]/5 |
| Demographic parity gap | [X]% | [X]% |

*Fill in your actual or estimated figures.*

---

## Limitations & What I'd Do Differently

- **Collaborative filtering struggles with sparse data.** Early in a student's profile, there isn't enough history for strong collaborative signals. The NLP content-matching helps, but a production system would benefit from a more sophisticated cold-start strategy.
- **Fairness metrics are a floor, not a ceiling.** Demographic parity is one fairness definition — but there are others (equalised odds, individual fairness) that sometimes conflict. A real deployment would require working with educators and ethicists to decide which definition matters most for this context.
- **The feedback loop can create filter bubbles.** Recommending more of what a student already engages with risks narrowing their exposure. I'd add a deliberate "exploration" component — occasionally surfacing something outside their comfort zone.
- **No real student data was used.** The system was built and evaluated on simulated data. Real-world performance would look different and require careful monitoring after deployment.
- **SpaCy's semantic matching is English-only.** A global platform would need multilingual NLP support.

---

## Running Locally

```bash
# Clone the repo
git clone https://github.com/sreenidhi-batchu/learning-recommender
cd learning-recommender

# Install dependencies
pip install -r requirements.txt
python -m spacy download en_core_web_md

# Run the Flask app
python app.py

# Open in browser
http://localhost:5000
```

---

## Project Context

Built as a group project during my M.S. in Computer Science (2024). My primary contributions were the **fairness audit layer**, the **feedback loop integration**, and the **Flask API**. The project deepened my thinking about what it means to build AI responsibly for populations that don't always have power to push back against a system that isn't working for them.

---

## Further Reading

- [TensorFlow Recommenders Documentation](https://www.tensorflow.org/recommenders)
- [SpaCy Documentation](https://spacy.io/usage)
- [Fairness and Machine Learning — Barocas, Hardt, Narayanan](https://fairmlbook.org)
- [UNESCO Recommendation on the Ethics of AI in Education](https://www.unesco.org/en/artificial-intelligence/recommendation-ethics)
