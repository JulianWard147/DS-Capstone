# DS-Capstone-Twitter-Misinformation

## The Problem

This project began, more or less, on the one year anniversary of the January 6th riots/insurrection. An often discussed factor in this event is the role played by social media in radicalizing those who attacked the Capitol building. In the wake of those events, Twitter moved rapidly to suspend many accounts associated with election misinformation.

This project is looking to explore how NLP tools can assist moderation of online spaces b y helping to identify potentially dangerous tweets. There are some questions here about how Twitter might use this information, but this model aims only to identify tweets.

## The Data

In order to train a model to recognize misinformation, the model must first be given tweets that contain misinformation. For that this project relies on the work of VoterFraud2020, a collaboration between the [Social Technologies Lab](https://s.tech.cornell.edu/) at Cornell Tech and The Technion. The VoterFraud group analyzed networks and communities of retweeters and other misinformation promoters to classify several communities of people tweeting about the 2020 election. Early attempts were made to compare this data to other Twitter datasets, but as testing progressed, I became worried that the model was merely able to tell a political tweet from a non-political tweet.

In order to increase the model’s ability to pick out misinformation from other political discussion, the model was trained on information from [the Harvard Dataverse](https://doi.org/10.7910/DVN/PDI7IN). These tweets relate to the 2016 election, and thus allows the model to train on rhetorically charged political discussions, often including the same people or places, but which predates the 2020 election misinformation claims.

These two databases provided their information as ‘dehydrated’ tweet ID’s. A major challenge of this model construction came in the form of ‘rehydrating’ these tweets. Twitter API’s put a limit on the number of queries they’ll allow in a given time period.  The notebooks 'Pulling-from-Twitter.ipynb' and 'Pulling-from-Twitter-Election2016.ipynb' contain the cells used to generate the two csv files 'hydrated_tweets' (the misinformation tweets) and 'tweets2016' (the tweets from 2016). These files represent a subset of a subset of the larger dataset available. The original datasets that I used to pull the tweet ids from are too large to store in git, but can be found through the links provided.

The method of data collection did lead to some complications in model building. Text from Tweet ids that belonged to users with deleted or suspended accounts won’t be returned. The VoterFraud2020 database had a row to help filter out suspended or deleted accounts, but if a user became inactive after that database was last updated will still return blank data. Additionally, the fact that the VoterFraud2020 dataset was collected through network analysis led to the disproportionate appearance of links and retweets. Some versions of the model during model building (particularly the TFIDF versions) predicted that a tweet that merely contained 'https' and 'com' was 86% likely to be a 'misinformation' tweet. Obviously, this was a result of an artifact of the way that data was put together, not anything to do with the semantic content of the tweet. Although this did get dealt with (see the section on model building below) this stands as a reminder that any model will only be as good as the data fed into it.  This will particularly become a problem if the model is used to make decisions about moderation, and then decisions about moderation are used to train the model. At that point the model is just recursively reinscribing itself and any quirks it has.

## Model Building

The whole process of iterative model building can be found in the notebook ‘iterative_model_building.ipynb’ as well as the notebook ‘additional_iterative_modeling.ipynb’.

The actual model building part of this project went fairly smoothly. Even default options with various classifiers and vectorizers were able to score about 98% accuracy. I did some GridSearch to try to optimize parameters. While I was sorting out the errors that the models did have I noticed the preponderance of links and retweets in false positives across several of my models. I did some testing, and found that adding ‘rt’, ‘com’, and ‘http’ to the list of stopwords helped several models perform better. 

For simplicity's sake I created a new notebook to rerun the optimization process with the new stopwords as an option. This is the ‘additional_iterative_modeling.ipynb’ notebook. 

Ultimately I chose to use the following vectorizer and classifier for my model:

NLTK Count Vectorizer with my slightly modified stopwords list, otherwise default. 
Random Forest classifier with 500 estimators, otherwise default.  

I chose this model for two main reasons. First, it scored the highest on accuracy metrics. Second, it did so without relying on the strings related to links and retweets. Other classifiers scored better without those stopwords, leading me to worry that part of their performance was coming from picking up on that artifact of data collection. 

The arguments against this model is that it does take more computational power to run and will take longer to adjust. It does take several times longer to run this classifier than the next best competitor (Multinomial Naive Bayes), and it gains only marginally in accuracy. If this product were to be brought to a larger scale or more time sensitive, it would be possible that another model might be more appropriate. Ultimately I chose to stick with this model on the assumption that our hypothetical client, Twitter, would have access to ample computing power. Still, the door is open to a more lightweight model, but that might increase demands on human employees. 

## Results and Analysis
The model performs very well.

Insert confusion matrix and roc-auc curve.

Even given my reservations about quirks in data collection, it’s hard to look at 13 false positives and 98 false negatives in a validation set over of over 18000 tweets as anything other than a great success. Additionally, when looking into the specifics of the false negatives and the false positives we can see how the model is processing. For instance, let’s look at this false positive from the 2016 election, which the model is pretty sure about: 

“RT @Bakari_Sellers: But but voter fraud… https://t.co/op9LlzNoVF”

So there’s some interesting things going on here. For one, once the vectorizer strips out the stopwords and punctuation, the model basically just sees ‘voter’ and  ‘fraud’, so it is hard to fault it for flagging this as having something to do with voter fraud conspiracies.  Arguably this is the model correctly picking up on the misinformation that was floating about the 2016 election that only later coalesced into the 2020 conspiracies. 

That said, I can’t really tell whether or not this tweet is promoting or contesting claims about voter fraud. I’d have to look at the surrounding context and see where that link leads. 

Likewise false negatives (a relatively less problematic error than the false positives) mostly result from the model not knowing where a link leads. A lot of these are tweets with mundane content but links that presumably lead to misinformation. We can look at this as an illustrative example of the limitations of this model as far as moderation goes. 


## Next Steps, Recommendations, and Caveats

Deciding where, when and how much human intervention gets balanced against the judgment of the model in the moderation process should be something our hypothetical client considers. I think this project shows that, even given a relatively small sample of problematic tweets, it is not hard to create a model that can pick out similar problems. This sort of model could be a great aid for moderators looking to contain the spread of a particularly worrying trend. 

I feel reasonably confident that a similar process could be made to address, e.g. COVID denialism, or could be spun up to react to some new problem in the future. Given Twitter’s resources, it would also be fairly easy to periodically update the model with new data, or tweak it as human moderation finds holes or quirks that I haven’t yet.  

That said, there are some barriers to expanding this product. One would be the problem of the initial dataset. I was able to lean on the work of the VoterFraud2020 data, but that was collected after the problem had run out of control. This model might help, for instance, in November 2022, but it may be too late to act on this particular issue. Is the metaphorical cat is out of the bag? A model like this would be most useful if a training dataset could be quickly assembled by moderation teams using other methods of finding problematic tweets. Then, if a pattern of rapidly spreading misinformation is found a model could be deployed.

Additionally, there are business and political ramifications to moderation that a data model won’t be able to answer. What is the cost of banning a sitting president? The model can’t help there. 


**Link to presentation**

## Repository Structure
```
├── [data]
├── [images]
├── .gitignore
├── README.md
└── notebook.ipynb
```


## Citations

A. Abilov, Y. Hua, H. Matatov, O. Amir and M. Naaman. (2021). VoterFraud2020: a Multi-modal Dataset of Election Fraud Claims on Twitter. To Appear, International Conference on Web and Social Media (ICWSM 2021).

Littman, Justin; Wrubel, Laura; Kerchner, Daniel, 2016, "2016 United States Presidential Election Tweet Ids", https://doi.org/10.7910/DVN/PDI7IN, Harvard Dataverse, V3

