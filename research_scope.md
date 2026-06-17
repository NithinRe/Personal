# Florida K-12 Market Analysis — Research Scope

*Draft for review. All three problem statements, plus the strategic research question that connects them, are captured below.*

## Context

**National backdrop.** The 2025 American Society of Civil Engineers (ASCE) report gave U.S. public school infrastructure a **D+** grade, unchanged from 2021. There is an estimated **$270B in deferred maintenance**, and the average public school is **49 years old**. Roughly 30% of schools have failing HVAC systems, 25% have failing roofs, and 32% have poor-condition windows; more than 40% of districts require significant HVAC work in at least half of their schools. The recommended annual maintenance standard is about 4% of operating funds, but many districts fund only about 2%, deferring problems into more expensive emergency repairs. National K-12 enrollment is projected to decline from 49.4 million students in 2021-22 to roughly 46.9 million by 2031-32, forcing districts to modernize aging buildings while planning for fewer students.

**The market.** The 2026 total U.S. K-12 ESCO opportunity is approximately **$4.22B per year**, with the top ten states representing more than half of it. **Florida ranks third at approximately $245M per year**, behind California (~$499M) and Texas (~$471M).

**Key terms.**
- **ESCO** — Energy Service Company; a vendor that designs, installs, and finances building energy upgrades.
- **ESPC** — Energy Savings Performance Contract; the ESCO guarantees a level of energy and operating savings, and the upgrades are paid for out of those savings. The model is budget-neutral, and if the savings fall short the ESCO covers the difference.
- **FEFP** — Florida Education Finance Program; the state's operating-funding formula, driven largely by student enrollment.
- **PECO** — Public Education Capital Outlay; Florida's restricted capital (construction) funding stream.
- **F.S. 1013.23** — the Florida statute governing guaranteed energy performance contracting for school districts.

---

## Problem Statement #1 — The Market Opportunity

Florida K-12 is one of the largest ESCO/ESPC markets in the United States, estimated at approximately $245M per year (about $1.2B over five years), yet only an estimated **10–15% has been realized to date**. The remaining share is described as untapped due to structural constraints rather than a lack of need. The objective is to validate the size and current penetration of the market and to identify what drives — or blocks — adoption across Florida's 67 county school districts.

### Hypotheses

1. **The opportunity is large and largely unrealized.** Florida K-12 represents a substantial addressable ESCO market, of which only a small share (an estimated 10–15%) has been implemented to date. Confirming this requires estimating the addressable market from building footprint, age, condition, and energy spend, and counting realized ESPC projects (by count and dollar value) to express a penetration rate.

2. **The opportunity is concentrated in a few large districts.** A small number of county districts hold the majority of students — and therefore the majority of the building and energy footprint — making the market reachable through a limited set of large engagements. Confirming this requires ranking all 67 districts by enrollment and by building square footage and measuring the share held by the largest districts.

3. **Low adoption is driven by structural and funding factors, not by a lack of need.** Districts that have not adopted ESPC still exhibit substantial need — aging buildings, high energy use, and deferred maintenance — and whether a district has adopted is better explained by structural factors such as district size, taxable wealth, capital-funding access, and capital referendum history. Confirming this requires comparing need indicators between adopter and non-adopter districts, then relating adoption to those structural factors.

### Notes for review

- **Target levels (Hypotheses 1 and 2).** These hypotheses reference quantitative targets — how much of the market is untapped and how concentrated it is. Reference figures are available from the brief (approximately 85% of the market untapped; the largest 28 of 67 districts holding roughly 90% of students). The specific thresholds that define "largely unrealized" and "concentrated" for confirming each hypothesis are to be set during review.

- **Method (Hypothesis 3).** Whether adoption is related to structural factors through a descriptive comparison of adopter and non-adopter districts, a regression model, or a machine-learning model will be decided at the build stage, based on data availability and the limited number of districts (67, of which only a small subset are adopters).

