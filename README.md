# DS-Capstone-Twitter-Misinformation

## The Problem

This project began, more or less, on the one year anniversary of the January 6th riots/insurrection. An often discussed factor in this event is the role played by social media in radicalizing those who attacked the Capitol building. In the wake of those events, Twitter moved rapidly to suspend many accounts associated with election misinformation. 

This project is looking to explore how NLP tools can assist moderation of online spaces b y helping to identify potentially dangerous tweets. There are some questions here about how Twitter might use this information, but this model aims only to identify tweets.

## The Data

In order to train a model to recognize misinformation, the model must first be given tweets that contain misinformation. For that this project relies on the work of VoterFraud2020, a collaboration between the Social Technologies Lab[https://s.tech.cornell.edu/] at Cornell Tech and The Technion. The VoterFraud group analyzed networks and communities of retweeters and other misinformation promoters to classify several communities of people tweeting about the 2020 election. Early attempts were made to compare this data to other Twitter datasets, but as testing progressed, I became worried that the model was merely able to tell a political tweet from a non-political tweet. 

In order to increase the model’s ability to pick out misinformation from other political discussion, the model was trained on information from the Harvard Dataverse[https://doi.org/10.7910/DVN/PDI7IN]. These tweets relate to the 2016 election, and thus allows the model to train on rhetorically charged political discussions, often including the same people or places, but which predates the 2020 election misinformation claims. 

These two databases provided their information as ‘dehydrated’ tweet ID’s. A major challenge of this model construction came in the form of ‘rehydrating’ these tweets. Twitter API’s put a limit on the number of queries they’ll allow in a given time period.  The notebooks 'Pulling-from-Twitter.ipynb' and 'Pulling-from-Twitter-Election2016.ipynb' contain the cells used to generate the two csv files 'hydrated_tweets' (the misinformation tweets) and 'tweets2016' (the tweets from 2016). These files represent a subset of a subset of the larger dataset available. The origional datasets that I used to pull the tweet id's from are too large to store in git, but can be found through the links provided. 

The method of data collection did lead to some complications in model building. Text from Tweet ids that belonged to users with deleted or suspended accounts won’t be returned. The VoterFraud2020 database had a row to help filter out suspended or deleted accounts, but if a user became inactive after that database was last updated will still return blank data. Additionally, the fact that the VoterFraud2020 dataset was collected through network analysis led to the dispropotionate appearance of links and retweets. Some versions of the model during model building (particularly the TFIDF versions) predicted that a tweet that merely contained 'https' and 'com' was 86% likely to be a 'misinformation' tweet. Obviously, this was a result of an artifact of the way that data was put together, not anything to do with the semantic content of the tweet. Although this did get dealt with (see the section on model building below) this stands as a reminder that any model will only be as good as the data fed into it.  This will particularly become a problem if the model is used to make decisions about moderation, and then decisions about moderation are used to train the model. At that point the model is just recursively reinscribing itself and any quirks it has. 


## Model Building

# MORE TO COME
