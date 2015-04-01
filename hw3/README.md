There are three Python programs here (`-h` for usage):

 - `./decode` a simple non-reordering (monotone) phrase-based decoder
 - `./grade` computes the model score of your output

The commands are designed to work in a pipeline. For instance, this is a valid invocation:

    ./decode | ./grade


The `data/` directory contains the input set to be decoded and the models

 - `data/input` is the input text

 - `data/lm` is the ARPA-format 3-gram language model

 - `data/tm` is the phrase translation model


====

I modified the decoding file so that phrases don't have to be translated in the same order.
I first inserted a loop that iterates for k between i and j, and look if what appears between
i and k is a phrase, as well for between k and j. If this is the case, then I compute its score
and I add a new hypothesis. Then I do the same the 3 and 4 permutations:
A B C can become C B A, A B C D can become D C B A, etc.

Increasing the size of the stack gives better results. I did some pruning to make the procedure
faster, but it remains pretty slow. The method and code are ugly but I don't have time to improve it.

