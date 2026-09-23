# Vedara — AI-Powered Telugu Astrology Application

**Team:** NLP Team 14

---

## 1. Problem Statement

Telugu-speaking users seeking astrology guidance today are largely served by applications that are either purely computational (producing raw planetary/chart data with no conversational or interpretive layer) or purely static content apps (fixed horoscope text with no personalization or natural-language interaction). There is no production-grade, cross-platform mobile application that combines:

- **Accurate, deterministic astrological calculations** (birth chart/Janma Kundali, planetary positions, Nakshatra, Rasi, Dasha, houses and aspects) derived from a real astronomical ephemeris rather than approximated or invented data, and
- **A natural-language AI conversational interface** that *understands* input in **Telugu, English, Tanglish, or mixed Telugu-English**, but always *responds* in a single, clean language — pure Telugu or pure English, never Tanglish or code-mixed — allowing users to ask questions the way they naturally speak while still receiving a linguistically clean, professional response.

This creates a gap for Telugu-speaking users who want to explore astrology conversationally — e.g. *"నా రాశి ప్రకారం ఈ నెల ఎలా ఉంటుంది?"* or *"In my chart, Saturn is in the 10th house. What does it mean?"* — and receive a response that is linguistically natural, contextually aware (remembering prior turns, e.g. a follow-up "What about in my chart?" after asking about Saturn), and astrologically grounded in real computed data rather than hallucinated output.

A further gap is **safety**: existing astrology apps do not reliably detect when a query — however it is phrased, including indirectly, emotionally, sarcastically, or in Telugu — it touches a high-risk domain (medical diagnosis, mental health crisis/self-harm, high-stakes financial decisions, legal disputes, or relationship abuse/domestic violence) and instead let astrology interpretation stand in for professional guidance in these situations.

**Objective:** Design and build a cross-platform (Android/iOS) AI astrology application for Telugu-speaking users that:

1. Computes accurate birth charts and planetary data using a proper ephemeris/astrology calculation library — never AI-invented positions.
2. Provides a conversational chatbot that understands intent regardless of script or language mixing (Telugu / English), but always replies in pure Telugu or pure English only — never Tanglish or mixed output — and maintains conversational context.
3. Separates deterministic astrology calculation from LLM interpretation, so the AI explains and interprets computed data rather than generating it from scratch.
4. Enforces safety guardrails that detect high-risk queries (medical, mental-health crisis, financial, legal, abuse-related) across all supported languages and override astrology interpretation with appropriate, safe, non-astrological guidance.
5. Presents all astrological content as interpretive/traditional belief rather than scientific or guaranteed fact.
6. Delivers this through a secure, scalable, production-quality mobile application respecting user privacy, particularly around sensitive birth data.

---

## 2. Datasets / Data Sources

Since astrology depends on **real-time planetary positions** calculated for a user's exact birth time and location, the project does not use a traditional labeled "dataset" for chart generation — it uses a **mathematical ephemeris**, i.e. an orbital-mechanics-based data source that predicts the exact position and velocity of celestial bodies at any given time, in place of a static table.

### 2.1 Ephemeris Source

- **NASA JPL Ephemerides** — a large mathematical/orbital-mechanics dataset that tracks precise planetary positions based on physical models. Rather than parsing these raw ephemeris files directly, the project uses open-source libraries built on top of this kind of data to compute the required astrological values.

### 2.2 Calculation Libraries (in place of raw ephemeris processing)

