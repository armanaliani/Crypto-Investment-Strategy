# Overview
---

Our goal is to identify good investment opportunities in cryptocurrency tokens. We're analying Ethereum blockchain data over the past year to build a classifier/recommender that works towards a viable investment strategy. 

With crypto tokens being a newer and unregulated asset class built on decentralization, information is scarce unlike public stocks. There have been huge opportunities for growth and some have found financial freedom through these investments. However far more have found themselves victem to scams and poor investment decisions. Few have found themselves able to recreate their success, many claim their success is due to insider trading and market making tactics, they claim they have put in the work and found market signals. Our goal is to shed light on which claims are more likely.


## The Process
---
Here you can find the working notebooks in order from start to finish in this process:

#### Data Scraping
1.**[Discord Scrape](./dataScrape/discord_scrape_step_1.ipynb)** <br />
2.**[Discord Dataframe](./dataScrape/discord_dataframe_step_2.ipynb)** <br />
3.**[Dune Scrape](./dataScrape/dune_scrape_step_3.ipynb)** <br />
4.**[Defined Scrape](./dataScrape/defined_scrape_step_4.ipynb)** <br />

#### Data Cleaning and EDA
5.**[Data Cleaning](./Research/tokens_cleaning.ipynb)** <br />
    5.a **[Volume Scrape](./dataScrape/defined_volume_scrape.ipynb)** <br />
    5.b **[Pair Extraction](./dataScrape/pair_extraction.ipynb)** <br />
6.**[Exploratory Data Analysis](./Research/data_eda.ipynb)** <br />

#### Models
7.**[Base Modelling](./Models/base_modelling.ipynb)** <br />
8.**[Base Modelling](./Models/neural_network.ipynb)** <br />


## Data
---

Our Data comes from multiple sources:
1. A private discord feed with liquidity lock token alerts, we have pulled tokens at their liquidity lock snapshot: discord channel belonging to => [consortiumkey](https://consortiumkey.com/)
2. Dune API, using the token address, the all time high price and time of occurance was pulled => [here](https://dune.com/home)
3. Defined API, using the token address, the total supply was pulled to calculate all time high marketcap, as well as volume metrics at these prices => [here](https://docs.defined.fi/reference/overview)
*Stretch*
4. using Defined API in tandem with occurance of all time high price from Dune to look at activity metrics at that time, volume, buys, sells, etc.


*Limitations*
1. Discussing with investors in the space, their number 1 strategy is often to simply copy other successful traders. While wallet data is public it is not historic
2. Token contract; a checksum function compares the contract to all others to test for uniqueness, this is NOT a historic measure and cannot be used for the data over the past year

Data can be viewed here:[Google Drive](https://docs.google.com/spreadsheets/d/17YEDsqSk0fiZbUbFbI8fp1uJzZtLcMBzmmhlVvK30nM/edit#gid=1451416529)


## Data Analysis Overview
---
For our target class, we have made the following assumptions:
- At least 1 day between liquidity alert and all time high to filter out pump and dumps
- At least $10,000 in volume on the day of all-time-high price as well as the day before and day after
  - This will filter out inflated growth due to liquidity being pulled
- A minimum growth of 15

Our target class makes up 1.1% of our overall data. This is a severe class imbalance that reflects the true nature of the crypto space.

As is stands our trading strategy is based on many conservative assumptions:
- We assume a non target class investment results in a 100% loss
    - In the real world, while many may be scams there are still growth opportunities between 2-15x, or even breakeven
- We assume target class investments cash out at 15x
    - While the average growth within our target class is greater than 15, we have not created a selling/take profit strategy, and it would be dishonest to assume that we could sell the abosulte peak of every toke, every time.
- Gas fees, liquidity, and transaction limits are factors in modelling, but not factored when evaluation profitability
    - When the selling strategy is created, these must be accounted for
 
Creating specific trading strategies requires extensive data, which will likely include scraping the blockchain itself. As it stands now we have a snapshot of the beginning and peak of each token, but to truly create a thorough strategy we need all the trading volume and moves inbetween. 

Before we invest such resouces in aquiring this data, we need a baseline case to determine if this asset class of crypto is profitable at all.

As of now, our goal is to see a model with a precision of at least 6.7%, this is our breakeven number.


## Base Models
---
We know given our low correlations we will need a complex model to pick up on subtle patterns and trends in our data.
For a benchmark of this problem, were going to observe how various models perform before attempting a Neural Network.

1.Base Logistic Regression: 
- 99% accuracy ~ misleading given that our dataset is already at a 99:1 ratio
- Assumes all data is of non-target class
- Precision and Recall of 0 for the target class

2.Weighted Logistic Regression:
- Recall of 31% of target class cases 
- Precision of 1.2% -- marginal improvement on random guessing

3.SVM:
- Recall of 30% of target class cases 
- Precision of 1.2%

4.Random Forest:
- Recall of 1% of target class cases -- drop in recall
- Precision of 5.2% -- improved precision, still not profitable

5.Optimized Random Forest:
- Recall of 3% of target class cases
- Precision of 7.5% -- improved precision, finally profitable

## Advanced Models
---

We saw that most of our base models did not perform well in terms of precision of the target class.

Neural Networks are a powerful machine learning tool, we expect them to outperform the base models in our use case.

We saw that we had extremely low coefficients with our target class in our eda so we already knew we needed an in depth and powerful model. Neural Networks are great at capturing complex and non-linear relationships. However, with their power comes a slight drawback, the element of random chance. 

We've taken this into account, and made precautions to prevent both overfitting and a one off "lucky" successful model.

While conducting a test/train split is a good method in ensuring a model performs well with unseen data, we took extra precaution. We tested our models both on raw data as well as pca data and ensured the results were within a reasonable range. A good model should detect the underlying pattern in any representation of the same data. PCA helps mitigate collinearity within our data as well as overfitting to the raw train data.

We also took an aggregate measure of our model evaluations. Each models architecure was trained multiple times on multiple variations of data. Every single run, for every single model architecure regardless if it was pca data or raw data, was profitable at one or more thresholds. This validates all of our model architures, and suggests in a real world setting we can expect results within our range of results. Therefore we can rule out profitability being due to luck and overfitting. 

Here are the average scores for each model:

6.Base Neural Network Architure (0.75 threshold):
- Recall 5% of target class cases
- Precision of 8.9%
  
7.Neural Network Architecture 2 (0.8 threshold):
- Recall 5.5% of target class cases 
- Precision of 9%

8.Complex Neural Network Architecure (0.8 threshold):
- Recall of 8.5% of target class cases
- Precision of 8%

In our most profitable individual models, we saw scores such as:
- Precision 15% & Recall 4%
- Precision 11% & Recall 7%
Such scores had the highest profit calculated


## Next Steps
---
Now that we've established a profitable trading strategy is possible. We can invest our efforts into deeper analysis. 
Our current data shows a snapshot of the beginning and peak of the token lifecycle, but not the moves in between.
To break out of our assumptions set earlier, we'll need this information.
With this information, we will be able to:
- Create tiered target class
- Identify trading patterns of failing tokens to minimize loss
- Identify trading patterns of growing tokens to hold off on profit taking at 15x
- Create a thorough Sell strategy to maximize the buy strategy of our current model


## To-Do List
- [ ] Find Trading Data
- [ ] Introduce Reinforcement learning
- [ ] Automate Pipeline
