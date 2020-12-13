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

(Multiple) linear regression, <img src="http://latex.codecogs.com/svg.latex?\inline&space;Y=\beta_0&plus;\beta_1X_1&plus;...&plus;\beta_nX_n&plus;\epsilon" title="http://latex.codecogs.com/svg.latex?\inline Y=\beta_0+\beta_1X_1+...+\beta_nX_n+\epsilon" />, is useful when all of the observations come from a single homogeneous group.
However, LMEM is more useful when there are nested groups within a larger dataset, such as in the abstract data. It introduces the ideas of fixed effects and random effects. Basically, a fixed effect is the variable of interest that we want to use to make a prediction. The random effect is a variable for which we have information, but we want to control for its effect on the outcome. In the case of MSRS, we would have the following:

**Dependent variable**: Abstract Score

**Fixed effect**: Abstract (ID)

**Random effect**: Judge

<img src="http://latex.codecogs.com/svg.latex?Score_{abstract}=&space;\beta_0&space;&plus;&space;a_{judge}&space;&plus;&space;\beta_{abstract}X_{abstract,judge}&space;&plus;&space;\epsilon_{abstract}&space;" title="http://latex.codecogs.com/svg.latex?Score_{abstract}= \beta_0 + a_{judge} + \beta_{abstract}X_{abstract,judge} + \epsilon_{abstract} " />
<img src="http://latex.codecogs.com/svg.latex?\inline&space;a_{judge}=\textrm{random&space;effect&space;of&space;judge}" title="http://latex.codecogs.com/svg.latex?\inline a_{judge}=\textrm{random effect of judge}" />
<img src="http://latex.codecogs.com/svg.latex?\inline&space;epsilon_{abstract}=\textrm{residual}" title="http://latex.codecogs.com/svg.latex?\inline epsilon_{abstract}=\textrm{residual}" />
<img src="http://latex.codecogs.com/svg.latex?\inline&space;X_{abstract,judge}=\textrm{raw&space;score&space;for&space;given&space;abstract&space;from&space;a&space;given&space;judge}" title="http://latex.codecogs.com/svg.latex?\inline X_{abstract,judge}=\textrm{raw score for given abstract from a given judge}" />

LMEM is able to:
- Ensure good and bad abstracts are well-differentiated, unlike in the z-scores 
- Account for judge bias, an estimation of within-judge correlation, i.e. how correlated are the scores across abstracts for the same judge.
- Introduce shrinkage into the model for the random effect, which helps reduce some statistical error

## Running LMEM in R
In the main directory, you can find the [R Markdown file](LME random effects.Rmd) that runs the script.
In the [input_text_files folder](input_text_files/), you will find
- 1 text file ([all_raw.xlsx](input_text_files/all_raw.xlsx)) that contains all the raw data (judge, abstract ID, score for each section, etc.)
- 5 text files that contain the names of the judges
  - *Note 1: In hindsight, it be easier to have one text/Excel file that contains 2 columns -- Judge and Concentration. Then, the R script can pull the appropriate judges as needed rather than having 5 separate files for judges.*
  - *~~Note 2: It could be worth changing the R Markdown file so that it takes input .xlsx or .csv files instead of .txt files since the former would be cleaner, and you wouldn't have to copy the data into a .txt file if it changes.~~* -- Fixed this 12/13/2020

In the Example folder, you will find
- [PDF](Example/LME-random-effects.pdf) of running the R script on the "Clinical" concentration
- [Output files](Example/) from R after running the "Clinical" concentration

### What does the R script do?
The details are commented and provided in the RMarkdown file -- I recommend looking at the example R script on the clinical concentration.
However, here is a broad overview:
1. Load the data into R, which includes raw abstract scores, abstract ID, and judges.
2. Sum the abstract scores from the 4 categories (background, methods, etc.)
3. Run the LMEM on this dataset with the equation as described earlier.
4. Based on LMEM, remove the residual and random effect terms for each abstract to get a predicted score, aka adjusted score, for each abstract.
5. For fun, display a caterpillar plot for the random effect of the judges out of curiosity to see magnitude and direction of judge bias.
6. For modeling and ensuring fit, display a residual plot of the fitted data to ensure it is uniformly distributed without any tendencies. 
  - *There are more options to check how well the model fits the data, both numerically and graphically. Check out the links in the resources.*
7. (Less useful) Create an output file that for each row of the data (i.e. for each judge and abstract), gives the LMEM score and Z-score. File: concentration_raw_adjusted.xlsx
8. (More useful) Create an output file that contains the averaged LMEM, averaged Z-score, and averaged raw score for each of the abstracts. File: concentration_means_adjusted.xlsx

### Data Pre-Processing
In the current version of the R script, you have to check that the data are in the appropriate format.

*Note: The R Markdown file can always be modified so that it checks some of these things instead and make it more elegant, but for some reason thought at the time these were faster to check "by hand"*

- [ ] Set the workding directory to yours in the first line of the R script. `setwd("C:/Users/HGupta/Documents/Hopkins/MSRS/2020/Data/Results")`
- [ ] Formatting for **[concentration_judges.txt](input_text_files)**: 1-column with judge names -- should match exactly those in [all_raw.xlsx](input_text_files/all_raw.xlsx)
- [ ] Formatting for **[all_raw.xlsx](input_text_files/all_raw.xlsx)**: A tab-separated 6-column file (*or .xlsx/.csv if you change the script*). If you used the same Google Form, this was just a copy/paste straight from the Google Spreadsheet.
  - Column 1: Judge name. **The judge names should be 100% identical across different rows and match the text files that have judge names for each concentration.**
    - Note: You could add a function that performs proper capitalization on all the judge names in both [all_raw.xlsx](input_text_files/all_raw.xlsx) and concentration_judges.txt. I would recommend using the `str_to_tile` command from the `stringr` library with `apply`.
  - Column 2: Abstract ID. Check to make sure the IDs of the submitted abstracts match the ones you assigned to each respective judge. 
  - Column 3-6: Scores for the background, methods, results, and conclusion.
- [ ] I sadly didn't have it loop through the 5 concentrations, so R script must be run 5 times. The only thing you have to change each time you run it is this line: `SC_category = "clinical"`. Change "clinical" to each respective concentration, which were called basic, clinical, heart, history, public. 
- [ ] If you don't change the R script, ensure all these files are in the same working directory:
  - [ ] [all_raw.xlsx](input_text_files/all_raw.xlsx)
  - [ ] [basic_judges.txt](input_text_files/basic_judges.txt)
  - [ ] [clinical_judges.txt](input_text_files/clinical_judges.txt)
  - [ ] [heart_judges.txt](input_text_files/heart_judges.txt)
  - [ ] [history_judges.txt](input_text_files/history_judges.txt)
  - [ ] [public_judges.txt](input_text_files/public_judges.txt)

## Resources
The explanation for LMEM was extremely brief, so here are a few resources to learn more about it. Feel free to try different variations of LMEM or another model if that ends p being a better fit for the data.

[LMEM and LMEM in R](https://www.youtube.com/watch?v=9BDA5b-gtbc&ab_channel=PagePiccinini): Video. She goes through the equations for LMEM pretty well and compares the difference of adding random effects into a model.

[LMEM Overview and Fitting](https://www.youtube.com/watch?v=QCqF-2E86r0&ab_channel=MatthewE.Clapham): Video. He explains LMEM in a different, compact way as well, and also goes through some additional ways to check how well the model fits the data.

[Intro to Linear Mixed Models](https://ourcodingclub.github.io/tutorials/mixed-models/): Webpage. Alternative to watching videos for going through LMEM.
