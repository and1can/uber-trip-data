
# Uber Trip Data

Welcome to my analysis of Uber trip data from a freedom of information request to NYC's Taxi & Limousine Commission. All data can be found at: https://github.com/fivethirtyeight/uber-tlc-foil-response

There are three questions that I would like to investigate. 

1) Given a date and dispatching_base_number, how many trips will be made that day and how many active vehicles will there be? 

2) Given a date and dispatching_base_number, where should vehicles be deployed? 

3) Are there different types of customers that use a specific dipatching_base_number? Are there different types of customers that use different dispatching_base_number, but should actually be using the same because they are getting picked up in same location? 

# A Closer Look Into Data


```R
# Read CSV into R
MyData <- read.csv(file="Uber-Jan-Feb-FOIL.csv", header=TRUE, sep=",")
head(MyData, n=5)
```


<table>
<thead><tr><th scope=col>dispatching_base_number</th><th scope=col>date</th><th scope=col>active_vehicles</th><th scope=col>trips</th></tr></thead>
<tbody>
	<tr><td>B02512  </td><td>1/1/2015</td><td> 190    </td><td> 1132   </td></tr>
	<tr><td>B02765  </td><td>1/1/2015</td><td> 225    </td><td> 1765   </td></tr>
	<tr><td>B02764  </td><td>1/1/2015</td><td>3427    </td><td>29421   </td></tr>
	<tr><td>B02682  </td><td>1/1/2015</td><td> 945    </td><td> 7679   </td></tr>
	<tr><td>B02617  </td><td>1/1/2015</td><td>1228    </td><td> 9537   </td></tr>
</tbody>
</table>




```R
plot(MyData$date, MyData$trips, xlab='Date', ylab='Trips')
```


![png](output_3_0.png)


# Base Code and Base Name for Uber Bases


```R
unique(MyData['dispatching_base_number'])
```


<table>
<thead><tr><th scope=col>dispatching_base_number</th></tr></thead>
<tbody>
	<tr><td>B02512</td></tr>
	<tr><td>B02765</td></tr>
	<tr><td>B02764</td></tr>
	<tr><td>B02682</td></tr>
	<tr><td>B02617</td></tr>
	<tr><td>B02598</td></tr>
</tbody>
</table>



B02512:	Unter

B02598:	Hinter

B02617:	Weiter

B02682:	Schmecken

B02764:	Danach-NY

B02765:	Grun

B02835:	Dreist

B02836:	Drinnen

We see that the dataset for 2015 does not include all of the Uber Bases. Since on the github page, link on top of notebook lists 8 base names, but our dataset only has 6. 


```R
boxplot(split(MyData$trips,MyData$dispatching_base_number),main='Trips made by Dispatching Base Number')
```


![png](output_8_0.png)



```R
boxplot(split(MyData$active_vehicles,MyData$dispatching_base_number),main='Vehicles available by Dispatching Base Number')
```


![png](output_9_0.png)



```R
JanJune15 <- read.csv(file="uber-trip-data/uber-raw-data-janjune-15.csv", header=TRUE, sep=",")
```


```R

```


```R
head(JanJune15, n = 5)
```


<table>
<thead><tr><th scope=col>Dispatching_base_num</th><th scope=col>Pickup_date</th><th scope=col>Affiliated_base_num</th><th scope=col>locationID</th></tr></thead>
<tbody>
	<tr><td>B02617             </td><td>2015-05-17 09:47:00</td><td>B02617             </td><td>141                </td></tr>
	<tr><td>B02617             </td><td>2015-05-17 09:47:00</td><td>B02617             </td><td> 65                </td></tr>
	<tr><td>B02617             </td><td>2015-05-17 09:47:00</td><td>B02617             </td><td>100                </td></tr>
	<tr><td>B02617             </td><td>2015-05-17 09:47:00</td><td>B02774             </td><td> 80                </td></tr>
	<tr><td>B02617             </td><td>2015-05-17 09:47:00</td><td>B02617             </td><td> 90                </td></tr>
</tbody>
</table>



The interesting thing about JanJune15 file is that the Dipatching_base_num and the Affiliated_base_num need not match. Why that is? I wonder if some base numbers are subsidiaries of other base numbers at one point and then later became subsidiaries of Uber. 

LocationID is important for segmentation question 3 because the locationID is a number that represents a specific taxi region in New York. 


```R
taxiZone <- read.csv(file="uber-trip-data/taxi-zone-lookup.csv", header=TRUE, sep=",")
```


```R
head(taxiZone, n = 10)
```


<table>
<thead><tr><th scope=col>LocationID</th><th scope=col>Borough</th><th scope=col>Zone</th></tr></thead>
<tbody>
	<tr><td> 1                     </td><td>EWR                    </td><td>Newark Airport         </td></tr>
	<tr><td> 2                     </td><td>Queens                 </td><td>Jamaica Bay            </td></tr>
	<tr><td> 3                     </td><td>Bronx                  </td><td>Allerton/Pelham Gardens</td></tr>
	<tr><td> 4                     </td><td>Manhattan              </td><td>Alphabet City          </td></tr>
	<tr><td> 5                     </td><td>Staten Island          </td><td>Arden Heights          </td></tr>
	<tr><td> 6                     </td><td>Staten Island          </td><td>Arrochar/Fort Wadsworth</td></tr>
	<tr><td> 7                     </td><td>Queens                 </td><td>Astoria                </td></tr>
	<tr><td> 8                     </td><td>Queens                 </td><td>Astoria Park           </td></tr>
	<tr><td> 9                     </td><td>Queens                 </td><td>Auburndale             </td></tr>
	<tr><td>10                     </td><td>Queens                 </td><td>Baisley Park           </td></tr>
