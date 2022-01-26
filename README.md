# DS-Capstone-Twitter-Misinformation

## The Problem

This project began, more or less, on the one year anniversary of the January 6th riots/insurrection. An often discussed factor in this event is the role played by social media in radicalizing those who attacked the Capitol building. In the wake of those events, Twitter moved rapidly to suspend many accounts associated with election misinformation. 

This project is looking to explore how NLP tools can assist moderation of online spaces b y helping to identify potentially dangerous tweets. There are some questions here about how Twitter might use this information, but this model aims only to identify tweets.

## The Data

In order to train a model to recognize misinformation, the model must first be given tweets that contain misinformation. For that this project relies on the work of VoterFraud2020, a collaboration between the Social Technologies Lab[https://s.tech.cornell.edu/] at Cornell Tech and The Technion. The VoterFraud group analyzed networks and communities of retweeters and other misinformation promoters to classify several communities of people tweeting about the 2020 election. Early attempts were made to compare this data to other Twitter datasets, but as testing progressed, I became worried that the model was merely able to tell a political tweet from a non-political tweet. 

In order to increase the model’s ability to pick out misinformation from other political discussion, the model was trained on information from the Harvard Dataverse[https://doi.org/10.7910/DVN/PDI7IN]. These tweets relate to the 2016 election, and thus allows the model to train on rhetorically charged political discussions, often including the same people or places, but which predates the 2020 election misinformation claims. 

These two databases provided their information as ‘dehydrated’ tweet ID’s. A major challenge of this model construction came in the form of ‘rehydrating’ these tweets. For one, Twitter API’s put a limit on the number of queries they’ll allow. Additionally, text from Tweet ids that belonged to users with deleted or suspended accounts won’t be returned. The VoterFraud2020 database had a row to help filter out suspended or deleted accounts, but if a user became inactive after that database was last updated will still return blank data.

This model will require some tuning as time passes. 


# MORE TO COME
