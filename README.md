# From Stray to Stay: Leveraging Data to Improve Pet Adoption and Shelter Efficiency

**Austin Animal Center | SQL + BigQuery + Tableau | Team A02 — BA775, Boston University**

---

## Business Problem

The Austin Animal Center (AAC) is the largest no-kill municipal animal shelter in the United States, serving Austin, TX and surrounding Travis County. Operating under a no-kill mandate with constrained resources and rising intake volumes, shelter leadership faces a set of recurring operational questions that data can help answer:

- Are there seasonal or geographic patterns in intake that would support proactive capacity planning?
- Which animal characteristics are associated with longer stays or repeat shelter returns — and what interventions might reduce them?
- Has shelter performance improved over time, and where do bottlenecks remain?

This project analyzes five years of intake and outcome records (2020–2025) using Google BigQuery, Tableau, and Jupyter to produce actionable, data-driven answers to each of these questions.

---

## Dataset

**Source:** [Austin Animal Center Open Data Portal](https://data.austintexas.gov/)

| Table | Records | Description |
|---|---|---|
| Animal Intakes (2013–2025) | ~174,000 rows | Every intake event with species, breed, condition, location, and timestamp |
| Animal Outcomes (2013–2025) | ~174,000 rows | Every outcome event with type (adoption, transfer, euthanasia, etc.) and timestamp |
| Intake Found Location (derived) | ~60,000 rows | Structured geographic table parsed from unstructured address strings |

**Analysis window:** 2020–2025 (~60,000 intake events, ~60,000 corresponding outcomes)

**Data license:** Public Domain

**Match rate:** 98.4% of intake records have a corresponding outcome record

---

## Tools and Technologies

| Layer | Tool |
|---|---|
| Cloud platform | Google Cloud Platform (GCP) |
| Query engine | Google BigQuery |
| Languages / techniques | SQL — CTEs, window functions, multi-table joins, REGEXP, SAFE.PARSE_TIMESTAMP |
| Notebook environment | Jupyter Notebook / Google Colab |
| Visualization | Tableau Public (interactive dashboards) |
| Python libraries | pandas, matplotlib |

---

## Key Business Questions

1. Do intake volumes follow a seasonal pattern, and what does that imply for staffing and capacity?
2. Is the shelter keeping pace with demand year over year, or accumulating a backlog?
3. Which species, breeds, and animal characteristics drive the most intake volume?
4. Do animal characteristics — including color — influence adoption likelihood?
5. Why do animals enter the shelter, and does this differ by species?
6. How common is repeat shelter entry, and which species are most at risk?
7. What is the relationship between length of stay and outcome type?
8. Has the adoption rate improved over time?
9. Where are stray animals concentrated geographically, and what does that imply for community outreach?

---

## Methodology

### Data Preparation

Before any analysis, the raw dataset required significant cleaning. All transformations were implemented in BigQuery SQL and are fully documented in the notebook.

| Issue | SQL Transformation | Justification |
|---|---|---|
| Invalid timestamps (years recorded as `00xx`) | `REGEXP_REPLACE` + `SAFE.PARSE_TIMESTAMP` | Ensures accurate temporal analysis and length-of-stay calculations |
| Exact duplicate rows | `SELECT DISTINCT *` on cleaned tables | Removes erroneous duplication while preserving valid repeat-intake events |
| Negative age values (2 records) | `WHERE NOT (Age LIKE '-%')` | Eliminates biologically impossible values |
| Unstructured address strings in Found Location | `REGEXP_EXTRACT` to derive house number, street name, city, state | Converts messy text into structured geographic features for spatial analysis |
| Dataset spans 2013–2025; analysis needs a consistent window | `EXTRACT(YEAR FROM Intake_Time) BETWEEN 2020 AND 2025` | Focuses on recent, operationally relevant data; captures COVID disruption, recovery, and current steady-state |

The cleaned dataset achieved a 98.4% intake-to-outcome match rate, confirming high data integrity.

### Analysis Approach

The analysis is organized around four operational domains:

- **Temporal patterns** — seasonal intake peaks, annual volume trends, net flow balance
- **Animal population profile** — species composition, breed distribution, color and adoption likelihood
- **Intake and outcome behavior** — intake reasons by species, repeat entry rates, outcome type distribution, length of stay by outcome
- **Geographic patterns** — intake hotspot identification, city-level demand distribution

Each section connects findings to specific operational implications and recommendations.

---

## Key Findings

| # | Finding | Metric | Operational Implication |
|---|---|---|---|
| 1 | Intake peaks sharply in Spring and Summer | Spring: 16,772 intakes — 21% above Winter baseline | Peaks are predictable; seasonal capacity planning is feasible |
| 2 | Annual throughput is nearly balanced year over year | Net flow within ±200 events across all 5 years | No systemic backlog; shelter processes intake at pace |
| 3 | Dogs re-enter the shelter at 3× the rate of cats | Dogs: 9.8% repeat rate vs. cats: 3.2% | Post-adoption follow-up for dog adopters would directly reduce repeat entries |
| 4 | Adoption rate improved 13 percentage points over 4 years | 50% in 2020 → 63% in 2024 | Sustained improvement; adoption outreach and rescue partnerships are working |
| 5 | Stray hotspots cluster along arterial corridors, not neighborhoods | Levander Loop: 879 animals; IH-35 corridor among top intake streets | Targeted community outreach along high-volume roads is more efficient than city-wide campaigns |
| 6 | 80%+ of outcomes are positive (adoption + transfer combined) | Adoption: 57.65%, Transfer: 24.55% | Strong no-kill performance; rescue partnerships effectively extend physical capacity |
| 7 | Stray intake dominates across all species | Cats: 20,054 strays; Dogs: 18,512 strays | Community-level interventions (microchipping, TNR) address root causes more effectively than shelter-side adjustments |
| 8 | Length of stay varies substantially by outcome type | Transfer: 20.9 days avg; Adoption: 51.6 days avg; Rto-Adopt: 58.6 days avg | Long-stay outcome categories benefit most from proactive monitoring |

---

## Recommendations

**1. Implement seasonal capacity planning**
Spring and Summer intake peaks are consistent across all five years. The shelter should develop a staffing model that increases volunteer onboarding, foster recruitment, and medical triage capacity beginning in February–March, ahead of peak season.

**2. Build a dog-specific post-adoption support program**
Dogs re-enter the shelter at 9.8% — three times the rate of cats. A structured 30-day check-in program, behavioral resource referrals, and owner retention support for dog adopters would directly target this gap.

**3. Target community outreach along high-intake corridors**
Stray hotspots cluster along major arterial roads rather than residential neighborhoods. Microchipping events, shelter signage, and community partnerships concentrated along IH-35, Research Blvd, and Frontier Trail would address the highest-volume intake sources.

**4. Optimize adoption listings for visual appeal**
Animal color is associated with variation in adoption likelihood. Prioritizing higher-quality photography and featured placement for solid or darker-coated animals could partially offset adoption bias toward patterned or lighter-coated animals.

**5. Monitor and flag long-stay animals proactively**
Rto-Adopt cases (58.6-day average) and animals in unclear status categories accumulate disproportionate housing days. A length-of-stay monitoring dashboard flagging animals beyond 45 days would enable proactive intervention before long stays become entrenched.

---

## Tableau Dashboards

Interactive dashboards are published on Tableau Public:

- **Dashboard 1 — Shelter Performance Overview:** Intake/outcome trends by species, intake composition by type, outcome composition by type, and key KPIs (92% live release rate, 62.53% adoption rate)
  → [View on Tableau Public](https://public.tableau.com/app/profile/yuyang.zhai6198/viz/TeamA02/dataoverview)

- **Dashboard 2 — Intake–Outcome Behavior Analytics:** Geospatial distribution of intake locations, repeat intake percentage by species, and average length of stay by species
  → [View on Tableau Public](https://public.tableau.com/app/profile/yuyang.zhai6198/viz/TeamA02-dashboard2/IntakeOutcomeBehaviorAnalytics)

Screenshots of both dashboards are included in the `dashboard_screenshots/` folder.

---

## Repository Structure

```
BA775-Animal-Shelter-Data-Analysis/
├── README.md                          # Project documentation
├── animal_shelter_operations_analysis.ipynb   # Full SQL notebook (BigQuery)
├── A02_Presentation.pdf               # Final project slide deck
├── dashboard_screenshots/             # Tableau dashboard exports
│   ├── dashboard_1_overview.png
│   └── dashboard_2_behavior.png
└── extension/                         # Post-course independent work (labeled separately)
    ├── README_extension.md
    └── extension_analysis.ipynb
```

---

## How to View and Run

### Viewing the Analysis (No Setup Required)

The notebook PDF export is included in this repository for viewing without a BigQuery connection.

Both Tableau dashboards are publicly available at the links above.

### Running the Notebook

1. Open `animal_shelter_operations_analysis.ipynb` in **JupyterLab** or **Google Colab**
2. Ensure access to **Google BigQuery** with an active GCP project
3. Update the project and dataset identifiers in the notebook setup cell:
   ```python
   project_id = "YOUR_PROJECT_ID"
   dataset_id = "animal_shelter"
   ```
4. Run all cells in order

The notebook is fully SQL-based via BigQuery Magic (`%%bigquery`). No local data files are required — all queries run against the Austin Animal Center public dataset loaded into BigQuery.

**Required Python packages** (if running locally rather than in Colab):
```
google-cloud-bigquery
pandas
matplotlib
```

---

## Limitations

1. **Single-shelter scope:** All data originates from Austin Animal Center. Findings reflect Austin's specific demographic, policy, and infrastructure context and may not generalize to shelters in different cities or operating under different mandates.

2. **Partial 2025 data:** The dataset covers only through May 2025. Annual comparisons for 2025 are excluded from trend analysis to avoid misleading year-over-year conclusions.

3. **Geographic data precision:** The Found Location field was recorded inconsistently in the source data. Parsed street-level addresses are best-effort regex extractions; some records could not be geocoded and are excluded from geographic analysis.

4. **No ZIP code or neighborhood-level enrichment:** Attempts to join census neighborhood and ZIP code data were limited by licensing restrictions on the required reference datasets, preventing deeper demographic analysis of intake geography.

5. **Event-level data only:** The shelter tracks intake and outcome events, not full animal life trajectories. Behavioral history, medical outcomes, and post-adoption follow-up data are not captured, limiting causal inference about what drives repeat intakes or adoption success.

6. **Color adoption analysis note:** The adoption likelihood metric in the color analysis reflects animals with multiple intake events across the study period. Values above 1.0 should be interpreted as relative rankings rather than absolute rates.

---

## Contributors

**Team A02 — BA775 Business Analytics Toolbox, Boston University Questrom School of Business**

Erin Zhai (Project Manager) · Bill Odiase · Angela Liou · Shon Shaju · Allen Chiu

Instructed by Professor Mohammad Soltanieh-ha

---

## Generative AI Disclosure

Generative AI tools (including ChatGPT) were used to support non-analytical tasks — refining markdown text, improving clarity in written explanations, and assisting with formatting. AI was also used to help debug minor SQL syntax issues and suggest clearer narrative structure for summaries. All analysis, SQL queries, interpretations, and final decisions were independently reviewed and validated by the team.

---

## License

[MIT License](LICENSE)