</tbody>
</table>



TaxiZone contains locationID that maps to a borough: town or district and a zone. This can be used in tandem with JanJune15 locationID to find out where users were getting picked up. 


```R
apr14 <- read.csv(file="uber-trip-data/uber-raw-data-apr14.csv", header=TRUE, sep=",")
```


```R
head(apr14, n = 10)
```


<table>
<thead><tr><th scope=col>Date.Time</th><th scope=col>Lat</th><th scope=col>Lon</th><th scope=col>Base</th></tr></thead>
<tbody>
	<tr><td>4/1/2014 0:11:00</td><td>40.7690         </td><td>-73.9549        </td><td>B02512          </td></tr>
	<tr><td>4/1/2014 0:17:00</td><td>40.7267         </td><td>-74.0345        </td><td>B02512          </td></tr>
	<tr><td>4/1/2014 0:21:00</td><td>40.7316         </td><td>-73.9873        </td><td>B02512          </td></tr>
	<tr><td>4/1/2014 0:28:00</td><td>40.7588         </td><td>-73.9776        </td><td>B02512          </td></tr>
	<tr><td>4/1/2014 0:33:00</td><td>40.7594         </td><td>-73.9722        </td><td>B02512          </td></tr>
	<tr><td>4/1/2014 0:33:00</td><td>40.7383         </td><td>-74.0403        </td><td>B02512          </td></tr>
	<tr><td>4/1/2014 0:39:00</td><td>40.7223         </td><td>-73.9887        </td><td>B02512          </td></tr>
	<tr><td>4/1/2014 0:45:00</td><td>40.7620         </td><td>-73.9790        </td><td>B02512          </td></tr>
	<tr><td>4/1/2014 0:55:00</td><td>40.7524         </td><td>-73.9960        </td><td>B02512          </td></tr>
	<tr><td>4/1/2014 1:01:00</td><td>40.7575         </td><td>-73.9846        </td><td>B02512          </td></tr>
</tbody>
</table>



Here is one of many data files recorded in 2014. All of the data may or may not have the same column titles, but the data is the same. Each file for a specific month in 2014 contains the date and time, latitude, longitude, and base. 

One thing that will be really useful is to that take the longitude and latitude and add a column for the district the longitude and latitude is in and a column for the zone for every entry in the table. 

# 1) Predicting Number of Vehicles and Number of Trips

## Potential Algorithms 

## Linear Regression 

Linear Regression will perform Least Square Method on our data to determine the best fit line. We can do Linear Regression for Number of Vehicles as the dependent variable and use date and time, longitude and latitude, locationID, and base as the independent variable. 

Similarly, we can perform Linear Regression for the number of trips as dependent variable and use the same independent variables as mentioned above. 

Shortcomings: What if the trend is not a straigiht line? Well, then it would not work very well. See below. 


## Polynomial Regression 

We can try polynomial regression to try to get a good fit. Just need to be careful because using a higher degree polynomial will yield better validation accuracy, but may overfit! 

Try k-fold cross validation to ensure that no overfitting has occured. 

## Stepwise Regression

This may be useful because predictors are removed at each step. R-square, t-stats, and AIC metric is used to determine what independent variables are useful enough to keep. This is good if we have too many independent variables. 

Shortcomings: This algorithm may not be to useful because there are not that many dimensions for independent variables. Also, the independent variables seem extremely relevant to me. Thus, I do not expect any of the independent variables to be removed by stepwise regression. 

## Ridge Regression 

Ridge Regression is used when multicollinearity is present (independent variables are highly correlated). 

Ridge regression will reduces the standard error. Will shrink weight results, but will not make them 0. 

## Lasso Regression 

Similar to ridge, except weights can be shrunk to 0. If group of predictors are highly correlated, lasso picks only one of them and shrinks the others to 0. 

## ElasticNet Regression

Hybrid of Lasso and Ridge Regression. Trained with L1 and L2 prior as regularizer. Useful when multiple features are correlated. Encourages group effect in case of highly correlated variables. No limitations on number of selected variables. 

Shortcoming: can suffer with double shrinkage. 

# 2/3) Segmentation 

There are a few segmentation problems to explore with the data. One is to figure out how many different types of Uber customers there are. Another is to find out how many cars each borough and zone needs on a specific day in New York. 

## Potential Algorithms

## Chi-square Automatic Interaction Detector (CHAID) 

CHAID is an algorithm that can help us solve this problem. CHAID is a tool that can help discover relationships betwen variables. CHAID analysis builds a tree and determines how to variables best merge to explain outcome of dependent variable. In our case, our dependent variable will be number of trips and number of vehicles available. Features that we can use to help CHAID determine the tree structure are date and time, locationID, and base. Our resulting tree will have leaves and each leaf will be a specific class (type) of Uber customer that we have. 

CHAID requires continuous data and since our data has half a year's worth of data in 2014 we must clean our data to be suitable for CHAID. One option is to only train on the months that we have data on and only test on months that we have trained our data. A second option is to create artificial data for the missing months (this may be difficult because each month may have its own trends due to holidays). 

CHAID determines splits with Chi Square statistic. 

## Classification and Regression Trees (CART)

Similar to CHAID because it is also about making a tree. However, unlike CHAID, CART nodes split into two and not more. To determine splits, CART uses Gini impurity. 


```R

```
