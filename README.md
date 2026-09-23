# heldout-ll-permutation
The dataset partitioning -> heldout LMER likelihood calculation -> permutation testing pipeline

`partition.py` handles the dataset partitioning. In much of my (and Cory's) work, this is based on the subject ID and the sentence ID.
The `compute_splitID` function turns the subject ID and sentence ID into integer codes and adds them together.
The modulo operation is applied to this `splitID` to partition the data points, which means that all data points from a particular subject-by-sentence combination will be assigned to one partition.
In principle, the held-out split can have 'unseen' subjects or sentences, although I think this would rarely happen in practice.

`get_loglik_lmer.r` calculates likelihood on held-out data from a fitted LMER model.
The implementation relies heavily on [this StackExchange post](https://stats.stackexchange.com/questions/271903/understand-marginal-likelihood-of-mixed-effects-models), and assumes that there are only by-subject random effects in the model.
It iterates over different subjects in the held-out data, calculates the log-likelihood of the subset of data points from each subject, and adds them up together.
In this particular implementation (i.e. I don't think Cory does this), I also normalized the total log-likelihood by the number of data points, in case the partitions are imbalanced in the number of data points.

`pt.py` performs the paired permutation testing.
Given some number of random shuffles (usually 10k) between two vectors of likelihoods, it tallies how many times the difference in means *after shuffling* is bigger or smaller than the observed difference in means.
*p*-values are defined as the proportion of times the difference after shuffling is more extreme than what is observed.
