# NSL-KDD Intrusion Detection Classifier

A machine learning classifier trained on the NSL-KDD dataset to detect network intrusions, with a focus on understanding *why* certain attacks get missed rather than just reporting accuracy.

## What I did
- Loaded and explored the NSL-KDD dataset (train/test splits, feature and attack-type distribution)
- Preprocessed the data: column naming, one-hot encoding of categorical features, train/test split
- Trained a classification model to distinguish normal traffic from attack traffic
- Evaluated performance using a confusion matrix and a per-attack-type breakdown of misclassifications

## What I found
The model didn't miss attacks uniformly — It missed about 51% of R2L attacks, accompanied by 31% of DoS attacks, with 13% of Probe and 1% of U2R attacks. It was surprising to have DoS attacks missed since they are quiet easy to pinpoint. Digging into *why* those specific types were missed was the most useful part of this project — it pointed to 3 major reasons: 

1. The test data set had different types of DoS compared to the training data
2. Class Imbalance: the attacks subclasses which were mostly ignored had less amount of data to be trained on. 
3. The features of some attacks which were present in both tain and test dataset varied. 

## What I tried to improve
I first tried to balance the classes to have the model to pay more attention to minority classes. It didnt work because it only balanced the normal vs attack classes not the subclasses of attacks. 

I tried SMOTE (Synthetic sampling) to create synthetic samples and increase the train set for attacks with less data. But SMOTE failed. It was because even the synthetic samples made from train data contained different features compared to test data set
This mattered more to me than the overall accuracy number, since a SOC context cares which attacks slip through, not just the aggregate score.

## Final Conclusion?
- Accuracy on Test data set was 76.5% while accuracy on Train data set was 99.99%. 
- Most attacks were missed because the never saw those types during training. 
- But two types — guess_passwd and warezmaster — were in the training data, and still got 
  missed heavily beacuse of lack of training data. we tried fixing it by balancing and SMOTE since that were the major reasons. but we found out that it were also missed partially because the features of the training data for the attack were different from features of the attacks in test data. 

- Rebalancing/oversampling techniques can only recombine patterns that already exist in your real data — they can't manufacture a behavior your training data never captured in the first place.

## What I'd improve
- Train the model on more sets with actual mix of features
- Have it analyze the activities of the IP addresses from past to detect any anomaly or suspicious behaviours. 

## Tools
Python, pandas, scikit-learn, Google Colab

## Dataset
[NSL-KDD](https://www.unb.ca/cic/datasets/nsl.html) — a refined version of the KDD Cup 1999 dataset used for benchmarking intrusion detection systems.