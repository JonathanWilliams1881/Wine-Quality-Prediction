# Wine Quality Prediction
### Prediction of Wine Quality with Random Forest

***
### Introduction 
##### When life gives you lemons you make lemonade right? How about when life gives you grapes? As for me, I wouldn't just make grape juice. This project is all about **wine**! We all want to know what are the distinguishing factors for a good wine tasting event, so without any further ado... let's get into it.

![#finewines #goodtimes](https://user-images.githubusercontent.com/32421511/56669703-9056d400-667f-11e9-9a2d-11ae7d09b42d.jpg)

#### About the Dataset...
The data used for the prediction of wine quality consists of two separate datasets. One of which is for white wines and the other is for red wines. You can find both of these datasets as well as descriptions of their contents by following this link <https://archive.ics.uci.edu/ml/datasets/Wine+Quality>

***

### Business Problem: Predict the Wine Quality
##### The brief description of the datasets tell us that these observations come from samples of the Portugeuse wine brand "Vinho Verde". In short, the chemical contents of the wines have been recorded and we have been assigned the task of finding a model that can predict the quality of the wines with some degree or measure of accuracy. 

#### Concerns: "All input variables may not be relevant", "There are much more normal wines than excellent or poor ones."
***

### Project Approach
##### I used a combination of Power BI and R to quickly examine possible nuiances in the data and to obtain descriptive stats about the distribution of the dependent variable *quality*.

![Wine Dashboard-page-001](https://user-images.githubusercontent.com/32421511/56670362-b2048b00-6680-11e9-8b5f-882f820b8d5b.jpg)

```{r message=FALSE, warning=FALSE}
#### Import Libraries ####
library(data.table)
library(dplyr)

#### Import Wine Datasets ####
white <- read.csv2(file = "C:/Users/jwill/Downloads/winequality-white.csv", header = T,
                   dec = ".")
red <- read.csv(file = "C:/Users/jwill/Downloads/winequality-red.csv", header = T)

# Combine Red and White
wine.table <- data.table(rbind(white, red))

#### Data Viz & Exploration ####
library(ggplot2)
library(ggcorrplot)

# Make a Correlation Matrix
corr <- matrix(round(cor(wine.table), 2), nrow = 12,
               ncol = 12)
colnames(corr) <- names(wine.table)
rownames(corr) <- names(wine.table)
p1 <- ggcorrplot(corr, hc.order = T, outline.color = "white",
                 lab = TRUE, lab_size = 1.8)

p1

# Melt prep for variable distributions
library(reshape2)
wine.table.melt <- melt(wine.table)

# Variable Distributions chart
p2 <- ggplot(data = wine.table.melt, aes(x = value)) +
  stat_density() +
  facet_wrap(~variable, scales = "free") +
  xlab("Distributions") + ylab("Density")

p2
```
![Wine Dashboard-page-008](https://user-images.githubusercontent.com/32421511/56671864-5c7dad80-6683-11e9-8383-1400f6c4f1e7.jpg)

#### About the Quality Variable...
##### It is worth mentioning that the distribution of the *quality* variable is like an integer. Thus, when making predictions with the models we may choose to round off the predicted values in practice. For example: A prediction of a 5.73 should be treated as a quality of 6.

#### Data Cleaning
##### Data cleaning was minimal because there were no missing values or other notable nuiances, so I was able to proceed to the modeling process.
***

### Modeling

![Wine Dashboard-page-002](https://user-images.githubusercontent.com/32421511/56670463-e415ed00-6680-11e9-9ad8-e8e26e5fcc6d.jpg)

##### As the above image indicates, the model that worked best for predicting wine quality was a **Random Forest Regression Model**. The Random Forest model outperformed against the competing **Support Vector Regression** and **Decision Tree Regression**.

***

## WAAAAAIIIIT WHATTT!!!??? 
Feel free to skip ahead if you are comfortable in your understanding of SVR, DTrees, and RForest.

#### Modeling for the Non-Techies

##### Trying to predict the quality of wines is almost like trying to classify a bag of skittles. Consider the following table:

Variable to Predict| Description of Possible Values
--------------|---------
Wine Quality | Can take distinct values from 0 to 10
Skittle Flavor| Can take a distinct **flavor** (however many).

##### Let's assume there are 10 possible flavors of skittles. In that case, we can assign a distinct number to each flavor... 
##### ex: 1 = grape, 2 = orange, etc...

##### OK great! Now consider all the skittles sprawled out on a table like the image above. Imagine that we had to separate each of the flavors using only a knife... How would you do it? And how would you decide which knife split to do first?

##### Intuitively we would decide to split them by color, which is the most telling factor for skittles. And as for which place to split from, it makes sense to make a split where the biggest group of a distinct skittle flavor can be identified. 

##### This is pretty much how a single Decision Tree works. It takes some input variables (like "color" or "size" for the skittle example) and creates *splits* based on defining instances of the input variables. In the end, these decisions drive predictions made for the dependent variable. The image below should give an idea of how that works...


### Decision Tree Example
![Wine Dashboard-page-003](https://user-images.githubusercontent.com/32421511/56670567-11fb3180-6681-11e9-88fe-5cccc2d5695f.jpg)

##### There is a Decision Tree on the right which corresponds to the graph. The easiest way to read the tree is to start from the end, which are often called the leaves and work your way up. The leaves are the four different predicted values of wine quality based on the alcohol content variable. 

##### The tree reads: 
* "If alcohol is less than 9.9, wine quality is 5.4."
* "If alcohol is greater than 9.9, but less than 11, wine quality is 5.7." And so on...

###### Note: One of the main issues for the Decision Tree here is that it struggles to catch more extreme values. This was also true for SVR, but not so for Random Forest!

***

## Into the Forest

![Wine Dashboard-page-004](https://user-images.githubusercontent.com/32421511/56670718-58e92700-6681-11e9-9f3a-1c7974040681.jpg)

##### What is a forest except... you guessed it... a bunch of trees! The above image shows how the Random FOrest Model performed better than the Decision Tree model and it's easy to see why.

##### Random Forest is basically the process of producing hundreds of Decision Trees and then averaging out the results of each tree. Remember the darts/bullseye? The chances of consitently getting close to the bullseye are much greater when you can throw the dart 500 times as opposed to once. 

##### Our results show that the Random Forest model has a smaller Mean (Average) Squared Error than the Decision Tree model. Also worth mentioning is that the Random Forest model narrowed our list of useful variables down to 4 from 11 predictors. They are shown above.

***

## Random Forest Model Accuracy

![Wine Dashboard-page-005](https://user-images.githubusercontent.com/32421511/56670836-8635d500-6681-11e9-83f2-99692544a23e.jpg)

#### It's a good sign that of the 2166 predictions made, most of the Squared Error values are close to 0.

![Wine Dashboard-page-006](https://user-images.githubusercontent.com/32421511/56670878-9cdc2c00-6681-11e9-9eb2-dce96fbf72a0.jpg)

#### This chart shows how many predictions have a Squared Error greater than or equal to 1. 
#### A Squared Error of 1 or more means that the prediction of wine quality was off by 1 point or more. 
#### Only about 11% of the observations were off by 1 point or more. 

![Wine Dashboard-page-007](https://user-images.githubusercontent.com/32421511/56670917-b2515600-6681-11e9-979f-6d8e9faddeef.jpg)

#### This chart shows how many predictions have a Squared Error greater than or equal to 4. 
#### A Squared Error of 4 or more means that the prediction of wine quality was off by 2 points or more. 
#### Only about 1% of the observations were off by 2 points or more.

***

## Conclusions
#### Putting this model into production could serve both wineries and buyers of wines for the "Vinho Verde" brand. Those that produce wines could use the model as a template for judgment of the quality of "Vinho Verde" wines. 

#### Accurate measurements of quality lead to more accurate pricing on both the buy and sell side... whoever catches on first.

***

## Q&A
#### **Does the model translate well into other brands of wines?** We don't know for sure because it's likely that different brands of wine have different chemical components. 
#### **The model doesn't produce integer values like the dependent variable "quality". Isn't it supposed to?** No. The model is supposed to produce close estimates of the integer values. From there stakeholders can make a quick judgement call by simply rounding up or down to the nearest integer with little worry of error. 

#### More questions are welcome!

## Hope you enjoyed! Cheers!

![Cheers](https://user-images.githubusercontent.com/32421511/56670968-c5642600-6681-11e9-998c-438652b77b01.jpg)



