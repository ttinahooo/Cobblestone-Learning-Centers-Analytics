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
## Case1:

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
## Case2:

### Project Summary: Evaluating New Curricula for the SkillsAdvantage Program
_Sources: GBA 424 - Team 2A Case 2.pdf, Code - GBA424 Case 2.pdf, Case 2 Memo-2.pdf_

#### (1) Methodology & Findings on Curriculum Effectiveness
- **Approach** _(Case 2 Memo-2 p.2; GBA 424 p.2-5)_:
  
1. Experimental Design: Assigned three new curricula (A, B, C) and the current program to 8 districts (e.g., Program B in Camas/Sherwood). Analyzed 9,035 students’ intake and post-program scores.
- Participants: 9,035 students across 8 districts.
- Treatment/control groups: Three new curricula (A, B, C) + current program, assigned to districts:

|Curricula | Districts|
|:----------: |:----------: |
|A | Lake Oswego, Ridgefield|
|B | Camas, Sherwood|
|C | Riverdale, Wilsonville|
|Current | St. Paul, Beaverton|

- Data Collected: Intake and post-program assessment scores (Reading, Writing, Math Calc/NoCalc, Total).

2. Statistical Analysis _(GBA 424, p.4-6; Code, p.2-5,9)_

- ANOVA & Tukey’s HSD: Compared mean differences in Delta_total (post-program − intake scores).

- Regression Models:

```r 
sumscore_delta ~ sumscore + delta_date + edu_program (baseline impact).
```

```r
Interaction terms (sumscore*edu_program) to control for district-level bias.
```

- Tried Difference-in-Differences (DiD): isFinished*edu_program to isolate program effects, but shouldn't use DiD here because there's no common trend before the experinment.

**3. Visualization** _(Code, p.5-6,12)_

- Violin plots: Score distributions by program.

- Histograms/Boxplots: Subject-wise trends (e.g., MathNoCalc vs. Writing).

**4. Key Insights** _(GBA 424, p.7-8)_
1. Program B had the highest impact:

  +13.76 mean Delta_total vs. Program A (Tukey’s HSD, p.5).

  +86.47 vs. Program C, which performed worst (−72.71 Delta_total vs. A).

2. Non-Randomization Bias:

  Districts assigned to Program B (Camas/Sherwood) had higher baseline scores.

  Regression with interaction terms (sumscore*edu_program) adjusted for this (Code, p.9).

3. Program A showed strengths in Reading/Writing, suggesting a hybrid approach (A+B) could optimize outcomes.

#### (2) Recommendations & Business Implications
**Actionable Insights** _(GBA 424, p.8-9)_
1. Adopt Program B for its consistent superiority, but combine with Program A’s strengths (e.g., reading/writing focus).

2. Conduct Cost-Benefit Analysis: Weigh performance gains against implementation costs.

3. Pilot Testing: Roll out Program B in additional districts to validate real-world efficacy.

4. Survey Design after the Analysis.

**Limitations**
District Bias: Non-random assignment may inflate Program B’s apparent success.

#### (3) Key Functions & Techniques
**Statistical Tests: ANOVA & Tukey’s HSD**
```r
model1 <- aov(Delta_total ~ educational_program, data = delta_experiment)  
TukeyHSD(model1)
```
**Visualization: Violin Plots (Compared score distributions by program)**
```r
ggplot(dt_melt_program, aes(x = edu_program, y = meandelta)) +  
  geom_violin() +  
  stat_summary(fun = "mean", geom = "point", color = "red")
```

**Notable Techniques**


**ANOVA & Tukey’s HSD** – Compared mean score differences across programs.
**Interaction Terms in Regression** – Controlled for district-level bias (e.g., sumscore*edu_program).
**Percentage Change Metrics** - Normalized deltas (e.g., delta_r/(score_reading - delta_r)) for fair comparisons.
**Violin Plots** – Visualized score distributions (intake vs. post-program) by program.

**Conclusion**
The analysis strongly supports adopting Program B, but emphasizes:

Hybridization with Program A’s strengths.

Pilot testing to address district bias.

Cost-benefit analysis before full-scale rollout.

**Workflow:** Data cleaning → Descriptive stats → Visualization → Hypothesis testing → Regression modeling. Advanced techniques (interaction terms) ensured robust conclusions despite non-randomized design.


---
## Case3:

### Project Summary: AI-Powered Chatbot for Personalized Online Learning
_Sources:_

_Case 3 Memo.pdf: Survey design, student preferences, and response scales._

_case3.pdf: Clustering results, student profiles, and recommendations._

_CLC_Case #3 - Team 2A.pptx: Chatbot features, risk mitigation strategies, and business objectives._

#### (1) Methodology & Findings on Student Segmentation (Clustering)

- **Approach** _case3.pdf (Pages 2-4)_:
  
**0. Descriptive analysis on Survey data**

**1. K-means clustering (k=4) on survey responses (11 questions, 7-point Likert scale).**
Step 1: Choose the optimal number of clusters

<img width="614" alt="image" src="https://github.com/user-attachments/assets/647d19a2-a833-4b69-86f8-31e2d30580ff" />

Step 2: Clustering result (with visualization):

