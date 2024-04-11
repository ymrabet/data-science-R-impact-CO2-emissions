# Reduce climate change 

## Framing the Problem:
### Global Warming

Global warming has become a major problem. It endangers our health, national security and threatens other basic human needs. In fact, record high temperatures, rising seas, severe flooding, and droughts—are already increasingly common. This has become a global problem and one of our biggest concerns. 

Did you know that our personal vehicles are a major cause of global warming! In the US alone, nearly one-fifth of all emissions are from cars and trucks. [ucsusa, 07.18.2014](ucsusa.org/resources/car-emissions-global-warming)

<br>
![Photo of air pulotion](https://ohsonline.com/articles/2020/01/15/-/media/OHS/OHS/Images/2020/01/CarPollutionInsideCar.jpg)

*Figure 1: Car Traffic Picture*

### Review of Previous findings
**To reduce the damage and slow the process of global warming, there is a need to look at potential ways to reduce the amount of emissions**. We discovered that Norway is the number 1 user of electric cars, and 9.3% percent of all cars on the road are fully electric. We wanted to look over to our friends in the north and see what they are doing as early adopters in the market of e-cars. [ssb.no, 02.31.2020](https://www.ssb.no/transport-og-reiseliv/artikler-og-publikasjoner/nesten-1-av-10-personbiler-er-en-elbil) [centreforpublicimpact.org, 04.08.2016](https://www.centreforpublicimpact.org/case-study/electric-cars-norway/?gclid=CjwKCAiAn7L-BRBbEiwAl9UtkLUBGPCSSsfzaLoxDLkSSE1OvTGOB_rJNAVn0_epMHlNlUAdouec9RoCEeYQAvD_BwE)

1) Norway has been the second most important market for Tesla Motors (after the United States) in September 2016.
2) Over 99% of the electricity production in mainland Norway is from hydropower plants. 
3) Although Norway is one of the largest producers and exporters of Oil, they remain a leading country in the electrification of its transport sector, having the largest fleet of electric vehicles per capital in the world in the oil industry. [regjeringen.no/, 12.08.2020](https://www.regjeringen.no/en/topics/energy/renewable-energy/id2000124/)
[ssb.no, 02.31.2020](https://www.ssb.no/transport-og-reiseliv/artikler-og-publikasjoner/nesten-1-av-10-personbiler-er-en-elbil)

Electric vehicles (EV), by limitation, are better suited for the city. Long Road trips may be challenging due to the vehicle's low range and limited charging infrastructure. In fact, a trip that was supposed to take 10 hours round trip took 35 hours round trip, where 19 of those were spent at a charging station.

Cars manufactured by Tesla are the exception because it has its own developed charging infrastructure, whereas all the other companies rely on the public/private investors. 

Think about how when Ford invented the model Ford T and automobiles were streamlined and mass sold. Ford didn't have to worry about building the highways or the gas stations to charge the cars; the state/big oil companies took care of that. [Wsj.com,01.2020](https://www.wsj.com/video/series/shifting-to-electric/the-electric-vehicle-road-test/E390D3C3-5437-4E64-B2F9-8AD8598DEE5A)

# Solving the Problem
### Our testable hypothesis:
We believe that Norwegian car manufacturers make an impact on the total C02 emissions by introducing electric cars. 

  **H0: The adaptation of manufacturers to sell electric cars has not led to an increased rate of change in the reduction of C02 emissions.** 

  **Ha: The adaptation of manufacturers to sell electric cars has led to an increased rate of change in the reduction of C02 emissions.**  

### Data Collection
Our dataset gives an overview of the sales of cars in Norway from 2007 to 2016. The dataset includes two tables, monthly sales of new passenger cars by make (manufacturer brand) and monthly summary of top-20 most popular models (by make and model) and a summary stats for car sales in Norway by month. With this data, we can gather information on the Norwegian status and trends that made them the highest consumer per population to choose an e-car. 

Link: 
[kaggle.com02.18.2017](https://www.kaggle.com/dmi3kno/newcarsalesnorway)

To run our analysis, we need to use several packages accessible from the library (*tidyverse, readxl*). We have used the following datasets:

1) A dataset that contains all the manufacturers and how many regular, electric, and hybrid cars they have sold in the years 2007 to 2016 ( dataset: card_sold).

2) A dataset that contains the C02 emission per year by vehicle type  (dataset: Sales_month).

3) A dataset that contains all the manufacturers and their total amount of sales by month and year (dataset: Nor_car).