- **Data gaps.** Where complete data is unavailable — most notably a comprehensive history of completed ESPC projects, for which no central public registry exists — figures will be reported as conservative lower bounds rather than omitted.

### Candidate datasets

| # | Dataset | What it provides | Candidate source |
|---|---------|------------------|------------------|
| 1 | Enrollment by district (FTE) | Student counts per district; market concentration | Florida DOE; NCES ELSI |
| 2 | School facilities inventory | Building square footage, age, and condition | FISH (Florida Inventory of School Houses), Florida DOE |
| 3 | District finances | Operating, capital, and energy/O&M expenditures | NCES F-33; Florida DOE financial reports |
| 4 | ESPC project history by district | Which districts have adopted ESPC, with year and dollar value | Public records (Florida DOE; district board minutes and procurement filings) |
| 5 | District wealth and referendum history | Taxable property base; capital sales-tax (surtax) referenda | County property/tax records; Florida DOE |
| 6 | Utility rates | Electricity price levels and trends | U.S. Energy Information Administration (EIA) |
| 7 | Cooling degree days | Regional variation in energy demand | NOAA |
| 8 | Facilities condition index | A quantified measure of maintenance need | FISH; Florida DOE |
| 9 | Charter-school enrollment share | ESPC eligibility and local competition | Florida DOE; NCES |
| 10 | District bond ratings | Indicator of capital-funding access | Public credit ratings (S&P, Moody's, Fitch) |

*Availability, granularity, and exact sources for each dataset are to be confirmed during data acquisition.*

---

## Problem Statement #2 — The Operating Funding Squeeze

Florida school districts face a structural operating-funding problem. Energy, operations, and maintenance costs have risen sharply — an estimated $857M (24%) over three fiscal years — while public-school enrollment has begun to decline for the first time, driven by transfers to charter, private, and homeschool options rather than by a falling school-age population. Because operating revenue is tied to enrollment through the FEFP formula while these costs are tied to building characteristics, the cost of running schools is becoming disconnected from the funding available to run them. The objective is to measure the cost trend, the enrollment trend and its causes, and the resulting gap between operating revenue and operating cost.

### Hypotheses

1. **Operating revenue declines with enrollment, and the enrollment decline is associated with school choice rather than a falling school-age population.** Public-school enrollment is declining in a pattern consistent with transfers to charter, private, and homeschool options rather than a decline in the school-age population; because operating funding follows enrollment, operating revenue falls accordingly. Confirming this requires showing that the school-age population (births carried forward through grade cohorts) is stable or rising while public enrollment falls, and that the steepest public-enrollment declines occur in the areas with the fastest charter, private, homeschool, and scholarship growth.

2. **Operating costs are rising and are driven by building characteristics, not student counts.** Energy and operations/maintenance costs have risen sharply in recent years and track building square footage, age, and systems rather than enrollment, so cost per student rises as enrollment falls. Confirming this requires verifying the cost increase from district financial data, showing that costs are associated with building characteristics rather than enrollment, and computing the cost-per-student trend.

3. **The gap between operating revenue and operating cost is widening toward a structural breakdown.** Operating revenue (enrollment-driven) and operating cost (building-driven) are diverging, and the gap is projected to continue growing. Confirming this requires placing both trends on a common basis per district, measuring the gap over time, and projecting it forward.

### Notes for review

- **Target levels.** The cost-increase figure (approximately $857M, or 24%, over three fiscal years) is drawn from the brief and is to be verified against district financial data. The threshold that defines a "structural breakdown" is to be set during review; a workable definition is the point at which a district's discretionary operating surplus (total operating revenue minus fixed costs) approaches zero.

- **Causation and confounders (Hypothesis 1).** The co-occurrence of public-enrollment decline and charter, private, or homeschool growth demonstrates association, not causation. The analysis will distinguish school choice from alternative explanations — a smaller school-age population aging through the grades, net migration, and pandemic-era cohort effects — using birth-to-kindergarten conversion trends and grade-cohort progression.

- **Cost decomposition (Hypothesis 2).** Rising costs reflect several forces at once: energy prices, building age, and declining building utilization. All cost and revenue figures will be inflation-adjusted, and the energy-price component will be separated from building and utilization effects using energy consumption (not expenditure alone). Cost per student will be expressed as cost per square foot multiplied by square feet per student, to isolate the effect of fewer students occupying fixed building space.

- **Revenue scope and projection (Hypothesis 3).** Operating revenue will be measured as total operating revenue — state (FEFP), local property tax, and federal funds — not FEFP alone, since districts with high property-tax bases may not face the same squeeze. The forward projection of the revenue-cost gap will be presented as a range of scenarios rather than a single estimate.

- **Data gaps.** Private-school and homeschool enrollment data are less complete than public-school and charter data; charter enrollment and birth-cohort analysis will serve as the primary evidence, with private and homeschool figures as corroborating. Where complete data is unavailable, figures will be reported as conservative estimates or bounds.

### Candidate datasets

| # | Dataset | What it provides | Candidate source |
|---|---------|------------------|------------------|
| 1 | Public enrollment over time (FTE) | The declining revenue driver | Florida DOE; NCES ELSI |
| 2 | Births by county | Evidence the school-age population is not falling | CDC WONDER; Florida DOH |
| 3 | Charter, private, and homeschool enrollment | The substitution check behind the enrollment decline | Florida DOE; NCES |
| 4 | District energy and O&M expenditures over time | The rising cost trend; verification of the reported increase | Florida DOE financial reports; NCES F-33 |
| 5 | FEFP revenue per district over time | The operating-revenue trend | Florida DOE |
| 6 | School facilities inventory (sqft, age) | Evidence that cost tracks buildings rather than students | FISH (Florida Inventory of School Houses) |
| 7 | Utility rates | Context for why energy costs have risen | U.S. Energy Information Administration (EIA) |
| 8 | Price deflator / inflation index | Comparison of costs and revenue in real (constant-dollar) terms | BLS CPI; BEA state and local government deflator |
| 9 | Total operating revenue (state, local, federal) | The full operating-revenue picture, not state funding alone | Florida DOE financial reports; NCES F-33 |
| 10 | Energy consumption (kWh) | Separation of energy usage from energy price | FISH energy data; public benchmarking databases (e.g., DOE Building Performance Database) |
| 11 | Grade-level enrollment by cohort | Distinguishes entry-point decline from cohorts aging out | Florida DOE |
| 12 | School-choice scholarship take-up | A direct signal of school-choice substitution | Florida DOE; scholarship administrators (e.g., Step Up For Students) |

*Availability, granularity, and exact sources for each dataset are to be confirmed during data acquisition.*

---

## Strategic Research Question — Do Districts That Invest Perform Better?

This question is drawn from the strategy section of the brief, which poses it directly as a hypothesis: that investment in facilities and equipment is not, on its own, producing results. It builds on the findings of Problem Statements #1 and #2 and informs the recommendations in Problem Statement #3. The question is whether districts that have invested in facilities and equipment achieve better cost and student outcomes than comparable districts that have not.

### Hypotheses

1. **Major facility and equipment investment is not associated with large, consistent improvements in operating and energy costs.** Districts that have made major, documented investments — identified through passed capital sales-tax referendums, sustained above-average capital, HVAC, and technology spending, or ESPC projects — do not show operating- and energy-cost trajectories that are clearly better than those of comparable non-investor districts within a few years of the investment being completed.

2. **Major facility and equipment investment is not associated with large, consistent improvements in student outcomes.** The same investor districts do not show clearly better school grades, test scores, graduation and attendance rates, or teacher retention than comparable non-investor districts, after accounting for differences that were present before the investment.

Confirming either hypothesis requires matching each investor district to comparable non-investor districts (similar in size, wealth, region, and pre-investment condition) and comparing their before-and-after trajectories. Both hypotheses are two-sided: a clear improvement would support the case for investment, while its absence would support an approach focused on outcomes rather than on equipment alone.

### Notes for review

- **Defining an investor.** "Investment" can mean a passed referendum, a sustained increase in capital/HVAC/technology spending, or an ESPC project — and these differ in size and timing. Districts will be grouped into tiers (for example, major / moderate / non-investor) rather than a single yes/no split, and investment will be dated from project completion rather than from referendum passage.

- **Fair comparison and time lag.** Findings are associational, not causal. Each investor district will be matched to comparable non-investor districts (similar size, wealth, region, and pre-investment condition) and compared before versus after the investment. Cost effects tend to appear within a few years; student-outcome effects take longer and are weaker, so the two are analyzed over different time windows.

- **Small sample.** Only a minority of the 67 districts are investors, so the analysis can reliably detect only sizeable differences. Results will be reported with that limitation stated, and framed as "no large, consistent effect" rather than as proof of no effect.

- **Comparability of measures.** Florida's school-grade formula has changed over time, so grades will be normalized before comparison; pandemic years (2020–2022) will be flagged or excluded because spending, testing, and energy use were all disrupted.

### Candidate datasets

| # | Dataset | What it provides | Candidate source |
|---|---------|------------------|------------------|
| 1 | Capital sales-tax referendum history | Identification of investor districts | County elections records; Florida DOE |
| 2 | Capital, HVAC, and technology spending | Magnitude and type of investment | Florida DOE financial reports; NCES F-33 |
| 3 | ESPC project history by district | Investment made through performance contracts | Public records (Florida DOE; district filings) |
| 4 | Operating and energy cost trends | The cost-outcome measure | Florida DOE financial reports; NCES F-33 |
| 5 | School grades and test scores | The student-achievement outcome measure | Florida DOE accountability data |
| 6 | Graduation and attendance rates | Additional achievement outcome measures | Florida DOE |
| 7 | Teacher retention / turnover | Additional achievement outcome measure | Florida DOE staff data |
| 8 | District demographics (size, poverty) | Controls for comparable district matching | Florida DOE; NCES |
| 9 | School facilities inventory (building age, condition) | Baseline building condition for matching | FISH (Florida Inventory of School Houses) |
| 10 | Project completion / commissioning dates | Correct dating of when an investment took effect | Florida DOE; district records |
| 11 | Charter and private enrollment share | Confounder affecting both costs and outcomes | Florida DOE; NCES |
| 12 | Per-pupil local property wealth | Wealth control for matching | Florida DOE |

*Availability, granularity, and exact sources for each dataset are to be confirmed during data acquisition.*

---

## Problem Statement #3 — Market Enablement: Policy and Funding Reform

Problem Statement #3 sets out the proposed solution: a set of policy and funding reforms intended to enable broader facilities investment across Florida K-12. Unlike Problem Statements #1 and #2, it does not contain a hypothesis to test with data — it is a set of recommendations that the findings from the preceding analysis would support and prioritize. The proposed reforms fall into three groups:

- **Funding reforms** — adjusting the FEFP formula to account for facility condition; revising restrictions on the use of FEFP and PECO funds; establishing matching grants and a revolving loan fund for energy performance contracting (ESPC) projects; offering incentives to combine PECO funds with ESPC projects; and introducing performance-based bonuses and penalties.

- **Energy performance contracting reforms (F.S. 1013.23)** — extending the maximum contract term; adjusting payback requirements; and requiring energy performance contracting for underperforming districts.

- **Benefits to legislators** — job creation and economic development, improved student health and academic performance, long-term fiscal responsibility, and community resilience.

These recommendations are strategic outputs. They are informed by the analysis but are not hypotheses tested in this research.
