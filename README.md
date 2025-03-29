# Cobblestone Learning Centers (CLC) Analytics
A data analysis case using descriptive analysis, causal analysis, segmentation, and AI training.

## 0. Background:

|| Program1        | Program2   | Program3  |
|---------| :-------------: |:-------------:| :-----:|
|**Location**| centers     | centers & on-line | centers, on-line starting in 2018(future) |


|Assessment| writing |  reading  | "calculator" math  | "no calculator" math |
|---------| :-------------: |:-------------:| :-----:|:-----: |
|**Points**| 100 |800    | 800 | 800|

When students first enroll at CLC, they take the general assessment and their scores are recorded as “intake”.

Every time a student completes one of CLC’s programs, they re-take the assessment.

**Objectives:**
  1. Use the student assessments database to understand student achievement and student/program outcomes across the student population.
  2. Understand program effectiveness in center-based vs. on-line delivery modality.
  3. CLC is considering adapting the SkillsAdvantage program to an on-line format. We would like to use insight from the transition to on-line tutoring to help decide whether we should pursue this initiative.
---
## 1. Case1:

### Project Summary: Enhancing Educational Outcomes through Analytics

_(Sources: CLC_Objectives & Analysis.pdf, Case 1- 2A.pdf)_

#### (1) Methodology & Findings on Student Achievement

- **Approach** _(CLC_Objectives p.3,19; Case 1 p.3-4)_:
1. Calculated average scores across 4 components (Reading, Writing, Math Calc/NoCalc) from 2016-2019
2. Visualized trends using line charts (R ggplot2)


- **Key Insights** _(Case 1 p.3-4,7)_:
1. Math (Calc): Highest scores but most variability ("largest spread in scores")
2. Math (NoCalc): Slowest improvement ("students may need additional support")
3. Writing: Lowest/steadiest scores ("hardest to improve" per regression analysis)

#### (2) Methodology & Findings on Program Effectiveness

- **Approach** _(CLC_Objectives p.7-9,19-20; Case 1 p.5,10)_:
1. Grouped by program (Tutoring/Skills/Refresh) and calculated subject-wise deltas
2. Used ANOVA _(p.12-14)_ to validate program impact

- **Key Insights** _(Case 1 p.7,10)_:
1. Tutoring: Most effective (+49.85 total score impact, p.18)
2. Refresh: Weakest results (mathNoCalc 17.8 vs tutoring's 25.4 delta, p.14)

#### (3) Program Utilization Patterns

- **Approach** _(CLC_Objectives p.9,21; Case 1 p.8-9)_:
1. Analyzed participation distribution (bar charts)
2. Tracked student progression via no_program variable

- **Key Insights** _(Case 1 p.7-8)_:
1. Participation: Skills (75%) > Tutoring > Refresh _(Case 1 p.8 chart)_
2. Sequence: 85% followed Skills→Tutoring→Refresh _(p.18 regression)_

#### (4) Online vs Center Delivery

- **Approach** _(CLC_Objectives p.6,11,21; Case 1 p.12-13)_:
1. Compared location-based deltas (t-tests)
2. Tracked modality shift 2016-2019

- **Key Insights**  _(Case 1 p.12, CLC p.11)_:
1. Online Reading scores +4.98 vs center _(p.11 coefficient table)_
2. Online adoption grew from 14% (2016) to 85% (2019) _(Case 1 p.13)_

#### (5) Evidence-Based Recommendations

_(All from Case 1 p.14)_:

1. **Bundle programs** as Skills→Tutoring→Refresh (supported by p.18 sequence analysis)
2. **Price premiums** for Tutoring (49.85 impact) and MathNoCalc (25.4 delta)
3. **Rebrand Writing** to "sense of beauty" (hardest to improve per p.7)
4. **Leverage Wilsonville teachers** (Reading scores +6.12 vs avg, p.14)
5. **Expand online courses** (+4.98 Reading impact, p.11)



#### (6) Key Functions Used

1. **Data Wrangling:** group_by(), summarise(), mutate(), filter()
2. **Visualization:** ggplot(), geom_line(), geom_bar()
3. **Statistical Tests:** lm(), anova()
4. **Data Merging:** left_join()


**Notable Techniques**

- Used lag() for delta calculations (p.21)
  
- Implemented facet_wrap() for multi-program visualizations (p.10)
  
- Created interaction terms (location*program) in regression (p.22)

The code follows a clear workflow: data prep → descriptive analysis → visualization → statistical testing. 
The most complex operations involve the delta score calculations and regression modeling for program impact assessment.

sample code:

```r
# Add another variable:
casedt2 %>%
group_by(student_id) %>%
arrange(student_id,date) %>%
mutate(no_program = row_number())

# ANOVA tests (p.12-14)
anova(lm(delta_r ~ program + district + delta_date + score_reading + location, data = casedt))

# Regression models (p.13-15)
lm_r <- lm(delta_r ~ program + district + delta_date + score_reading + location, data = casedt)
lm_w <- lm(delta_w ~ program + district + delta_date + score_writing + location, data = casedt)

# Program impact analysis (p.18)
summary(lm(sumScore ~ program + no_program + delta_date, data = casedt2))

# Visualization
ggplot(data = average_scores, aes(x = date)) +
  geom_line(aes(y = avg_reading, color = "Reading")) +
  geom_bar(aes(y = num_students, fill = program), 
           stat = "identity", 
           position = "dodge")
```
---