```{r Installing package, message=FALSE, warning=FALSE, include=FALSE, paged.print=FALSE}
#REMOVE # IN ORDER TO INSTALL PACKAGE
#install.packages("tidyverse")
#install.packages("readxl")
#install.packages("corrplot")
library(tidyverse)
library(readxl)
library(corrplot)
```

```{r Loading Data Files, message=FALSE, warning=FALSE, include=FALSE, paged.print=FALSE}
#Importing files with read_csv
Nor_car<- read_csv("norway_new_car_sales_by_make.csv")
Model_sale<- read_xlsx("norway_new_car_sales_by_model.xlsx")
Sales_month<- read_csv("norway_new_car_sales_by_month.csv")
```

```{r Show Data Tibbles, echo=FALSE, message=FALSE, warning=FALSE}
#Show a small part of the dataset to the reader know what we're working with
print("Nor_car")
slice_head(Nor_car, n=5)
print("Model_sale")
slice_head(Model_sale, n=5)
print("Sales_month")
slice_head(Sales_month, n=5)
```

### Adjustment 
We made two new columns called Electric_Hybrid, Quantity_Bensin, and Total_emissionsin the Sales_month dataset. Electric_Hybrid is a combined quantity of hybrid and electric cars; with this row, we can and easier see which car is more eco-friendly than the others. The Quantity_Bensin column allows us to know the quantity of bensin cars and is needed to compare the growth and decline of bensin cars. The Total_emissions column is showing the overall Co2 mad by Bensin and Diesel cars in total.

We also changed all the NA values in Sales_month and Model_sale to be 0. That way, we can easier make graphs and a more representative model.

```{r table manipulation section, message=FALSE, warning=FALSE, include=FALSE, paged.print=FALSE}
Sales_month["Electric_Hybrid"] = Sales_month$Quantity_Hybrid + Sales_month$Quantity_Electric # added Add a column in Sales_month for total sales. 
Sales_month["Quantity_Bensin"] = Sales_month$Quantity - Sales_month$Quantity_Diesel
Sales_month["Total_emissions"] = Sales_month$Bensin_Co2 + Sales_month$Diesel_Co2

#Replacing null/NA values
Sales_month[is.na(Sales_month)] = 0
Model_sale[is.na(Model_sale)] = 0
```

### New Data Sets

We added a new variable called "cars_sold," where we gathered all the quantity values, such as Quantity_Bensin, Quantity_Diesel, Quantity_Hybrid, and Quantity_Electric to get an overall number of total cars sold.

1) We created table1 by using data from Sales_month. We mutate a new column, "Percent_Change," we did this by including total_CO2_emissions where we did a calculation by taking the total_CO2_emissions divided by total_CO2_emissions, -1, times 100. By making this, we can now see the emission change.

2) Then, we created table2, where we edited the values in the Model_sale dataset to see the total sales of electric cars.

3) We made a data set (electric) due to a full join by table1 and table2. This dataset can now be used to do calculate and show data connected to the electric cars alone.

```{r Code for the new table, include=FALSE}
#Creating new dataset showing quantity by car type
cars_sold <- Sales_month %>% gather(key = category, value = Value, Quantity_Bensin, Quantity_Diesel, Quantity_Hybrid, Quantity_Electric )

# subsetting Sales Month and replace NA values
table1 = Sales_month %>%
          group_by(Year) %>% 
          summarise(total_CO2_emissions = sum(Total_emissions), .groups = "keep") %>% 
          ungroup() %>% 
          mutate(Percent_Change = (total_CO2_emissions/lag(total_CO2_emissions)-1)*100)

table1[is.na(table1)] = 0
         
# subsetting Model Sale and replace NA values
table2 = filter(Model_sale, Model_sale$Type == "Electric") %>% 
         group_by(Year, Make) %>% 
         summarise(total_E_Sold = sum(Quantity), .groups= "keep")

table2[is.na(table2)] = 0

#Join subsetted data table 1 and 2 to create a new data set called "electric", replace NA values where needed. 
electric = full_join(table1,table2, by = "Year")
```

