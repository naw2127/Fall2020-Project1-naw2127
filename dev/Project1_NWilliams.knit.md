---
title: 'Project 1: Analysis of ANES Voter Data'
output:
  pdf_document: default
  html_document: default
---
***
## What does Polarization Look Like in American Politics? 
### An Exploratory Data Analysis
#### by: **Natalie Williams** 
*naw2127@columbia.edu*

***
## **I. Introduction**

You may have heard the phrase "Political Polarization" in the news recently. A New York Times article described polarization as a "National Security Threat". [link](https://www.nytimes.com/2020/09/22/opinion/trump-national-security.html) \n Further, Americans surveyed by the Chicago Council Survey of American Public and US Foreign Policy placed political polarization within the top five critical threats to the US. [link](https://www.thechicagocouncil.org/publication/lcc/divided-we-stand)
While in January 2020, CNN reported that political polarization was at a 74 year high, citing an 82 point gap between Republican approval of Trump and Democratic. [link](https://www.cnn.com/2020/01/21/politics/trump-political-polarization/index.html)

### How is Political Polarization Defined?
The Pew Research Center, a highly respected nonpartisan fact tank, defines Political Polarization as the following:
> Political Polarization conceptually represents some kind of aggregated ideological differences between parties. 

### How do Political Scientists Measure It?
There are multiple methods of measuring political polarization. According to an [article](https://journals.sagepub.com/doi/10.1177/0002764218759576) published in American Behavioral Scientist, political scientists are adopting a new method to measure polarization. This method focuses on divisions such as: religious against secular; nationalist against globalist; traditional against modern; rural against urban.

Following this standard, to study polarization I will focus on five controversial areas: 

- Military Spending 
- Abortion
- Government Aid to Minorities
- Immigrates
- Healthcare

These issues frequently divide voters. Democratic and Republican politicians\n
typically take firm stances on these issues to resonate with their voter bases.

Healthcare, for instance, was a very large part of President Obama's 2008 and\n 
campaign. Dubbed "Obamacare"- the Affordable Care Act (ACA) sought to reduce the number of Americans without healthcare insurance, and constrain healthcare costs. [link](https://aspe.hhs.gov/pdf-report/health-insurance-coverage-and-affordable-care-act-2010-2016)
The ACA was a triumph for Democrats and Liberals, (the more Left strain of the Democratic Party),
but many Republicans and Conservatives (the right-wing counterpart to Liberals) saw ACA
as an overreach of government power and an excessive burden to the national budget. So, 
one of President Trump's promises before he was elected, was to reverse ACA as soon as he was
in Office. Making good on his promise, albeit a little late,  Trump asked the Supreme Court to overturn ACA in September of 2020, while COVID-19 cases were still swelling, and as ACA covers nearly 23 million Americans.[link](https://www.nytimes.com/2020/06/26/us/politics/obamacare-trump-administration-supreme-court.html)  
\n 
The government's role in Healthcare is a bitter debate, with politicians on both
sides submitting to their Party's idea of what it should be. 

Like Healthcare, the other topics of analysis make sharp divisions between Democrats
and Republicans. For young voters like myself, it is hard to imagine if this intense 
divide has always been the case. 

For this reason, I will compare how disparate Republican's and Democrat's views 
on the issues of: Military Spending, Abortion, Government Aid to Minorities, Immigrates, and Healthcare have grown over the years. 

To remain within the scope of this project, five issues are analyzed. The benefit of analyzing five issues instead of one or two deeply is that some issues are polarizing for other reasons that may not be completely respective to their party. For instance, Abortion is very polarizing mostly because it is viewed unethical by many Christian groups.[link](https://www.jstor.org/stable/3511563?seq=1#metadata_info_tab_contents)

Thus, to prevent focusing on one issue that many be especially polarizing or *not* polarizing, multiple issues are investigated here. 

This analysis chooses breadth over depth to capture overall trends over the decades, this trade-off prioritizes holistic trends within the Democratic and Republican Parties over time.  

### **II. Methodology **
To find what polarization looks like in the US, survey results collected by the American National Election Studies[(ANES)](https://electionstudies.org/), will be investigated.  

In the ANES surveys, respondents place their political identity on a spectrum of Strong Democrat-> Strong Republican. This feature will be used to split the data between Republicans and Democrats. Other questions ask about whom they voted for, but since people do not always vote for the presidental candidate of their party, I felt it would be accurate to subset the data based on how respondents described themselves. 

##### Step 1: Load in Data and Libraries and Preprocessing

```r
library(data.table)
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:data.table':
## 
##     between, first, last
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(haven)
library(ggplot2)
library(tidyverse)
```

```
## -- Attaching packages ---------------------------- tidyverse 1.3.0 --
```

```
## v tibble  3.0.3     v purrr   0.3.4
## v tidyr   1.1.2     v stringr 1.4.0
## v readr   1.3.1     v forcats 0.5.0
```

```
## -- Conflicts ------------------------------- tidyverse_conflicts() --
## x dplyr::between()   masks data.table::between()
## x dplyr::filter()    masks stats::filter()
## x dplyr::first()     masks data.table::first()
## x dplyr::lag()       masks stats::lag()
## x dplyr::last()      masks data.table::last()
## x purrr::transpose() masks data.table::transpose()
```

```r
library(reshape2)
```

```
## 
## Attaching package: 'reshape2'
```

```
## The following object is masked from 'package:tidyr':
## 
##     smiths
```

```
## The following objects are masked from 'package:data.table':
## 
##     dcast, melt
```

##### 1a) Filter and Save Questions Used in Analysis
- Questions used: 
  - **"Party Identification of Respondent- 7-point Scale"**
  - **"Government Health Insurance Scale"**
    - There are two Healthcare questions. I opted for the second one as it asked respondents to place themselves on a spectrum, while the first was a YES/NO question. 
  - **"Thermometer - Illegal Aliens"**
    - There are many thermometer questions in the ANES surveys. Lower "Temperatures" correspond to feeling colder towards the group in question, while higher questions correspond to "warmer" feelings. 
  - **"When Should Abortion Be Allowed" & "By Law, When Should Abortion Be Allowed"**
    - This question and answers were slightly reworded in 1980, the rephrased answers have equivalent         meanings to the older question. For these results I will coalesce the results of these questions         into one "abortion" factor.  
  -**"Defense Spending Scale"** 
    - Similar to the healthcare question, this question was rewritten so respondents placed themselves on a spectrum instead of answering a YES/NO question. I opted for the spectrum question
  -**"Aid to Blacks/Minorities Scale"**
    - Respondents place themselves on a scale of 1-7 on how much or little they believe the government should aid minority groups. 
    
- Save new list as anes_use


```r
anes_dat <- read_sav("../data/anes_timeseries_cdf.sav")

anes_use = anes_dat %>%
mutate(
  year=as_factor(VCF0004),               # Year of Survey
  
  #party of respondent (1-7,0 no party)
  party_scale =as_factor(VCF0301),       # Party Scale of Respondent
 
  # Polarization Questions
  healthcare2 = as_factor(VCF0806),      # Healthcare
  illegalAliens = as_factor(VCF0233),    # Illegal Immigrants thermometer
  abortion1 = as_factor(VCF0837),        # When should abortion be allowed
  abortion2 = as_factor(VCF0838),        # When should abortion be allowed 
  militarySpending2 = as_factor(VCF0843),# Military Spending
  govHelp = as_factor(VCF0830)           # Should Government help minority groups
  
)

anes_use <- data.table(anes_use)%>%
  select(year, party_scale, abortion1, 
         abortion2, illegalAliens, govHelp,healthcare2, 
         militarySpending2
        )
 

save(anes_use, file="../output/data_use.RData")
```
##### 1b) Subset all self reported Democrats and Republicans

```r
load(file = "../output/data_use.RData")

# Get all Respondents that call themselves Democrats
all_Democrats <- anes_use%>%
  filter(!is.na(party_scale))%>%
  filter(party_scale %in% c("1. Strong Democrat", "2. Weak Democrat", "3. Independent - Democrat"))


# Get all respondents that call themselves Republicans
all_Republicans <- anes_use%>%
  filter(!is.na(party_scale))%>%
  filter(party_scale %in% c("7. Strong Republican", "6. Weak Republican", "5. Independent - Republican"))

save(all_Democrats, file="../output/all_Democrats.RData")
save(all_Republicans, file="../output/all_Republicans.RData")
```
### **III. Results **
##### Lets look at how Democrats and Republicans Feel about...
##### 1a) Abortion

Since the Abortion question was reworded, the questions have different factor levels. 
The answers are mutually exchangeable to force the levels of the second question to equal those of the first. 
Next, the data of the newer (second) abortion question was coalesced into one column. This was done preserving party scale, year and of course how the response of the abortion question. 

```r
load(file="../output/all_Republicans.RData")
load(file="../output/all_Democrats.RData")

# Update Levels so the two columns can be coalseced. 
levels(all_Republicans$abortion2)<-levels(all_Republicans$abortion1)
levels(all_Democrats$abortion2)<-levels(all_Democrats$abortion1)

# Create New data frame that includes the new coalesced abortion data, year of response and the proportion of an answer within the data set 
abortionRep <- all_Republicans %>%
  mutate(
    abortion1=coalesce(abortion1, abortion2)
  )%>%
  select(year, abortion1)%>%
  filter(!is.na(abortion1))%>%
  group_by(year, abortion1)%>%
  summarize(n=n())%>%
  mutate(
    prop=n/sum(n)
  )
```

```
## `summarise()` regrouping output by 'year' (override with `.groups` argument)
```

```r
abortionDem <- all_Democrats%>%
   mutate(abortion1=coalesce(abortion1, abortion2)
  )%>%
  select(year, abortion1)%>%
  filter(!is.na(abortion1))%>%
  group_by(year, abortion1)%>%
  summarize(n=n())%>%
  mutate(
    prop=n/sum(n)
  )
```

```
## `summarise()` regrouping output by 'year' (override with `.groups` argument)
```

```r
ggplot(abortionRep, aes(x=year, y=prop, fill=abortion1))+
  geom_bar(stat = "identity")+
  theme(axis.text.x = element_text(angle = 90))+
  scale_fill_brewer(palette = "Reds")+
  labs(title="Republicans on Abortion 1972-2016")

ggplot(abortionDem, aes(x=year, y=prop, fill=abortion1))+
  geom_bar(stat = "identity")+
  theme(axis.text.x = element_text(angle = 90))+
  scale_fill_brewer(palette = "Blues")+
  labs(title="Democrats on Abortion 1972-2016")
```


\includegraphics[width=0.5\linewidth]{Project1_NWilliams_files/figure-latex/figures-side-1} \includegraphics[width=0.5\linewidth]{Project1_NWilliams_files/figure-latex/figures-side-2} 

Wow! In the first year of this question, 1972, 23% of Democrats and ***26%*** of Republicans answered that Abortion should never be forbidden! Its very surprising that according to the ANES data, in 1972 a larger proportion of Republicans supported fully legal abortion than Democrats! 

However, results from 2016 are very different. While the proportion of Republicans that answered "Abortion should never by forbidden" remained largely the same at 27.7%, the amount of Democrats that answered that way rose to a staggering 63%! 


In 1972, the largest portion of Republicans chose "Abortion should be permitted only if the life and health of the woman is in danger."(45%) the same was true in 2016, with 37% of Republicans answering this way. 
Democrats, on the other hand, saw a shift. The most common response in 1972 was the same as Republicans: "Abortion should be permitted only if the life and health of the woman is in danger."(46%) However, unlike Republicans, Democrats overwhelmingly chose "Abortion should never be forbidden" in 2016. 

So, while in 1972 Democrats and Republicans had similar views on abortion, in 2016 the majority of Democrats believe abortion should never be forbidden, while Republican views on abortion have only changed slightly


##### 1b) Immigrants
This is a thermometer question. Lower values correspond to "Colder" feelings. Answers that are: "98. DK  (exc. 1964-1968: see VCF0201 note)","99. NA; no Post IW; form III,IV (1972); breakoff,sufficient partial (2016)" were removed to avoid manipulating numeric calculations. 

```r
library(ggrepel)
load(file="../output/all_Democrats.RData")
load(file="../output/all_Republicans.RData")

`%out%` <- function(a,b) ! a %in% b

# Democrats
demIA <- all_Democrats%>%
  select(year, illegalAliens)%>%
  filter(!is.na(illegalAliens))%>%
  filter(illegalAliens %out% c("98. DK  (exc. 1964-1968: see VCF0201 note)",
                               "99. NA; no Post IW; form III,IV (1972); breakoff,   sufficient partial (2016)"))%>%
  group_by(year)%>%
  mutate(
    Average = mean(as.numeric(illegalAliens))
  )%>%
  distinct(year, .keep_all=TRUE)

# Republicans  
repIA <- all_Republicans%>%
  select(year, illegalAliens)%>%
  filter(!is.na(illegalAliens))%>%
  filter(illegalAliens %out% c("98. DK  (exc. 1964-1968: see VCF0201 note)",
                               "99. NA; no Post IW; form III,IV (1972); breakoff,   sufficient partial (2016)"))%>%
  group_by(year)%>%
  mutate(
    Average = mean(as.numeric(illegalAliens))
  )%>%
  distinct(year, .keep_all=TRUE)

# Must combine the Democrate and Republican Results
IA <- data.frame(
  demIA$year, demIA$Average, repIA$Average
)%>%
  rename(
    Year = demIA.year,
    Democrat_Average = demIA.Average,
    Republican_Average = repIA.Average
  )%>%
  mutate(
    Percent_Difference = 2*(Democrat_Average - Republican_Average)/(Democrat_Average+Republican_Average)  
    )


# This Pipe is solely to obtain labels for the beginning and end points. 
IAplot <- IA%>%
  mutate(
    labelDem1 =if_else(Year==first(IA$Year),
                      true = as.character(round(first(IA$Democrat_Average), digits = 1)),
                      false= ""
    ),
    labelDem2 =if_else(Year==last(IA$Year),
                      true = as.character(round(last(IA$Democrat_Average), digits = 1)),
                      false= ""
    ),
    labelRep1 =if_else(Year==first(IA$Year),
                      true = as.character(round(first(IA$Republican_Average), digits = 1)),
                      false= ""
    ),
    labelRep2 =if_else(Year==last(IA$Year),
                      true = as.character(round(last(IA$Republican_Average), digits = 1)),
                      false= ""
    )
  )%>%
  ggplot()+
  geom_line(aes(x= Year, y= Democrat_Average, group=1, color="Democrat"))+
  geom_line(aes(x= Year, y= Republican_Average, group=2, color="Republican"))+
  geom_label_repel(aes(label=labelDem1, x = Year, y=Democrat_Average),nudge_y =1)+
  geom_label_repel(aes(label=labelDem2, x = Year, y=Democrat_Average),nudge_y =1)+
  geom_label_repel(aes(label=labelRep2, x = Year, y=Republican_Average))+
  geom_label_repel(aes(label=labelRep1, x = Year, y=Republican_Average), nudge_y = -1)+
  geom_ribbon(aes(x=Year, ymin= Republican_Average, ymax= Democrat_Average, group=1), show.legend= FALSE, alpha=0.5)+
  scale_color_manual(values= c(
    "Democrat" = "darkblue", 
    "Republican"="red"
  ))+
  ylab("Average Warmness Towards Illegal Aliens/ Immigrants")+
  ylim(0,100)+
  xlab("Year")+
  labs(title= "Warmness Towards Illegal Immigrants By Party from 1988-2016", color= "Party")

ggsave(path="../figs", filename="illegalAlienPlot.png")  
```

```
## Saving 6.5 x 4.5 in image
```

```r
print(IAplot)
```


\includegraphics[width=0.5\linewidth]{Project1_NWilliams_files/figure-latex/unnamed-chunk-4-1} 
This figure, clearly shows the divergence between Republicans and Democrats over time on their feelings towards Illegal Aliens/Immigrants. 
Republican Respondents attitudes only shift downwards 8.8% indicating that their feelings towards illegal immigrants has worsened over time. 
Democrats, alternatively, see a more dramatic increase in warmness towards Illegal Immigrants, seeing a 31.3% increase. 


##### 1c) The Role of Government in Healthcare?

```r
load(file="../output/all_Democrats.RData")
load(file="../output/all_Republicans.RData")

# Democrats and Healthcare
# Two questions but  with different answers use question healthcare2 
`%out%` <- function(a,b) ! a %in% b

demHC <- all_Democrats%>%
  select(year, healthcare2)%>%
  filter(!is.na(healthcare2))%>%
  filter(healthcare2 %out% c("9. DK; haven't thought much about it"))
  

repHC <- all_Republicans%>%
  select(year, healthcare2)%>%
  filter(!is.na(healthcare2))%>%
  filter(healthcare2 %out% c("9. DK; haven't thought much about it"))

save(repHC, file="../output/republicans_HealthCare.RData")
save(demHC, file="../output/democrats_HealthCare.RData")
```

###### How have Democrats and Republicans Views on Healthcare Changed Over Time?

Another Spectrum Question "1" <-  There should be a Nationalized Health Care Service, to "7" <- Entirely Private Insurance Plans. "9" <-9. DK; haven't thought much about it


```r
load(file="../output/democrats_HealthCare.Rdata")
load(file="../output/republicans_HealthCare.RData")

demHC_df <- demHC %>%
  group_by(year, healthcare2)%>%
  summarize(n=n())%>%
  mutate(
    prop=n/sum(n)
  )
```

```
## `summarise()` regrouping output by 'year' (override with `.groups` argument)
```

```r
repHC_df <- repHC %>%
  group_by(year, healthcare2)%>%
  summarize(n=n())%>%
  mutate(
    prop=n/sum(n)
  )
```

```
## `summarise()` regrouping output by 'year' (override with `.groups` argument)
```

```r
ggplot(repHC_df, aes(x=year, y=prop, fill=healthcare2))+
  geom_bar(stat = "identity")+
  theme(axis.text.x = element_text(angle = 90))+
  scale_fill_brewer(palette = "Reds")

ggplot(demHC_df, aes(x=year, y=prop, fill=healthcare2))+
  geom_bar(stat = "identity")+
  theme(axis.text.x = element_text(angle = 90))+
  scale_fill_brewer(palette = "Blues")
```


\includegraphics[width=0.5\linewidth]{Project1_NWilliams_files/figure-latex/unnamed-chunk-6-1} \includegraphics[width=0.5\linewidth]{Project1_NWilliams_files/figure-latex/unnamed-chunk-6-2} 
The results of this question are unique in that substantial differences are seen even in the first year of the question, 1970. In 1970, 32.3% of Republicans felt that private insurance insurances should make up the entirety of health insurance. While only 18% of democrats felt the same way. 

Democrat's most common answer in 1970 was surprisingly, "1. Government Insurance Plan", with 34.9% of the respondents. 

So even in 1970, surveyed Democrats and Republicans felt very different about this. How about in 2016?
In 2016, the proportion of Republicans who answered "1. Government Insurance Plan" dropped from 
17.8% to only 5.3%. 

Also interesting, the proportion of Democrats who answered in favor of fully nationalized healthcare (Option:"1. Government Insurance Plan") decreased to 27.1%. 

So Republicans and Democrats since 1970 have had very different views on the role of Government in Healthcare, but since 1970 their views have largely not diverged greater. 


##### 1d) Another issue: The military 

Spectrum: "1" <- Greatly Decrease defense Spending, "7" <- Greatly Increase defense Spending
"9" <-9. DK; haven't thought much about it

```r
# Military Analysis
load(file="../output/all_Democrats.RData")
load(file="../output/all_Republicans.RData")

militaryDem <- all_Democrats%>%
  select(year, militarySpending2)%>%
  filter(!is.na(militarySpending2))%>%
  group_by(year, militarySpending2)%>%
  summarize(n=n())%>%
  mutate(
    prop=n/sum(n)
  )
```

```
## `summarise()` regrouping output by 'year' (override with `.groups` argument)
```

```r
militaryRep <- all_Republicans%>%
  select(year, militarySpending2)%>%
  filter(!is.na(militarySpending2))%>%
  group_by(year, militarySpending2)%>%
  summarize(n=n())%>%
  mutate(
    prop=n/sum(n)
  )
```

```
## `summarise()` regrouping output by 'year' (override with `.groups` argument)
```

```r
ggplot(militaryRep, aes(x=year, y=prop, fill=militarySpending2))+
  geom_bar(stat = "identity")+
  theme(axis.text.x = element_text(angle = 90))+
  scale_fill_brewer(palette = "Reds")

ggplot(militaryDem, aes(x=year, y=prop, fill=militarySpending2))+
  geom_bar(stat = "identity")+
  theme(axis.text.x = element_text(angle = 90))+
  scale_fill_brewer(palette = "Blues")
```


\includegraphics[width=0.5\linewidth]{Project1_NWilliams_files/figure-latex/unnamed-chunk-7-1} \includegraphics[width=0.5\linewidth]{Project1_NWilliams_files/figure-latex/unnamed-chunk-7-2} 
This question is interesting because it does not focus on domestic issues. Since the recent Iraq War and the war in Afghanistan, did not take place on American soil, ideas surrounding the military can be more abstract to the American populace. This is supported by 14% of Democratic respondents who answered "DK; haven't thought much about it" (2016). 

The topic of military spending is the least polarizing issue in this analysis. In 1980, Democrats and  Republicans most commonly answered "5", and "6" respectively on the scale of 1-7. In 2016, the most common answer for Democrats and Republicans was "4", and  "5" respectively again.

##### 1e) Lastly: Government Aid for Minorities
Spectrum: "1" Government should help minority groups/blacks , "7" <- Minority groups/ blacks should help themselves, 
"9" <-9. DK; haven't thought much about it

```r
load(file="../output/data_use.RData")

`%out%` <- function(a,b) ! a %in% b

govHelpAll <- anes_use%>%
  select(year, party_scale, govHelp)%>%
  filter(!is.na(party_scale)& !is.na(govHelp))%>%
  filter(govHelp %out% c("9. DK; haven't thought much about it"))%>%
   group_by(year, party_scale)%>%
  count(govHelp)%>%
  group_by(year, party_scale)%>%
  mutate(
    Percent = 100 * (n/ sum(n))
  )
save(govHelpAll, file="../output/all_govHelp.RData")
```

```r
load(file="../output/all_govHelp.RData")

years <- c("1970","1980","1990","2000","2010","2016")

# "Poles" are the furtherest respondents of each party, Strong Democrats and Strong Republicans. 

poles<- govHelpAll%>%
  select(year, party_scale, govHelp, Percent)%>%
  filter(as.character(party_scale)%in% c("1. Strong Democrat","7. Strong Republican"))%>%
  filter(as.character(year) %in% years)%>%
  group_by(govHelp, year)

ggplot(poles, aes(govHelp, Percent, fill=party_scale))+
  geom_bar(stat = "identity", position="dodge")+
  facet_wrap(~year, ncol = 1)+
  scale_fill_manual(values=c("blue", "red"))+
  labs(title="How Did Strong Democrats & Republicans Feel About Government\nfor Minorities over the years?")
```

![](Project1_NWilliams_files/figure-latex/unnamed-chunk-9-1.pdf)<!-- --> 
In this question, we see a similar pattern that was since with Democrats and how they felt about abortion. Over time, Democrats felt more and more strongly that abortion should never be forbidden. Republicans did become more conservative as well, but much slower then Democrats became more liberal and polar in their view. 

This phenomenon occured again, but this time, Republicans were the party that became much more steadfast in their opinion on the question at hand. 
As we can see in the above figure, in 1970, 22.5% of Republicans felt that "7. Minority groups/blacks should help themselves", while this is still a substantial proportion of respondents, in 2016, this proportion nearly doubles to **39%!**. After 2000, the proportion of Republicans who feel exactly in the middle between "1. The Government should help minority groups/blacks" and "7. Minority groups/blacks should help themselves" decreased, and respondents increasingly answered more towards 7.

While Republicans' responses are changing, Democrats are remaining nearly constant. In 1970, 24% of respondents answered "4" so right in the middle of the spectrum. In 2016, this remained nearly the same with 25% of respondents answering that way. 

### **IV. Discussion **
From the five issues investigated here, three conclusions can be drawn about political polarization in America. 
-1) Republicans and Democrats are more likely to have diverging views on social issues, as opposed to foreign affairs. 

This conclusion is drawn from results on Military Spending. Democrats and Republicans views on Military Spending do not vary much over time, and do not show major differences across parties. Respondents mostly placed themselves on the middle of the spectrum ( with Republicans mostly answering "5" and Democrats "4"), and this did not largely change from 1980 to 2016. 
One cause of this is that Americans are not "face to face" with military spending in their daily lives, and thus are less informed and/or more apathetic about the issue. 

-2) Democrats and Republicans views on social issues ARE very polar, but this isn't a new phenomenon. 

This conclusion somewhat counteracts the current hype that American politics have never been more polarizing then they are right now. Although there is polarization between Democrats and Republicans, the data reflected from ANES shows that in the 1970 and 1980's there was polarization. This is best supported by the results of "Government Aid for Minorities" and "Healthcare". In the 1970, Democrats and Republicans felt very differently about the role of goverment in health care. While 34% of Democrats supported a Government insurance plan, only 14% of Republicans were in favor (1970). In 2016, the difference had widened, with 27% of democrats and 5% of Republicans supporting a Government insurance plan but it hadn't appeared out of the blue. 

-3) Certain issues polarization certain parties more. 

Democrats' attitudes towards illegal Immigrants warmed significantly, increasing by 31%. Republican's attitudes towards illegal Immigrants did sour, but not as drastically as Democrats warmed. 

Further, Democrats views on abortion became increasingly polarized while Republicans views did as well, but at a much slower rate. In 1972, only 23% of Democrats answered that abortion should never by forbidden, and in 2016 this proportion rose to 63%. Whereas, in 1972, 26% of Republicans answered that abortion should never be forbidden and this number remained nearly the same in 2016, 27%.  


### **V. Limitations and Recommendations **

This analysis is limited in that the respondents may not represent the American populace well. Respondents were paid on a sliding scale for how much of the survey they completed. This may yield data of low quality: answering randomly, not reading questions before answering etc. Future studies could focus on the confidence level of the data. 

Another limitation of this analysis is depth. The analysis of government aid for minorities only reflected respondents that called themselves "Strong" Democrats or Republicans. A deeper analysis could compare how other 'strengths' of Democrat and Republican respondents feel about government aid. 

Finally, this analysis could be enhanced in future studies by comparing changes within the Democratic and Republican Parties over time, i.e. has the proportion of Democrats who describe themselves as "Strong Democrats" increased or decreased. 



