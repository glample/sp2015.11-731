There are three Python programs here (`-h` for usage):

 - `./rerank` a simple reranker that simply sorts candidate translations on log p(czech|english)
 - `./grade` computes the mean reciprocal rank of your output

The commands are designed to work in a pipeline. For instance, this is a valid invocation:

    ./rerank | ./check | ./grade


The `data/` directory contains the input set to be decoded and the models

 - `data/train.input` is the input side of training set in the format described on the homework webpage

 - `data/train.refs` are the references to the training set, giving the correct czech translation for the highlighted phrase in each sentence

 - `data/train.parses` are dependency parses of the training sentences, provided for convenience. (Note: these files are provided in gzip format to avoid the space limitations imposed by github)

 - `data/dev+test.input` is the input side of both the dev and test sets

 - `data/dev.refs` are the references to the dev set, which is the first half of the above dev+test file

 - `data/dev+test.parses` are dependency parses of the dev and test sentences, provided for convenience

 - `data/ttable` is the phrase translation table which contains candidates that you will rerank

 If you want the raw parallel data used to build the training data and translation tables English-Czech data (for example, to build word vectors), it is available at http://demo.clab.cs.cmu.edu/sp2015-11731/parallel.encs .

 
=========

README

# Cross-lingual Language vector model (Kazuya / Guillaume)

## Preprocessing
We trained 100 dimension skip-gram word vectors for both languages with the word2vec toolkit. We then projected these vectors into a common 50 dimension space with [CCA toolkit] (http://www.cs.cmu.edu/~mfaruqui/papers/eacl14-vectors.pdf). 

## Features
1. Cosine similarity between Czech target word vector and english averaged context vector (dim:1), repeated 100 times (dim:100). Without this little trick of duplicating the cosine similarity, the network is not able to give enough weight to this feature which turns out to be crucial.
2. Source word vector. (dim:50)
3. Context vector, which is obtained by reading the source words in a context window of size 4 using LSTM, in forward and backward way. (forward dim:10, backward dim:10)
4. The four default phrase table features

## Architecture
The four features above are concatenated into a single vector of dimension 174 (100 + 50 + 20 + 4), which is feed into a Multi Layer Perceptron. The MLP has 2 layers of dimension 10 and 1, the last one being the output unit. We use sigmoid as squash functions, so that the output of the network is between 0 and 1. We then train the network so that for a given phrase / context, a Czech phrase will have a score of 1 if this is the correct translation, and 0 otherwise. The cost function is defined by -log(y) is the Czech phrase is correct, and -log(1-y) otherwise.
The network is trained using Adadelta with minibatches.

# Other methods
We tried different methods to combine the left and right contexts:
- LSTM
- Average
- Convolutional neural network

The convolutional neural network didn't turn out to work well on this task, it was suffering from overfitting. Although taking the average of the 4 words in the left and right context is a very naive method, it performed really well, and almost gave as good results as LSTM.
