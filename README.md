Bayes Impact Hackathon Project 2014
====

This is a one-day project completed as part of the Bayes Impact social impact hackathon.: http://bayeshack.challengepost.com/submissions/30299-predicting-wage-violations

## Background on the Issue

Wage theft is a multi-billion dollar issue for low-income populations in the US. For low-income workers, lost wages can dip them further into poverty and limit their financial resources to access housing, health care and other key services, which in turn perpetuates the cycle of poverty. A 2009 study of 4,000 low income workers in New York City, Los Angeles and Chicago found that these workers lost on average $2,634 annually due to workplace violations. This was out of an average income of $17,616, which translates into a loss of 15 percent of income. Extrapolating from these figures, low wage workers in these three cities alone lost more than $2.9 billion due to employment and labor law violations. Enforcement and identification of wage violators is particularly difficult for the Department of Labor given its workforce of ~1,000 investigators nationwide.

You can learn more about the problem in this 3-minute video: https://www.youtube.com/watch?v=SBSLF6Inb-k

## Problem

Can we identify the highest priority industry & county combinations for the Department of Labor to proactively and most efficiently investigate potential wage violators with its limited resources?

## Visualization and Methodology

Utilizing a Department of Labor Wage and Hour Compliance Action Data Set from 1989 - 2013, we predicted Net Monetary Impact (Total Backwages Due + Total Civil Monetary Penalties) by a Industry and County level view. The Net Monetary Impact gives us a measure of relative riskiness for each county and industry.

We created a data visualization using d3.js which indicates the largest NMI/probable wage violators by county and industry with dark red being the highest risk. We have mapped this by industry so that it is possible to identify the highest risk counties by industry. Our 2014 and 2015 data view is predictive in nature and additionally we have included descriptive mapping of 2000 - 2013 data.

Visualization can be found here: http://askeys.github.io/bayes_impact_hackathon_2014/index.html

## Methodology:

Calculate Net Monetary Impact (Total Backwages + Total Civil Monetary Penalties) for each wage investigation
For each particular investigation evenly distribute the NMI across the years the NMI was incurred
Split data by State + Industry
NMI split by Year for each Company
Accumulate by State + Industry
For a given State + Industry, use Time Series Analysis to Forecast NMI for next year (2015)
Distribute the predicted NMI of a State to Counties by weighting % of working population
Map Forecasted NMI
Key Predictions

Our visualization identifies investigation areas in further detail by the county level, but we found the following industries (aggregated on the state level) have the highest predicted NMIs:

* Construction in CA and FL
* Waste Management in NY, CA, MD and FL
* Transportation and Warehousing in CA
* Manufacturing in CA
* Health Care and Social Assistance in FL
* Oil & Gas Extraction in TX

## Potential Future Analyses

* Future analysis on the employer level
* Explore additional data to better understand the nature of the risks
* For example is the risk related to underpayment, medical screening violations, discrimination or other?
* When violations occur
* For example, do wage violations increase during recessions or after natural disasters?


Presentation

Can be found at: http://www.slideshare.net/scfernandez/bayes-hack-wage-presentationeditted