## Key variables
These variables are relevant for the hypotheses:

* Emission

* Rate of change

* Number units sold

* Manufacturer

* Year

We hypothesize that the adaptation of manufacturers to sell electric cars has led to an increased rate of change in the reduction of CO2 emissions. To prove or disprove the hypothesis, we need to look into variables like emissions to see the total amount of Co2 given by cars. The rate of change is essential to know so we can see the change of Co2 over the years while electric cars gradually become bigger. This is where the number of units sold is important; thus, we can see if the increasing number of electric cars are making an impact due to the rise of multiple electric car manufacturers in the field.  We need to know when this is happening; that why the year is an important variable to see if there is a rate over time.

## Exploratory Data Analysis
### General Analysis
#### Descriptive Anlysis 
To know how to work with the data, it is essential to see the data's structure and some descriptive statistics. As an example of how we did the descriptive analysis, we showcase the electric data set. 

Looking at the statistics shown, the one thing that stands out is that columns "Make" and "total_E_Sold" have some NA values. This is because of the join in the previous section. So show the number of cars sold, it would be better to impute these NAs with 0. No changes were made for the other datasets.
<br>
```{r Str() and summary() the Variable selection}
#Str() and summary()
str(electric)
summary(electric)
```
<br>

```{r Electric imputation, message=FALSE, warning=FALSE, include=FALSE, paged.print=FALSE}
#Removing NA values in electric dataset for total_E_Sold
electric["total_E_Sold"][is.na(electric["total_E_Sold"])] <- 0
```

```{r message=FALSE, warning=FALSE, include=FALSE, paged.print=FALSE}
#OTHER DATASETS analysis
str(cars_sold)
summary(cars_sold)
str(Model_sale)
summary(Model_sale)
str(Sales_month)
summary(Sales_month)
```

#### Outlier Analysis
For the boxplot "Outliers in the Cars sold in Electric", we can see that there are no outliers in the dataset and that all the values are representable.

<br>
```{r Boxplot showing outliers for Electric, echo=FALSE, message=FALSE, warning=FALSE, paged.print=FALSE}
#Crating an boxplot vith values of total_E_Sold
Outliers_E <- ggplot(data = electric, aes(x=Year, y=total_E_Sold, group= Year))+
  theme_classic()+
  geom_boxplot()

#Include title, lables, size and colors.
Outliers_E + ggtitle("Outliers in the Cars sold in Electric")+
  xlab("Year") + 
  ylab("Total number of Electric cars")+
  theme(plot.title = element_text(color="Dark Green", size=15, face="bold.italic"))
```

*Figure 2: Boxplot Outliers in the Cars sold in Electric*

### Insight Analysis
**Question 1:** 
<br>
    **What manufacturers are contributing to the sales of electric cars?**

We want to know this question because it is essential to understand how many manufacturers are in the market producing electric cars. This could explain the number of cars sold. 

```{r Stacked bar chart by manufacturer., echo=FALSE, message=FALSE, warning=FALSE, paged.print=FALSE}
#Make a stack chart over manufacturer and their total sale of cars
Model_sale %>% 
  group_by(Type, Make) %>% 
  summarise(cars_sold = sum(Quantity), .groups= "keep")  %>%
  #Show only top 30 manufacturer so it's still readable and organized
  slice_max(cars_sold, n = 30) %>% 
  #Reorder 
  ggplot(aes (x= reorder(Make, desc(cars_sold), function(x) -sum(x)), y= cars_sold))+
  theme_classic()+ #Make the chart look clean
  theme(axis.text.x = element_text(angle = 90, hjust = 1)) + #Fix the manufacturer names
  geom_col(aes(fill = Type))+
  
  #Add different colors to see which value is what
  scale_fill_manual(values = c('forestgreen', 'blue', "black"))+
ggtitle("Types of Cars Sold By Manufacturer")+ #Add title and lab
  ylab("Car Models") + 
  xlab("Number of cars sold")+
  theme(plot.title = element_text(color="Dark Green", size=15, face="bold.italic"))
```

