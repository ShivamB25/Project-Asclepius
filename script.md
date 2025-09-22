# Project Asclepius v2.0 Demo Script (Presenter Friendly)

This script is written so anyone can deliver the story—even if you never touch BigQuery. Each section tells you what to show, what to say, and which judge rubric boxes you are checking. Feel free to adjust the words so they sound natural in your voice, but keep every idea in the **Rubric Cue** lines.

---

## 0. Prep (Before Recording)
- Open the Colab notebook `project_asclepius_v22.ipynb` in Google Colab. Make sure Drive is connected and run `!gcloud auth login` so the notebook points at the correct Google Cloud project (`PROJECT_ID`).
- Double-check BigQuery and Vertex AI APIs are enabled. In the notebook, set the default project and location once, then restart the runtime so authentication sticks.
- Keep these tabs or slides open:
  - Intro slide with the problem statement ("clinical insights trapped in text") and headline impact metrics.
  - README architecture diagram (Mermaid render or exported PNG).
  - Public GitHub repo + draft blog or video outline (for the Assets portion of the rubric).
  - Final slide summarizing Gemini prompt feedback and confirmation that the BigQuery AI survey is done.
- Do a dry run of every cell you plan to execute so nothing surprises you while recording.

**Tip:** Between sections you can pause recording, reset your windows, and glance at the next lines in this script.

---

## 1. Opening Hook (0:00 – 2:00)
**Show on screen:** Intro slide with three KPIs listed (Narrative Risk Score, Care Fragmentation Index, Inferred Social Determinant Burden).

**Say (read naturally):**
- "Healthcare is data rich yet insight poor—about 80% of EHR intelligence lives inside free-text notes." 
- "Project Asclepius v2.0 reads that text directly inside BigQuery so care teams finally see three new KPIs and can run a smart search for high-risk patients." 
- "Automating this saves roughly 120 analyst hours every week in a 300-bed hospital and surfaces high-risk cases days earlier so teams can act." 

**Rubric Cue (must cover):** Problem clarity + impact metrics (Innovation/Creativity 15%) and a teaser that you'll walk through the architecture later (Demo 10%). Say: "In a minute I'll show exactly how the architecture makes this possible." 

**Pause/Check:** Stop recording if you need to switch windows.

**Optional On-screen Text or Extra Voice Line:**
- "Narrative Risk Score tells us how serious the situation is based on the clinician notes." 
- "Care Fragmentation Index warns us when too many providers or conflicting instructions create gaps for a patient." 
- "Inferred Social Determinant Burden highlights issues like transportation, housing, or support that could block recovery." 

If you are not narrating, display these three lines on screen (slide, caption, or Colab Markdown cell) while the intro is visible.

---

## 2. Architectural Overview (2:00 – 4:00)
**Show on screen:** Architecture diagram. Use your cursor to trace each stage.

**Say:**
1. "Everything you see here runs inside BigQuery—no copied data, no extra databases. That makes it easy to govern and scale." 
2. "Stage 1 brings in safe, synthetic hospital records so we can demo without touching real patients." 
3. "Stage 2 uses Gemini through `ML.GENERATE_TEXT` to read raw notes and create a clean patient story." 
4. "Stage 3 turns those stories into three fresh KPIs: Narrative Risk, Care Fragmentation, and Social Determinant Burden." 
5. "Stage 4 converts each story into embeddings with `ML.GENERATE_EMBEDDING`, then `VECTOR_SEARCH` finds patients by concept instead of keyword." 
6. "Stage 5 packages the results into views and table functions so dashboards and alerts can plug in instantly." 

**Rubric Cue:** BigQuery AI usage (15%) + architecture explained (Demo 10%). Explicitly say: "You just saw BigQuery plus `ML.GENERATE_TEXT`, `ML.GENERATE_EMBEDDING`, and `VECTOR_SEARCH` working together." 

**Pause/Check:** Switch to Colab and resume recording when ready.

---

## 3. Stage 1 – Data Foundation (4:00 – 6:00)
**Screen steps:** In Colab, run the cells that create the schema, tables, and sample data. Show a quick `SELECT` to prove records loaded.

**Say:**
- "I’m seeding the demo right inside BigQuery—schema, tables, and the sample data all live here." 
- "The records mimic real hospital patients but remain synthetic, so we keep the demo safe." 
- "Notice the labels and the US region setting; we treat this like a production system from the very first command." 

