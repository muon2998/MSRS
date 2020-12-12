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
#### Raw Score
Method: Compute the average raw total scores for each abstract
Issue: Raw scores do not account for difference in bias/harshness of judges in scoring abstracts, so top-scoring abstracts may all come from judges who grade more easily.

#### Z-score/t-score
Method: For a given judge, the judge has an “judge abstract average and standard deviation” based on the 10 abstracts they reviewed. Then, each abstract can be assigned a z-score for each of the judges, and then we can average the z-scores for each abstract to rank abstracts.
Formula: 

<img src="http://latex.codecogs.com/svg.latex?Z_{judge,student}&space;=&space;\frac{rawScore_{judge,student}-\overline{rawScore_{judge}}}{\sqrt{\frac{(\sum{rawScore_{j,s}-\overline{rawScore_j})^{2}}}{n-1}&space;" title="http://latex.codecogs.com/svg.latex?Z_{judge,student} = \frac{rawScore_{judge,student}-\overline{rawScore_{judge}}}{\sqrt{\frac{(\sum{rawScore_{j,s}-\overline{rawScore_j})^{2}}}{n-1} " />

*While this helps to control for some of the judge bias, 2 problems include:*
- Issue 1: Say judge A gets 9-10 true excellent abstracts and scores them highly. Judge B gets 9-10 true poor abstracts and scores them poorly. The z-scores for both sets of abstracts would be similar even though the abstracts from judge A were truly “good” abstracts while those from B were not. Thus, better abstracts may be penalized in this system.
- Issue 2: The z-score method does not take into consideration of “repeated measurements”, in which the same abstract is read by different judges, so each z-score for a given is computed completely independently from the other judges who scored it

#### Linear Mixed Effects Model
Based on the problems above, as well as other statistical advantages, the linear mixed effects model (LME, LMEM) is a better fit.
##### Math (briefly)
(Multiple) linear regression, <img src="http://latex.codecogs.com/svg.latex?\inline&space;Y=\beta_0&plus;\beta_1X_1&plus;...&plus;\beta_nX_n&plus;\epsilon" title="http://latex.codecogs.com/svg.latex?\inline Y=\beta_0+\beta_1X_1+...+\beta_nX_n+\epsilon" />, is useful when all of the observations come from a single homogeneous group.
However, LMEM is more useful when there are nested groups within a larger dataset. It introduces the ideas of fixed effects and random effects. Basically, a fixed effect is the variable of interest that we want to use to make a prediction. The random effect is a variable for which we have information, but we want to control for its effect on the outcome. In the case of MSRS, we would have the following:
Dependent variable: Abstract Score
Fixed effect: Abstract (ID)
Random effect: Judge

*LMEM also allows an estimation of within-judge correlation, i.e. how correlated are the scores across abstracts for the same judge.*

<img src="http://latex.codecogs.com/svg.latex?Score_{abstract}=&space;\beta_0&space;&plus;&space;a_{judge}&space;&plus;&space;\beta_{abstract}X_{abstract,judge}&space;&plus;&space;\epsilon_{abstract}&space;" title="http://latex.codecogs.com/svg.latex?Score_{abstract}= \beta_0 + a_{judge} + \beta_{abstract}X_{abstract,judge} + \epsilon_{abstract} " />
<img src="http://latex.codecogs.com/svg.latex?\inline&space;a_{judge}:randomeffectofjudge" title="http://latex.codecogs.com/svg.latex?\inline a_{judge}:randomeffectofjudge" />
<img src="http://latex.codecogs.com/svg.latex?\inline&space;\epsilon_abstract:residual" title="http://latex.codecogs.com/svg.latex?\inline \epsilon_abstract:residual" />




##### Terms
Fixed effect: 
Random effect: 

## Running LMEM in R

## Resources