*Figure 3: Types of Cars Sold By Manufacturer*
<br>

Figure 3 shows that there are only four manufacturers that sell electric cars; these are Renault, Tesla, BMW, and Nissan. By those where Nissan is the one leading in the e-cars industry. The majority of the market players offer regular models. Only one manufacturer focuses on hybrid cars. For the most part, sales are not diversified as the majority of the manufacturers are concentrating on one type of car.

**Question 2:** 
<br>
    **How is Electric Performance compared to other car types?** 

We would like to know if there is any growth in electric cars over the year and when is growth started. This is important when we want to see the influence on the co2 emissions
<br>

```{r echo=FALSE, message=FALSE, warning=FALSE, paged.print=FALSE}
#Make a histogram showing the cars bout by quantity over the years
Car_history <- ggplot(data = cars_sold, mapping = aes(fill = category, y = Value, x =  Year)) + geom_col(position = "dodge") +
  theme_classic() +
  
  #Differentiate the different lines with different colors that will be presentable for the category 
  scale_fill_manual(values = c('cornsilk4', 'black', 'forestgreen', "blue"))

# Adjust the chart so it clean and easy to read.   
Car_history + ggtitle("Trend of cars being used")+
  xlab("Year") + 
  ylab("Total number of cars")+
  theme(plot.title = element_text(color="Forest green", size=15, face="bold.italic"))
```
<br>
*Figure 4: Trends of Types of Cars over the years*

We can conclude by looking at Figure 4, that there has been a growth of Electric cars sold from 2011. We can also see a small drop in 2016, but we can assume the trend will continue.

**Question 3:**
<br>
    **Have co2 emissions decreased over time?** 
    
It's important to find out if C02 emissions have decreased over time so that we can compare it to the increase in manufacturers adopting electric cars. 

<br>
```{r The reduction of co2 by cars, echo=FALSE, message=FALSE, warning=FALSE, paged.print=FALSE}
#Make a histogram howing the Avg C02 over the years.  
Avg_Co2 <- ggplot(data=Sales_month, mapping = aes(x=Year, y=Avg_CO2)) +
  theme_classic()+
  geom_col(fill = "black")

#Adjust the graph with adding title, lap and colors
Avg_Co2 + ggtitle("C02 Emissions over the years")+
  xlab("Year") + 
  ylab("Co2")+
  theme(plot.title = element_text(color="Dark Green", size=15, face="bold.italic"))
```
<br>
*Figure 5: C02 Emissions over the years*

As we can see from the histogram of "C02 Emissions over the years", there is a reduction starting in 2008/2009. This was done by looking into average C02 over the years made by all car types. 

<br>
**Question 4:** 
<br>
            **Is the rate of change in the decrease of C02 emissions increasing?**
<br>
We are trying to see if electric cars have led to a change in the rate of change. If there is no change in the rate of change and there is at the same time an increase in electric car sales, then the two are most likely not correlated.

```{r (line graphs), echo=FALSE, message=FALSE, warning=FALSE, paged.print=FALSE}
#Create a line chart about electric cars and precent change over the years
ggplot(data = electric, aes(x = electric$Year, y = electric$Percent_Change)) +
  
#Make the graph presentable and readable for the viewer
  theme_classic()+
  geom_line( )+ 
  ggtitle("Rate of change over the years")+
  xlab("Year") + 
  ylab("Percentage Change")+
  theme(plot.title = element_text(color="Dark Green", size=15, face="bold.italic"))
```
<br>

*Figure 6: Rate of change over the years*

From what we can see in Figure 6, the rate of change is fluctuating. It looked like when electric cars were introduced in 2011, there was an initial increasing rate of change. To get a better picture, let's look at the correlation matrix.