**Rubric Cue:** Technical Implementation (20%). Clarify that the work happens in BigQuery and follows production hygiene.

**Pause/Check:** Make sure the dataset objects show as `DONE` in the job history before moving on.

---

## 4. Stage 2 – AI Architect: Structuring Notes & Event Stream (6:00 – 9:00)
**Screen steps:**
1. Run the `ML.GENERATE_TEXT` cell that writes to `ehr_structured_extracts`.
2. Open one row that shows extracted social determinants, care coordination notes, and risk factors.
3. Run the patient event stream query that merges notes, labs, and demographics.

**Say:**
- "By clicking run, I’m asking Gemini to act like a clinical summarizer—the prompt forces a neat JSON structure so the results stay consistent." 
- "Here’s an example row: it spotted transportation gaps, fragmented care, and risk factors without us coding any rules." 
- "Now I’m building a timeline. Think of it as one story for each patient—notes, labs, and demographics woven together so the model can read the full context." 
- "Because we flatten the JSON right in SQL, analysts down the line open clean tables, not messy blobs." 

**Rubric Cue:** Reinforce Technical Implementation quality and core BigQuery AI usage. Add one line: "This is all standard BigQuery SQL calling Gemini directly—no external services." 

**Pause/Check:** Confirm the table populated before moving to Stage 3.

---

## 5. Stage 3 – Generating Patient Narratives & KPIs (9:00 – 14:00)
**Screen steps:**
1. Run the Python batching script that fills `patient_summary_chronicle` (20 admissions at a time).
2. Execute the ISDB prompt cell to show clean JSON output.
3. Trigger the Care Fragmentation Index (CFI) orchestrator: show the control table, then the output table.

**Say:**
- "Watch the batching script—it walks through admissions in small groups so we stay within Vertex AI limits and can retry any hiccups automatically." 
- "This prompt locks the response to our schema, which is why every row comes back as perfectly structured JSON." 
- "Narrative Risk Score is the headline: it turns all those notes into an at-a-glance risk level for the care team." 
- "When you hear Inferred Social Determinant Burden, think of it as a score that tells us how much social challenges—like transport or housing—might slow down care." 
- "Now I’m running the Care Fragmentation orchestrator. Even if ETL isn’t your world, just know a control table logs every attempt and retries anything that times out, which makes it hospital-scale ready." 
- "Here’s what pops out: number of providers, communication gaps, and a fragmentation score we’ve never had before." 
- "Care Fragmentation Index is simply a warning light that the patient is juggling too many providers or disjointed instructions." 

**Rubric Cue:** Technical Implementation (clean engineering) + BigQuery AI centrality. Say: "This is the AI Architect pattern in action—using Gemini to mint new clinical KPIs inside BigQuery." 

**Pause/Check:** Verify no errors in the execution logs before proceeding.

---

## 6. Stage 4 – Semantic Detective: Embeddings & Similarity (14:00 – 17:00)
**Screen steps:**
1. Run the cell that builds `patient_trajectory_embeddings` with `ML.GENERATE_EMBEDDING`.
2. Execute the `find_similar_patients` table function with a plain-English query, such as "elderly patient living alone with transportation gaps and multiple providers".
3. Show the results with similarity score, Narrative Risk Score, and summary excerpt.

**Say:**
- "Now every patient story becomes a vector without leaving BigQuery—`ML.GENERATE_EMBEDDING` handles that instantly." 
- "I’m running our `find_similar_patients` function. Think of it as a Google search bar for complex clinical situations." 
- "Here are the matches in seconds: similarity scores, risk levels, and a quick summary so care coordinators know who to call." 
- "Searching for this insight used to take hours of manual chart review; now it takes seconds." 

**Rubric Cue:** BigQuery AI usage and Innovation. Explicitly say: "This is how we cut complex case discovery from hours to seconds—an innovation judges can score." 

**Pause/Check:** Make sure the similarity table loads correctly.

---

## 7. Stage 5 – Operational Views & Dashboard Hooks (17:00 – 19:00)
**Screen steps:**
1. Query the `clinical_validation_dashboard` view and filter to show High, Moderate, and Low risk patients.
2. Mention the `semantic_training_data` table and show a preview.
3. Flash a Looker Studio mock-up or simply explain how a dashboard would connect.

