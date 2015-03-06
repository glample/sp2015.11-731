There are three Python programs here (`-h` for usage):

 - `./evaluate` evaluates pairs of MT output hypotheses relative to a reference translation using counts of matched words
 - `./check` checks that the output file is correctly formatted
 - `./grade` computes the accuracy

The commands are designed to work in a pipeline. For instance, this is a valid invocation:

    ./evaluate | ./check | ./grade


The `data/` directory contains the following two files:

 - `data/train-test.hyp1-hyp2-ref` is a file containing tuples of two translation hypotheses and a human (gold standard) translation. The first 26208 tuples are training data. The remaining 24131 tuples are test data.

 - `data/train.gold` contains gold standard human judgements indicating whether the first hypothesis (hyp1) or the second hypothesis (hyp2) is better or equally good/bad for training data.

Until the deadline the scores shown on the leaderboard will be accuracy on the training set. After the deadline, scores on the blind test set will be revealed and used for final grading of the assignment.


=========

I do something very simple:

I associate every word to a vector (I tried different set of word vectors), and given two sentences of size n and m, I compute M,
the matrix of size n*m, such as M_{i, j} is the cosine similarity between the word i and the word j.
This matrix can actually be seen as a bipartite graph, where each part of the graph represent a sentence. Then, I compute the best
matching of this graph (http://en.wikipedia.org/wiki/Hungarian_algorithm).

This gives me a score that I normalize. This way, I compute the score of the two sentences with the reference, and the one with the
highest matching will be labeled as the good one.

This method was enough to have about 0.53 accuracy. Then, to penalize a matching between a word at the beginning
of a sentence to a word at the end of the other sentence, I add a exp(-alpha * |i/n - j/m|) term, where I tried different
values of alpha. It gives me an accuracy of 0.54620726 for alpha = 0.75.

Finally, I tried to add a language model but it didn't really improve the results.