```{r Correlation Matrix, echo=FALSE, message=FALSE, warning=FALSE, paged.print=FALSE}
# Here we are setting the colors according to our color scheme 
col <-
  colorRampPalette(c(
    "blue",
    "forestgreen",
    "cornsilk4",
    "black"
  ))

# Select the right columns (not the categorical one)
correlation <- electric %>%
  select(
    "Year",
    "total_CO2_emissions",
    "Percent_Change",
    "total_E_Sold"
  )

# Define the model to make correlations with method of pearson
Model = cor(correlation, method = "pearson")

# We want to have a good model, so we set a confidence interval
res1 <- cor.mtest(correlation, conf.level = 0.95)

# Creating the code for the Correlation matrix chart
corrplot(
  Model,
  col = col(200), 
  tl.col = "black", 
  tl.srt = 45,
  p.mat = res1$p,
  sig.level = 0.01, 
  type = "upper"
)
```
<br>

*Figure 7: Correlation matrix*

Our correlation matrix in figure 7 shows a strong negative correlation between “Year” and “total C02 emissions,” which is good as the emissions should go down each year. 

There is also a strong positive correlation between Year and Electric cars sold. This means that the demand for electric cars goes up every year. 

However, we do have a strong negative correlation between Electric cars sold and total C02 emissions. This could indicate that there is some relationship between electric cars and emissions. 

We can also state that there are weaker positive relationships in the matrix where there is a green color; however, these are not significant.

## Conclusions 

We can conclude that there are not that many electric car manufacturers. And regardless, the sales of electric cars are still growing and going up. The total of C02 emissions are falling/going down, but the rate of change is flatting, but there seems to be a positive change when electric cars got introduced. 

There is a negative relationship between emissions and e-cars sold, but at the same time, the relationship with the change is not substantial. As shown in figure 5, the emissions were already going down before electric cars were introduced, which makes us question whether this change is actually due to e-cars. 

### Hypothesis Adjustments

As hybrid cars were introduced before electric cars, the green movement actually started before 2011, which could already impacted the co2 emissions. This could explain the decrease that was seen in figure 5. Therefore, we want to test whether the total numbers of electric cars AND hybrid cars sold led to a decrease in c02 measured by the percentage change. 

The hypothesis was changed to: 

  **Ho:** 
  <br>
    **The adaptation by manufacturers to produce electric and hybrid cars has impacted the rate of change in the reduction of C02 emissions **

  **Ha:**
  <br>
    **The adaptation by manufacturers to produce electric and hybrid cars has had no impact on the rate of change in the reduction of C02 emissions** 
<br>

In order to satisfy this hypothesis, a new data set had to be created that included electric and hybrid (Nok_Data).

```{r code new tables}
#Crate a new temporary table that you can filter only Electric and Hybrid
table4 = filter(Model_sale, Model_sale$Type == "Electric" | Model_sale$Type == "Hybrid") %>% 
         group_by(Year, Make) %>% 
         summarise(total_E_and_H_Sold = sum(Quantity), .groups= "keep")

#Crate a new table with a full join of table1 and table4
Nok_Data = full_join(table1,table4, by = "Year")
```

## Modelling

Due to not having many variables in our data sets, we decided to take a different approach. First, we will set significance level: 0.10 (10%) and try five different models.

### Model 1: Checking Assumption
As we have seen from our exploratory data, we assumed that the percentage change in c02 emissions is not influenced by the adoption of electric cars. Our first model checks this assumption.

```{r Model 1}
#Use the lm() formula to see the Percent_Change ~ total_E_Sold in the electric dataset
lm(formula = Percent_Change ~ total_E_Sold, data = electric) %>% 
summary()
```

Looking at the results of *Model 1*:

- Our residuals are somewhat symmetrical. This means that the model doesn't predict any points that are far away from the actually observed points.
- Looking at our t-value, it is very close to 0 (-4.5 at intercept and 0.74 at our independent variable), ideally to reject the null hypothesis, you would want that value as far away from 0 as possible.
- the p-value of the coefficient of total electric cars sold (total_E_Sold) does not meet the set significance level. 
- Our F-statistic p-value (0.47) does not meet the significance level as well. 
- The R squared, and adjusted R squared are also very low, meaning that this model can explain less than 3.08% of the data.

The above observations can let us say we cannot reject the initial null hypothesis with this model, and therefore our assumption was correct. 