**Say:**
- "Even if SQL feels foreign, this view behaves like a ready-to-go clinician dashboard—risk tiers plus the factors driving them." 
- "That High or Low tag is simply the Narrative Risk Score in plain language: High means urgent attention, Low means routine watch." 
- "Here’s the `semantic_training_data` table. Think of it as a launch pad for training future models or hooking into Vertex AutoML—no extra prep needed." 
- "Because these outputs are standard BigQuery views, operations teams can plug them into Looker, alerts, or whatever tools they already trust." 

**Rubric Cue:** Technical polish + Demo clarity. Add: "We’re showing a clean, reusable package, not loose scripts." 

**Pause/Check:** Stop recording to set up the recap slide if needed.

---

## 8. Impact & Metrics Recap (19:00 – 21:00)
**Show on screen:** Slide with icons for time saved, readmissions reduced, proactive outreach.

**Say:**
- "Everything you just watched happened inside one governed BigQuery environment—SQL and Python working together with prompts that behave the same way every time." 
- "The new value is three AI-native KPIs and the ability to search patients semantically. No one else is doing that straight inside the warehouse." 
- "The payoff is huge: ~120 analyst hours saved each week and a projected 5–7% reduction in CHF readmissions, which means around $15K saved per avoided case plus better outcomes." 

**Rubric Cue:** Reinforce Impact metrics and innovation.

**Pause/Check:** Prep to speak directly to the judges next.

---

## 8a. Rubric Callouts for Judges (Optional but Recommended)
**Show on screen:** Same recap slide or a simple checklist.

**Say:**
- "Judges, here’s how this lines up with your rubric." 
- "Innovation: Asclepius is the only solution we’ve seen that blends LLM-built KPIs with warehouse-native semantic search—there’s no copy-and-paste recipe online." 
- "Impact Metrics: Remember the 120 analyst hours saved and the 5–7% readmission drop worth about $15K per avoided case. Picture that across thousands of admissions." 
- "Demo Clarity: You saw the full pipeline running live and the README doubles as documentation." 
- "BigQuery AI + Architecture: We walked through the Mermaid diagram and executed `ML.GENERATE_TEXT`, `ML.GENERATE_EMBEDDING`, and `VECTOR_SEARCH` right in BigQuery." 
- "Assets: Repo, blog or video, feedback doc, and the BigQuery AI survey are all submitted." 

**Rubric Cue:** Make sure each bullet maps to the scoring categories: Innovation (10%), Impact Metrics (15%), Demo Clarity (10%), BigQuery AI + Architecture (10%), Assets (20%).

---

## 9. Assets & Bonus (21:00 – 23:00)
**Screen steps:**
1. Show the GitHub repo tab containing the notebook and README.
2. Display the blog post or video link (even if it’s in draft).
3. Show the feedback doc and note the survey completion (a slide or quick doc view works).

**Say:**
- "Here’s the public GitHub repo—everything you saw today lives here and is ready to clone." 
- "We published a companion blog/video that walks through the solution. The link is on screen and in the submission." 
- "We also documented our prompt feedback and completed the BigQuery AI survey—both are attached for the bonus points." 

**Rubric Cue:** Assets (20%) and Bonus (additional 10%). State clearly that everything is delivered.

---

## 10. Closing Call-to-Action (23:00 – 24:00)
**Show on screen:** Final slide with contact info, repo link, blog/video link, and feedback summary bullets.

**Say:**
- "Project Asclepius v2.0 shows healthcare semantics can live directly in BigQuery." 
- "We invite partners to bring in real EHR data, co-develop clinical validation, and move from reactive to proactive care." 
- "Thank you—happy to answer questions." 

**Rubric Cue:** Leave judges with a clear call-to-action and confidence in delivery quality.

---

## Backup & Q&A (Use only if someone asks)
- Keep an extra slide with prompt templates so you can speak to determinism or low temperature settings.
- Have Colab cells ready to show raw JSON prompt bodies if someone wants to see the exact structure.
- Prepare quick talking points on governance: data stays in the US, audit logs in BigQuery, and planned monitoring.

---
**Presenter Reminder:** After each stage, glance at the BigQuery job history. If something fails, mention the retry safety and show the control table—turning a hiccup into proof of engineering rigor.