| Library | Purpose | Source |
|---|---|---|
| **jyotishganit** | Computes Tithi, Vaara, Nakshatra, Raasi and related Vedic astrology values | [github.com/northtara/jyotishganit](https://github.com/northtara/jyotishganit) |
| **pyswisseph** | Python bindings for the Swiss Ephemeris — precise planetary position/velocity calculations | [github.com/astrorigin/pyswisseph](https://github.com/astrorigin/pyswisseph) |

These libraries handle the underlying astronomical computation so the application does not need to process NASA JPL's raw ephemeris files directly.

### 2.3 Telugu Mapping Table

A **custom-built Telugu mapping table** (translation dictionary) is required to map the numerical/English outputs from jyotishganit and pyswisseph (e.g. planet IDs, Nakshatra indices, Rasi indices) to their standard **Telugu and English equivalents** for display and NLP purposes. This mapping table is a project deliverable rather than an externally sourced dataset.

### 2.4 Prior Research

A comprehensive literature search did not surface existing research papers or publications directly addressing this problem space (a multilingual, Telugu-focused, safety-guarded AI astrology conversational system), indicating this project addresses a novel combination of requirements rather than replicating prior published work.

### 2.5 Existing Applications Reviewed (Competitive Landscape)

The following existing Telugu/Vedic astrology applications were reviewed as part of the problem-space analysis:

- Phalam AI
- Hindu Jyotish – Astrology
- Telugu Jathakam: Horoscope App
- Jathakam Telugu Astrology (Android and iOS)
- Web App (Telugu Jathakam-related)

These serve as reference points for existing feature sets and UX patterns, but none combine accurate ephemeris-based calculation with a natural multilingual conversational AI layer and cross-language safety guardrails, which is the gap Vedara aims to fill.

---

## 3. Ethical Considerations & Restricted Query Categories

Astrology is presented to users as **interpretive/traditional belief content**, never as scientifically validated fact, and the chatbot is explicitly restricted from acting as a substitute for licensed professionals. The following query categories must **never** receive an astrology-based answer, prediction, or recommendation — this applies regardless of whether the query is phrased directly, indirectly, emotionally, sarcastically, in Telugu, in Tanglish, or in mixed language:

| Category | The chatbot must NOT | Instead, it must |
|---|---|---|
| **Medical / physical health** | Diagnose disease, interpret symptoms, predict illness, advise stopping/changing medication, or replace medical consultation | State that astrology cannot diagnose or guide treatment, and direct the user to a licensed physician (or emergency care if urgent) |
| **Mental health crisis / self-harm** | Explain suicidal thoughts, self-harm, or crisis states as planetary/karmic causes; say things like "Saturn is causing this" | Suppress astrology entirely, respond empathetically, and direct the user to emergency services, a crisis helpline, or a licensed mental-health professional |
| **High-stakes financial decisions** | Recommend investments, crypto, trading, loans, mortgages, or business decisions based on chart placements | Explain that astrology is not a financial forecasting tool and direct the user to a qualified financial adviser (e.g. CFP/fiduciary) |
| **Legal disputes / criminal accusations** | Predict court outcomes, advise plea/settlement decisions, or infer guilt/criminality from a birth chart | Decline the astrological/legal prediction and direct the user to a qualified attorney or legal-aid service |
| **Relationship abuse, coercion, domestic violence** | Excuse, normalize, or explain abusive/controlling behavior as a zodiac trait; tell a victim to stay or to confront an abuser | Treat the behavior as the real issue (not astrology), avoid suggesting confrontation, and direct the user to a trusted person, counselor, domestic-violence support service, or emergency services |

**Priority order when a query spans multiple categories:** immediate safety/crisis → medical safety → abuse/personal safety → legal safety → financial safety → normal astrology assistance. Safety always overrides astrology interpretation, and this detection must work identically across English, Telugu, and Tanglish phrasing.

**Additional ethical commitments:**
- No absolute guarantees are ever made (e.g. "you will definitely...", "you will certainly..."); all astrology output is framed as interpretive, not certain.
- The AI never invents planetary positions, chart values, or calculations — all astrological data must come from the ephemeris-based libraries (Section 2), with AI-generated interpretation clearly separated from calculated data.
- Sensitive personal data (birth date/time/place, name, contact info, conversation history) is treated as private information, encrypted in transit and at rest, never exposed across users, and not retained beyond what is necessary.

---

## 4. Notes

- All astrological content in the application is presented as interpretive/traditional insight (e.g. "In Vedic astrology...", "Traditionally interpreted as...") and never as scientific fact, medical diagnosis, financial advice, legal advice, or guaranteed prediction.
- No planetary positions, chart values, or astrological data are ever invented by the AI layer — all such data originates from the ephemeris-based calculation libraries listed above.