Assuming if the model were correct, the coefficient tells us the following: 
The percentage change with each car sold would have a change of 0.00017 in the percentage change. This value is so low it's negligible.

## Model 2: Checking our adjusted hypothesis

Here we adapted the same formula stated in model 1, but then we consider the hybrid cars as well,  hoping to see a positive influence on the dependent variable.

```{r Model 2}
#Use the lm() formula to see the Percent_Change ~ total_E_and_H_Sold in the Nok_Data dataset
lm(formula = Percent_Change ~ total_E_and_H_Sold, data = Nok_Data ) %>% 
summary()
```

Looking at *Model 2*:

- The residuals tell us that its values are less symmetrical than the previous model. So it predicts more values that are far away from the mean.
- The F-statistic's p-value is even higher than in the first model
- Adjusted r squared is still very low, with less than 5% of the data fitting the model
- Considering the p-value of the total numbers of electric AND hybrid cars sold (0.80) and an R square of -0.04, we cannot reject the null hypothesis either.

Assuming if the model were correct, the coefficients tell us the following:
The percentage change with each car sold would have a change of 5.75e-05 in the percentage change. This value is even lower than in our first model.

## Model 3: Impact on total C02 emission
As our first two models prove that our assumptions were wrong, we decided to take it a step further and look into the influence of the electric AND hybrid cars sold on the total co2 emissions.

```{r Model 3}
#Use the lm() formula to see the total_CO2_emissions ~ total_E_and_H_Sold in the Nok_Data dataset
lm(formula = total_CO2_emissions ~ total_E_and_H_Sold, data = Nok_Data ) %>% 
summary()
```

Looking at the results of *Model 3*, we can say that:

- The distribution of the residuals does not appear strongly symmetrical and is skewed to the right, stretching to 608. This means that the model predicts certain values that fall quite far away from the actually observed points.
- The T value is also very high, with 41 at the intercept and probability and value being higher than 2e-16. The coefficient of 0.066 meets a significant level. 
- The adjusted R square for this model is 0.11, meaning that roughly 11 percent of the data fits the model and explained by this model.
- The F-statistic p-value of 0.06 indicates that it is unlikely we will observe a relationship between the predictor and response variables due to chance. The probability of that happening is 6%. 
- All the above points help say that this model is a good candidate for rejecting the null hypothesis considering our previous findings in earlier models.

The coefficients of this model tell us the following:
The total CO2 emissions will have a value of 3236.27 at the intercept. We assume that it represents the gasoline and bensin co2 emissions when no electric/hybrid car is introduced. Besides, the value of the CO2 emission is going to decrease by 0.059 for each car sold. 

### Model 4: Impacting C02 Emission (Multiple variables)
We wanted to check whether adding multiple variables would influence the total emissions. These variables include the percentage change and the number of cars sold.

```{r Model 4}
#Use the lm() formula to see the total_CO2_emissions, total_E_and_H_Sold and Percent_Change in the Nok_Data dataset
lm(formula = total_CO2_emissions ~ total_E_and_H_Sold + Percent_Change , data = Nok_Data ) %>% 
summary()
```

We can say the *Model 4*:

- The residuals distribution is not that symmetrical here either. Meaning that the model predicts values that fall far from the actual observed points.
The addition of a second independent variable (percent change) did not significantly impact the dependent variable as it only improved the adjusted R square to 0.17 and the F-statistic p-value to 0.15 from 0.06 which was better before.
- This means that percent_change does not have any impact on total electric and hybrid cars sold.

Assuming if the model were correct, the coefficients tell us the following:
The total CO2 emissions are going to decrease by 0.058 per car sold. We cannot consider the percent_change into the calculations for this one as it wouldn't make sense since you derive the percent change from the total CO2 emissions. At the intercept, we can assume that the number 3178.58 represents the CO2 emissions of bensin and diesel cars before any electric or hybrid car was sold. 

## Model 5: Brand Influence

Lastly, we wanted to check whether one significant brand has more influence on the total numbers of cars sold, and therefore perhaps contribute to the change in emissions.

