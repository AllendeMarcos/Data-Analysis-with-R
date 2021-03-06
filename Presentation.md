---
title: "Introduction to R for data analysis"
author: "Marcos Allende lópez"
date: "September 28, 2017"
output: html_document
---

# 1. Introduction

### 1.1. Programming languages 

Wikipedia defines a programming language as "a formal language that specifies a set of instructions that can be used to produce various kinds of output. Programming languages generally consists of instructions for a computer. Programming languages can be used to create programs that implement specific algorithms."

In practice, programming is about creating a piece of code, a software program, to accomplish a task in a computer. Generally, programmers use languages known as "high-level languages", that have strong abstraction from the computer language. Some examples are Java, PHP, C++, Python or R. The election of a specific high-level language depends on the task it will be used for. The codes written in these languages are not understood by the computer directly. They need to be compiled, interpreted or both in order to be understood and run.

### 1.2. What is R?

R is a language and environment for statistical computing and graphics. It is free and commonly used for statistics and data analysis. 

### 1.3. Installation

We are going to work with integrated development environment (IDE) for R called RStudio. The download links are below.

* [R](https://cran.rstudio.com/)

Download R for Windows -> Install R for the first time -> Download R 3.4.2 for Windows

* [RStudio](https://www.rstudio.com/products/rstudio/download/#download)

Click on RStudio 1.0.153-Windows Vista/7/8/10

### 1.4. Graphical user interface

The RStudio IDE consists of 4 panels:

-R Scripts (top left): We can look into the files at the same time we are working (or not) with them.

-R console (bottom left): We can either run files or type arguments that will only be saved on a temporary system.

-Environment and History (top right): The first tab has a record of the objects, functions and values. The second tab records the arguments run.

-Files, plots, packages, help and viewer (down right): The tab "files" displays the directory. The tab ''plots'' displays the graphics. The tab "packages" displays packages allowing you to see which of them are run. The tab ''help'' displays the help when asked for.

### 1.5. Help

Every time we need to know how a variable or object works, we can type:

```{r}
?variable_name
```

For more documentation https://www.rdocumentation.org/, https://Stackoverflow.com or https://Statmethods.net can be useful. Information about courses and first steps guides are also offered at the end of this document.

# 2. Using R

### 2.1. Defining vectors, functions and more

Despite the goal of this presentation is to have an approach to the use of R for data analysis, it is useful to start playing a little bit by defining different types of objects. In R, objects are defined with the arrow <- as follows:

```{r}
a <- 10
b <- 20
c<- "Bank"
d<-37; x<-"Bridge"
typeof(a)
typeof(c)
```

These objects are temporarily stored and can be seen in the window "Environment". We can also combine them or get more information. For instance:

```{r}
w<-a+b
cbind(a,x)
exp(d)
```

### 2.2. Opening external files

To work with external files, the first thing to do is to set the work directory. We can get the current directory by typing:

```{r}
getwd()v
```

Imagine we get something like "C:/Users/marcosal/Documents", but I have my file in a folder in desktop called "R course". Then I type:

```{r}
setwd("C:/Users/marcosal/Desktop/Research/R/R-Course")
```

Now we can open the files if they are in a language that R understands. In order to work with data, let's work with .tsv and .csv files. Once we are in the work directory where the file is, we open our document and save its information as an object at the same time. If we have the document "Document_name.csv" (comma separated values) we can just open it and store its content in a variable called (for instance) "Document1" by:

```{r}
Document1 <- read.csv('Document_name.csv')
```

If it was "Document_name.tsv" (tabulator separated values) we would need to specify:

```{r}
Document1 <- read.csv('Document_name.tsv', sep='\t')
```

Let's play with real data. For this presentation we are providing three documents with data. We are going to open and analyze one of them. Before that, it will be useful and necessary to install some packages:

```{r}
	install.packages('ggplot2', dependencies = T)
	install.packages('ggthemes', dependencies = TRUE)
	install.packages('gridExtra')
	library(ggthemes)
	library(gridExtra)
	library(ggplot2)
```

You don't need to install them every time, it is enough with calling them (last three lines).

# 3. Types of data

At a high level, data can be classified as structured or unstructured. Essentially, structured data is everything that resides in a database and unstructured data is everything else. Some examples of structured data are phone numbers, addresses, names or transactions information. As examples of unstructured data we have text files, image files, audio files or email messages.

It is fair to say that there is one type of intermediate data, the semi-structured data. For instance NoSQL or JSON files. Although not so easy to analyze as structured data, these files have certain structure. 

### 3.1. Unstructured data

For unstructured data to be analyze, it needs to be structured first. The analysis of unstructured data can be seen as the analysis of structured meta-data that is in an unstructured data set.

### 3.1. Reading the file

Let's look at an example. First, let's install the packages we will need:

```{r}
Needed <- c("tm", "SnowballCC", "RColorBrewer", "wordcloud", "biclust", 
    "cluster", "igraph", "fpc")
install.packages(Needed, dependencies = TRUE)

install.packages("Rcampdf", repos = "http://datacube.wu.ac.at/", type = "source")
```

Now, we can download the .txt files we are about to use. They are provided together with this document in a folder called "texts". I have downloaded it and save it in the folder with root "C:/Users/marcosal/Desktop/Research/R/R-Course". Thus, we can store it in an R variable called, for instance, "pltexts":

```{r}
pltexts <- file.path("C:/Users/marcosal/Desktop/Research/R/R-Course", "texts")
pltexts   
dir(pltexts)  
```

Then we load the package for mining text and save the texts in a variable called docs:

```{r}
library(tm)
docs <- VCorpus(DirSource(pltexts))   
summary(docs) 
inspect(docs[3])
```

We can read all the documents at the same time or one by one with the two commands below, respectively:

```{r}
writeLines(as.character(docs))
writeLines(as.character(docs[3]))
```

### 3.2. Cleaning the text

We apply some functions to remove punctuation, numbers, special characters, stop words, spaces and more in order to clean the text before the analysis.

```{r}
# Remove punctuation
docs <- tm_map(docs,removePunctuation)   
writeLines(as.character(docs[3]))

# Remove characters
for (j in seq(docs)) {
    docs[[j]] <- gsub("/", " ", docs[[j]])
    docs[[j]] <- gsub("@", " ", docs[[j]])
    docs[[j]] <- gsub("\\|", " ", docs[[j]])
    docs[[j]] <- gsub("\u2028", " ", docs[[j]])  
}
writeLines(as.character(docs[3]))

# Remove numbers
docs <- tm_map(docs, removeNumbers)    
writeLines(as.character(docs[3]))

# Convert to lower case
docs <- tm_map(docs, tolower)   
docs <- tm_map(docs, PlainTextDocument)
DocsCopy <- docs
writeLines(as.character(docs[3]))

# Remove stopwods words 
stopwords("english")
docs <- tm_map(docs, removeWords, stopwords("english"))   
docs <- tm_map(docs, PlainTextDocument)
writeLines(as.character(docs[3])) 

# Combining words that should stay together
for (j in seq(docs))
{
  docs[[j]] <- gsub("fake news", "fake_news", docs[[j]])
  docs[[j]] <- gsub("inner city", "inner-city", docs[[j]])
  docs[[j]] <- gsub("politically correct", "politically_correct", docs[[j]])
}
docs <- tm_map(docs, PlainTextDocument)
writeLines(as.character(docs[3]))

# Remove white space
docs <- tm_map(docs, stripWhitespace)
writeLines(as.character(docs[3]))

# Preprocess all documents as text documents
docs <- tm_map(docs, PlainTextDocument)
writeLines(as.character(docs[3]))
```

### 3.3. Stage the data

We use the "DocumentTermMatrix" function to construct or coerce to a term-document matrix or a document-term matrix. This step is what gives us a structured data from our unstructured set.

```{r}
# Matrix
dtm <- DocumentTermMatrix(docs)   
dtm  

# Inverse Matrix
tdm <- TermDocumentMatrix(docs)   
tdm  
```

### 3.4. Explore data

We can organize it by frequency:

```{r}
freq <- sort(colSums(as.matrix(dtm)), decreasing=TRUE)  
length(freq)   
ord <- order(freq)   
dtms <- removeSparseTerms(dtm, 0.2) # This makes a matrix that is 20% empty space, maximum.   
ord <- order(freq) 
head(freq, 20)
```

We can also Export to an Excel file:

```{r}
m <- as.matrix(dtm)   
dim(m)  
write.csv(m, file="DocumentTermMatrix.csv")   
```


### 3.5. Plots

Let's plot first a diagram with the words that appear at least 50 times:

```{r}
#install.packages("ggplot2")
library(ggplot2)   
wf <- data.frame(word=names(freq), freq=freq)   
p <- ggplot(subset(wf, freq>50), aes(x = reorder(word, -freq), y = freq)) +
          geom_bar(stat = "identity") + 
          theme(axis.text.x=element_text(angle=45, hjust=1))
p   
```


And plot now a wordcloud:

```{r}
install.packages("RColorBrewer")
library(RColorBrewer)
library(wordcloud)
set.seed(142)   
wordcloud(names(freq), freq, min.freq=100)   
```

# 4. Analyzing data

Now let's assume that we have already converted our unstructured informations into a structured set and we have it saved in a .tsv document that we can open with Excel. We are going to review some of the tools provided by R to extract information from it.

### 4.1. Reading the file

The first file is ''Pseudo_Facebook_data.tsv''. Let's open and save it:

```{r}
getwd()
setwd("C:/Users/marcosal/Desktop/Research/R/R-Course")
list.files()
Example1 <- read.csv('Pseudo_Facebook_data.tsv', sep='\t')
```

Now, let's introduce some functions to view it, know its structure, dimensions and first and last rows. 

```{r}
setwd("C:/Users/marcosal/Desktop/Research/R/R-Course")
list.files()
Example1 <- read.csv('Pseudo_Facebook_data.tsv', sep='\t')
str(Example1)
dim(Example1)
head(Example1)
tail(Example1)
```

### 4.2. One variable analysis: age

Let's focus on the age, and plot a very simple graph to visualize the column ages with the equivalent options qplot and ggplot (one at a time):

```{r}
qplot(data=Example1, x=age)
ggplot(aes(x=age), data = Example1) 
```

Now let's customize the bins (one for each year) and change the axis names and the color for a better analysis and also just because we can.

```{r}
qplot(x=age,data=Example1, binwidth=1, color=I('black'), fill=I('#099DD9'))+scale_x_continuous(breaks=seq(13,120,10))+ 	ylab('Number of people that old')+xlab('Age')
```

Let's, now, split it by gender:

```{r}
qplot(x=age,data=Example1, binwidth=1, color=I('black'), fill=I('#099DD9'))+scale_x_continuous(breaks=seq(13,120,10))+ 	ylab('Number of people that old')+xlab('Age')+facet_wrap(~gender, ncol=1)
```

And get rid of the N.A.

```{r}
qplot(x=age, data=subset(Example1, !is.na(gender)), binwidth=1, color=I('black'), fill=I('#099DD9'))+scale_x_continuous(breaks=seq(13,120,10))+ylab('Number of people that old')+xlab('Age')+facet_wrap(~gender, ncol=1)
```

Now, to compare better between genders, let's switch to a frequency plot and superpose both graphs.

```{r}
qplot(x=age, data=subset(Example1, !is.na(gender)), binwidth=10, geom='freqpoly', color=gender)+scale_x_continuous(breaks=seq(0,120,5)) +ylab('Number of people that old')+xlab('Age')                     
```

And to have a better interpretation, lets normalize:

```{r}
qplot(x=age, y = ..count../sum(..count..), data=subset(Example1, !is.na(gender)), binwidth=10, geom='freqpoly', color=gender)+scale_x_continuous(breaks=seq(0,120,5))+ylab('Number of people that old')+xlab('Age')   
```

Now, let's move to a more statistical interpretation, first with a boxplot that depicts the first percentile, third percentile and median (corresponding to the three horizontal lines of each gender):

```{r}
qplot(x=gender, y=age, data=subset(Example1, !is.na(gender)), geom='boxplot', color=gender)+ylab('Number of people that old')+xlab('Age') 
```

And with numbers:

```{r}
by(Example1$age, Example1$gender, summary)
```

What can we conclude here? 

-We have seen that ages begin and end in the same year for both genders. That is because Facebook doesn't allow to choose an age out of the frame 13-112. 

-We have seen that people from the first histogram that the most of people are between 15 and 30 years old. Then, a second spike is seeing between 48 and 58. Finally, a smaller but weird spike is presented at about 100 years old.

-In the second and third diagram we have seeing that in principle age and gender are not correlated. That is, how old is the people in Facebook does not depend on if we are talking about men or about woman. 

-In the fourth and fifth diagram we can appreciate more differences between men's and women's spectrums. They both have 20 years-old as the mean value (the most repeated), but they have a quite different spectrum for people between 40 and 85. While men seems to be less interested in Facebook as they aged, women seem to have a second frame of interest.

-In the sixth plot we have seen that the difference between the third percentile (the age at where 75% of samples are below) and the first (the age at where 75% of samples are over) is quite bigger for women. This confirms the fact that their interest in Facebook is not so restricted to young people.

-The final table depicts some of the previous arguments turned into numbers.

### 4.3. Two variable analysis: age and number of friends/number of likes

Now, let's introduce a new variable into the analysis: the number of friends. We want to know the number of friends by age and separated by gender.

```{r}
ggplot(aes(x=age, y=friend_count), data=subset(Example1, !is.na(gender)))+geom_point() +scale_x_continuous(breaks=seq(0,2000,10))+ylab('Number of friends')+xlab('Age')+facet_wrap(~gender, ncol=1)
```      

Now, each point corresponds to a person with a number of friends between 0 and 5000 (Y axis) and been between 0 and 120 years-old (X axis). For a better understanding, we can require than only a point is completely black when corresponds to 30 or more people. Otherwise, it will be proportionally dimmed.

```{r}
ggplot(aes(x=age, y=friend_count), data=subset(Example1, !is.na(gender)))+geom_point(alpha=1/40) +scale_x_continuous(breaks=seq(0,2000,10))+ylab('Number of friends')+xlab('Age')+facet_wrap(~gender, ncol=1)
```        

Let's now look at the likes that people receive instead of the friends they have. 

```{r}
ggplot(aes(x=age, y=likes), data=subset(Example1, !is.na(gender)))+geom_point(alpha=1/10) +scale_x_continuous(breaks=seq(0,2000,10))+ylab('Number of likes')+xlab('Age')+facet_wrap(~gender, ncol=1)
```        

What can we conclude here?

-The first plot reveals something we hadn't noted before. A spike when we look at the likes that 66-year-old people receive. We also see weird spikes when we look at early 100-year-old people, that matches with the weird behavior we had noted in the previous section.

-The second plot depicts that maybe some people had laid about their ages saying they were 69 or early 100 when they were indeed young people, because the number of likes they have corresponds to that ages. 

-The third plot endorses the previous assumption, especially for this early 100-year-old people, because again we see a feature in common with young people. 

# 5. Prediction models

Let's try now to make predictions. 

### 5.1. First case: Simple linear regresion

This is the simplest model, data fitted with a linear function. Let's work with data from a gas consumption for a specific temperature. We have some data and we want to predict new values of consume for a given temperature.

First we read and save the document and define the variables.

```{r}
Example2<- read.csv('Gas_consumption.csv')
Temp=Example2[,1]
Consum=Example2[,2]
```

Now we fit the data with a linear model

```{r}
fit=lm(Consum~Temp)
```

And we can predict the consumption for any temperature. Lets do it for temp=3:

```{r}
predict(fit, list(Temp=3), interval="prediction")
```

The lwr (lower) and upr (upper) values corresponds to the uncertainly interval. We can also specify the prediction interval (estimate of an interval in which future observations will fall, with a certain probability, given what has already been observed). Let's say, for example, 80%.

```{r}
predict(fit, list(Temp=3), interval="prediction", level=0.8)
```

We can also plot for a graphic idea:

```{r}
ggplot(Example2,aes(Temp, Consum))+geom_point()+stat_smooth(method="lm")
```

And basically the prediction is telling you where in the blue line you are.

### 5.2. Second case: Bill James' Pythagorean Win Expectation Formula

Let's work now with something more sophisticated. We are going to use data from the package Lahman. The prerequisites are:

```{r}
install.packages("Lahman")
require(Lahman)
require(dplyr)
require(ggplot2)
```

There are 24 data frames in the LehmanData package. We will be using just a few columns (yearID, lgID, teamID, W, L, R and RA) of the data frame Teams. We are going to filter the results for year 2014 and American league. 

```{r}
LahmanData
View(Teams) 
Data <- Teams %>% select(yearID,lgID,teamID,W,L,R,RA) %>%
  filter(yearID==2014,lgID=="AL")
View(Data)
```

Now we implement the prediction with the Bill james's Pythagorean win Expectation Formula (more info https://en.wikipedia.org/wiki/Pythagorean_expectation). Basically, it is about dividing the squared runs (R) by the sum of the squared runs and squared runs allowed (RA). This gives you a coefficient to multiply by the total number of games, which is 162, to get the expected number of wins. Finally, we are interested in the difference between our prediction and the real number of wins. We add a line in our code with the function "mutate" to add to the table three new columns with the previous info.

```{r}
LahmanData
View(Teams) 
Data <- Teams %>% select(yearID,lgID,teamID,W,L,R,RA) %>%
  filter(yearID==2014,lgID=="AL") %>%
  mutate(PyExpFor=R^2/(R^2+RA^2),ExpWins=round(PyExpFor*162),diff=W-ExpWins)
View(Data)
```

Let's plot now wins versus expected wins and apply the linear regression. 
```{r}
ggplot(Data,aes(ExpWins,W))+geom_point()+stat_smooth(method="lm")
```

To have a measure of how good it is, again we can see at the R-squared method (https://en.wikipedia.org/wiki/Coefficient_of_determination).

```{r}
mod <- lm(W~ExpWins,data=Data)
summary(mod)
```


# 6. References

Coursera and Udacity offer interesting courses. The data files for this presentation have been taken from the Udacity course:

https://www.coursera.org/specializations/r?utm_source=gg&utm_medium=sem&campaignid=313639027&adgroupid=40011621568&device=c&keyword=%2Blearn%20%2Br&matchtype=b&network=g&devicemodel=&adpostion=1t1&creativeid=192658660550&hide_mobile_promo&gclid=Cj0KCQjwx8fOBRD7ARIsAPVq-NnYGrFLEnLkt2H_84DFmqjP76_6Mg38vCOL-4fSn_N6XlJD5xb86gQaAr_GEALw_wcB

https://classroom.udacity.com/courses/ud651

There is a very clear first steps guide here:

https://cran.r-project.org/doc/contrib/rdebuts_es.pdf

More related to the bank, Marta Alonso Fernandez and Jose Luis Delgado Davara provided a very recommendable Tutorial to access and analyze data from the Bank's open portal.

https://github.com/EL-BID/Libreria-R-Numeros-para-el-Desarrollo/blob/master/Tutorial/Tutorial.Rmd

https://www.youtube.com/watch?v=ThyJ8siKs4o

The example for data mining in the third section has been extracted from this great and highly recommendable post:

https://rstudio-pubs-static.s3.amazonaws.com/265713_cbef910aee7642dc8b62996e38d2825d.html

The two last examples are inspired by this two videos from youtube:

https://www.youtube.com/watch?v=rQosPYLDZpg

https://www.youtube.com/watch?v=AQ0irl54umY

And, you can find whatever else here: 

https://www.google.com