<img width="578" alt="image" src="https://github.com/user-attachments/assets/acf16650-3615-4cc2-967e-cc3004aca0d9" />
<img width="638" alt="image" src="https://github.com/user-attachments/assets/8d9e95ef-18ac-4591-8e09-0ffdf410f6d7" />

**2. Give recommendations for each group:**

Example: (Variables are survey questions)

<img width="508" alt="image" src="https://github.com/user-attachments/assets/b86eb437-4517-44cd-90e0-4e59531a7423" />
<img width="535" alt="image" src="https://github.com/user-attachments/assets/3e1e3b8a-2615-46ac-980f-e1122b5f9e36" />
<img width="559" alt="image" src="https://github.com/user-attachments/assets/5f6b98b3-9e40-4193-9cec-9c508395a54e" />

#### (2) Chatbot Design Based on Segmentation

- **Approach** _CLC_Case #3 - Team 2A.pptx (Slides 2-5)_
  
1. Prioritized features aligned with cluster preferences (e.g., time management tools for Reserved Planners).
2. Embedded ethical AI rules (e.g., no data storage).

#### (3) Risk Mitigation

- **Approach** _case3.pdf (Pages 8-9); CLC_Case #3 - Team 2A.pptx (Slide 5)_
  
Addressed privacy (anonymized data) and bias (neutral tone).

#### (4) Key Functions & Techniques

1. Clustering: kmeans() to segment students (case3.pdf, Page 3).
2. Descriptive Stats: Mean scores per question group (G/S/A/T) (case3.pdf, Page 4).
3. Chatbot Logic: Custom prompts for each cluster (CLC_Case #3, Slide 4).

#### (5) Sample Coding

- Visualization
```r
ggplot(dt, aes(x=G1)) + geom_histogram()  # Histogram for single question
dt_melt <- melt(dt, "ID")                 # Reshape for boxplots
ggplot(dt_melt, aes(x=variable, y=value)) + geom_boxplot()  # Compare all questions
```

- Feature Standardization
 ```r
#Normalize Likert-scale responses (1–7) for clustering
dt_norm <- apply(dt, 2, function(x) {(x - mean(x))/sd(x)})  # Z-score normalization
```
- Determine Optimal Clusters (k)
```r
 fviz_nbclust(dt_norm, kmeans, method="silhouette",
             iter.max=100, nstart=20, k.max=15)
```
-------
-------
## quick segmentation review:

**(1) Criteria for a Useful Segmentation**: 
1. homogenity within & Heterogenity between;
2. systematic behaviors(interaction effects. _The bigger the interaction, the better the segmentation variable_. Because: A large interation means _the effect of one variable_, e.g. discount, _depends heavily on another variable_, e.g. age/region/past purchases, so those groups are well seperated, thus better segmented: a 10% discount increases sales by 5% for urban customers but 20% for rural customers, making location a key segmentation variable for pricing strategies);
3. marketing mix efficiency (cost-driven/demand-driven/..) For different dicision, we may have different segmentation variables. 

**(2) Segmentation Methods**: 
1. **A priori segmentation**: we already have the segment definition, like demographic, geographic, firmographic, .. we don't need to use any statistical method
2. **Post hoc segmentation**: segments created with unsupervised ml, typically have large numbers of variables(5-20), like preferences for diff snacks, attitudes,..

**Post hoc Segmentation**: Cluster Analysis is commonly used in this topic. And K-means is one type of Cluster Analysis.

**1. First consider feature scaling/standardization**

Reason: Distance-based models _(e.g., KNN, K-means, SVM)_ are sensitive to scales; Gradient-based models _(e.g., linear regression, neural networks)_ converge faster. Avoid Feature Dominance: the model will overly rely on B if Feature B ranges large

Some methods: 
|Method|When to Use|
|:------:|:-----:|
|Standardization (Z-score)|Data is near Gaussian (normal)|
|Min-Max Scaling|Bounded data (e.g., image pixels 0-255)|
```r
dt_norm <- apply(dt, 2, function(x) {(x - mean(x))/sd(x)})
```

**2. Choose Number of Clusters**
Methods: Elbow rule,..
```r
## WSS plot - look for the "elbow" point where WSS starts decreasing linearly → optimal k.
fviz_nbclust(dt_norm,kmeans,method="wss",iter.max=100,nstart=20,k.max=15)

## Avg silhouette width - find max (The k with the highest silhouette score is optimal.)
fviz_nbclust(dt_norm,kmeans,method="silhouette",iter.max=100,nstart=20,k.max=15) 
```

**3. [Segmentation](https://rpkgs.datanovia.com/factoextra/reference/fviz_cluster.html)**
```r
fviz_cluster(km, geom = "point", data = dt_norm) + ggtitle(paste("k =",nClust))+
    scale_colour_manual(values = c("#f8d86a", "#eeb8c3", "#9abeaf","#619ac3")) +
    scale_fill_manual(values = c("#f8d86a", "#eeb8c3", "#9abeaf","#619ac3")) 
```
**4. Confusion matrix**
Compares k-means clusters (converted to "AB"/"NO") with true labels (class).
Reports accuracy, precision, recall, etc., to validate clustering quality.
```r
dt$cluster <- as.factor(ifelse(dt$cluster=='1','AB','NO'))
confusionMatrix(vc$class, vc$cluster)  # From `caret` package
```

-------
-------





