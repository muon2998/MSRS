# JHU MSRS Abstract Scoring

**Goal: While minimizing confounding factors, identify top-scoring abstracts in each concentration to select podium, oral, and poster presenters**

## Background

### Scoring Structure
- Each abstract is placed into one of the 5 concentrations, based on student preference.
-	Each abstract is scored on 4 equally weighted sections (background, methods, results, conclusion) and a raw sum score is calculated.
-	Each judge reviews ~10 abstracts and each abstract is reviewed by ≥ 4 judges.
- Rankings are calculated for top-scorers in each of the of the concentrations.

### Models
> All models are wrong but some are useful. - *George E. P. Box*

**We can start by briefly looking at the problems with common scoring systems:**
##### Raw Score
Method: Compute the average raw total scores for each abstract
Issue: Raw scores do not account for difference in bias/harshness of judges in scoring abstracts, so top-scoring abstracts may all come from judges who grade more easily.

##### Z-score/t-score
Method: For a given judge, the judge has an “judge abstract average and standard deviation” based on the 10 abstracts they reviewed. Then, each abstract can be assigned a z-score for each of the judges, and then we can average the z-scores for each abstract to rank abstracts.
Formula: 

<img src="http://latex.codecogs.com/svg.latex?Z_{judge,student}&space;=&space;\frac{rawScore_{judge,student}-\overline{rawScore_{judge}}}{\sqrt{\frac{(\sum{rawScore_{j,s}-\overline{rawScore_j})^{2}}}{n-1}&space;" title="http://latex.codecogs.com/svg.latex?Z_{judge,student} = \frac{rawScore_{judge,student}-\overline{rawScore_{judge}}}{\sqrt{\frac{(\sum{rawScore_{j,s}-\overline{rawScore_j})^{2}}}{n-1} " />

*While this helps to control for some of the judge bias, 2 problems include:*
- Issue 1: Say judge A gets 9-10 true excellent abstracts and scores them highly. Judge B gets 9-10 true poor abstracts and scores them poorly. The z-scores for both sets of abstracts would be similar even though the abstracts from judge A were truly “good” abstracts while those from B were not. Thus, better abstracts may be penalized in this system.
- Issue 2: The z-score method does not take into consideration of “repeated measurements”, in which the same abstract is read by different judges, so each z-score for a given is computed completely independently from the other judges who scored it

#### Linear Mixed Effects Model
**Based on the problems above, as well as other statistical advantages, the linear mixed effects model (LME, LMEM) is a better fit


## Running LMEM in R

## Resources