```{r Model 5}
#Use the lm() formula to see the total_E_and_H_Sold in the Nok_Data dataset
lm(formula = total_E_and_H_Sold ~ Make , data = Nok_Data ) %>% 
summary()
```

Considering *Model 5*, we can say that:

- The residuals are skewed and asymmetrically distributed. The model is then predicting multiple values that fall far from the actual observed values.
- The t-value at the intercept is at 3.140 and the independent variable is very close to zero. this is a bad thing as the higher the t value, the greater the relationship between the total electric AND hybrid cars sold and the manufacturer. In addition, the p-value of the coefficients are not meeting the significance level. 
- The adjusted R square is the highest one we managed to find so far, with a value of 0.38. This means that 38% of the data fits and can be explained by this model.
- The F-statistic p-value is meeting the significance level. This is great as it means that it is unlikely we will observe a relationship between the predictor (Manufacturer - Make) and response (total electric and hybrid cars sold - total_E_and_H_Sold) variables due to chance.

Assuming if the model were correct, the coefficient tells us the following:
For This model, the intercept's value represents the number of BMW sales, which is 2358. this value would be increased by 816.5 by Nissan, making them the leading company in Electric car sales. Renault would be making the intercept - 1068 car sales. Tesla would be selling 2358 + 416 cars. Lastly, Toyota would be the worst company in this model with a value of 2358 - 1276. 

## Conclusion: 
### Hypothesis Decision

We cannot reject our null hypothesis based on the insights gathered from the modelling process. 

  **Ho:** 
  <br>
    **The adaptation by manufacturers to produce electric and hybrid cars has impacted the rate of change in the reduction of C02 emissions **

  **Ha:**
  <br>
    **The adaptation by manufacturers to produce electric and hybrid cars has had no impact on the rate of change in the reduction of C02 emissions** 
<br>

Although one model was accepted, its values were not good enough to reject the null hypothesis.

Furthermore, even if we assume that the models were correct, the coefficients would still not have an impact because the numbers are so small.


### Insights Gathered 


**1. Market Share Influence**

The number of Electric cars in the market is meager as only four manufacturers produce Electric cars, and 1 produces a Hybrid car up to 2016. This indicates that green cars' production had a low market share compared to other types of cars. 

Our analysis showed that those brands are Early adopters in a market where more and more manufacturers are switching to more efficient and less polluting cars. In the time period of our data, Nissan was the market leader, which was shown in our exploratory analysis and our modeling analysis. The analysis also showed that there was a great increase in the number of these early adopters from 2007 to 2016, and they are going to own a big market share once infrastructure allows for more Electric vehicles and Oil reserves starts depleting, forcing the use of an alternative power source. [ev-volumes, 2020](http://www.ev-volumes.com/country/total-world-plug-in-vehicle-volumes/)

**2. The Green Movement**
From our exploratory data and modeling, it seems like the Green-movement of hybrid and electric cars did not really contribute to the decrease in co2 emissions. This means that there should be another reason why these emissions show a negative trend. According to ucsusa: Over the years, car manufacturers were getting hard critic for their car's pollution and their contribution to global warming. The car industry responded to the critic and started to manufacturer cars with reused Co2 emissions and coming up with cars that were electric and hybrid to satisfy the customer's need. [ucsusa, 07.18.2014](ucsusa.org/resources/car-emissions-global-warming)

**3. Diesel Sales Is Dropping**
One interesting insight when we looked at our analysis is that in 2011, the sales for diesel went down. In fact, many countries after realizing how much pollution Diesel Engines generate(microparticles). The regulations mentioned earlier also apply to Diesel vehicle manufacturers. Thus, even though diesel engines are cleaner as time goes by, the problem is not completely solved as diesel cars are less bought in Norway (according to our dataset). In addition, alternative engines such as hybrid and electric cars were introduced in the market and have been steadily increasing since 2011, although at a slower pace. This could indicate that the population is becoming more eco-friendly in choosing their next means of transportation. Furthermore, we also noticed around the same time, that sales for bensin powered engines skyrocketed. [youmatter, 02.15.2019](https://youmatter.world/en/diesel-or-petrol-what-pollutes-more/)
