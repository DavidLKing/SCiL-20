# Morph Analysis Tools

Various tools we used in the paper "Interpreting Sequence-to-Sequence Models for Russian Inflectional Morphology"

These tools boil down into 3 main components:
- `pull*.py` A script for extracting all errors and correct forms with their morphosyntactic feature sets.
- `check.py` A script for finding differences in edit operations.
- `maxent_test.py` A script for generate maxent datasets.

All scripts are intended to run with python3. [Gensim](https://radimrehurek.com/gensim/) is also required for `maxent_test.py.

## `pull*.py`

For `pullerrors.py` and `pullright.py`, simply run the script on any seq2seq output as such:

`$ ./pullerrors.py FILE`

Where `FILE` is any seq2seq output with a format identical to `data/russian-sample-output.tsv`.  This will output another `tsv` to `stdout` with _either_ the erroneous or correct forms and their morphosyntactic tags (useful for isolating problematic paradigm cells)


## `check.py

```
$ ./check.py -h
usage: check.py [-h] -i INPUT -o OUTPUT [--wrong] [--correct] [--factored]
                [--not-factored]

optional arguments:
  -h, --help            show this help message and exit
  -i INPUT, --input INPUT
                        Input file
  -o OUTPUT, --output OUTPUT
                        Output file
  --wrong               Find errors (and not correct items)
  --correct             Find correct items (and not errors)
  --factored            Select if morphosytactic featres are factored
                        (separated)
  --not-factored        Select if morphosytactic featres are not factored
                        (separated)

```
This script compares gold vs. predicted output Input and output files must be specified. Additionally, either `--wrong` or `correct` should be specified, depending on your task.  You should also specify whether the input has factored morphosyntactic property sets or not. 

Sample usage:
`./check.py -i data/russian-sample-output.tsv -o test.tsv --wrong --not-factored`

## `maxent_test.py`

```
./maxent_test.py -h
usage: maxent_test.py [-h] -e EMBEDDINGS -no NOUN_OUTPUT -vo VERB_OUTPUT -udt
                      UNIDEP_TRAIN -udd UNIDEP_DEV -ude UNIDEP_EVAL -uni
                      UNIMORPH

optional arguments:
  -h, --help            show this help message and exit
  -e EMBEDDINGS, --embeddings EMBEDDINGS
                        Word embeddings file (binary)
  -no NOUN_OUTPUT, --noun_output NOUN_OUTPUT
                        Noun output file
  -vo VERB_OUTPUT, --verb_output VERB_OUTPUT
                        Verb output file
  -udt UNIDEP_TRAIN, --unidep_train UNIDEP_TRAIN
                        Universal Dependency training file
  -udd UNIDEP_DEV, --unidep_dev UNIDEP_DEV
                        Universal Dependency development file
  -ude UNIDEP_EVAL, --unidep_eval UNIDEP_EVAL
                        Universal Dependency testing file
  -uni UNIMORPH, --unimorph UNIMORPH
                        Unimorph file
```
All arguments are required.

Use this script to generate the datasets we used for the post hoc inflection class analsys. In addition to this script, you'll need one (or all) of the UD Russian corpora (we used [SynTagRus](https://universaldependencies.org/treebanks/ru_syntagrus/index.html)), Hal Daumé's [MegaM](http://users.umiacs.umd.edu/~hal/megam/version0_3/)) maxent implementation, and the Russian [Unimorph](https://github.com/unimorph/rus) (a sample can be found [here](https://dlk.sdf.org/transfer/rus-unimorph.tsv)). Additionally, you'll need Russian word embeddings. For testing, feel free to use [these](https://dlk.sdf.org/transfer/vectors.50.bin)---they're awful, but feel free to prototype with them. 



Maxent analysis sample usage:
```
$ ~/bin/git/MED-pytorch/maxent_test.py -e vectors.50.bin -no nouns.tsv -vo verbs.tsv -udt UD_Russian-SynTagRus/ru_syntagrus-ud-train.conllu -udd UD_Russian-SynTagRus/ru_syntagrus-ud-dev.conllu -ude UD_Russian-SynTagRus/ru_syntagrus-ud-test.conllu -uni data/rus-unimorph.tsv 
loading word vectors
loading UDs
Starting train
Starting dev
Starting test
Writing files

$ ./megam_i686.opt -nc -tune multiclass nouns.txt > nouns.cls 
Scanning file...13788 train, 5041 dev, 5212 test, reading...done
optimizing with lambda = 1
it 1   dw 7.772e-01 pp 6.70785e-01 er 0.19727 dpp 6.27489e-01 der 0.15076 tpp 6.50583e-01 ter 0.17383
it 2   dw 5.172e-01 pp 5.45225e-01 er 0.19727 dpp 4.96347e-01 der 0.15076 tpp 5.24458e-01 ter 0.17383
it 3   dw 6.983e-01 pp 4.69075e-01 er 0.19727 dpp 4.33728e-01 der 0.15076 tpp 4.58975e-01 ter 0.17383
it 4   dw 1.152e+00 pp 3.55816e-01 er 0.19727 dpp 3.42106e-01 der 0.15076 tpp 3.67777e-01 ter 0.17383
it 5   dw 3.445e+00 pp 3.01134e-01 er 0.00754 dpp 3.37684e-01 der 0.04503 tpp 3.46415e-01 ter 0.05238
it 6   dw 9.810e-01 pp 2.45691e-01 er 0.19727 dpp 2.58248e-01 der 0.15076 tpp 2.88533e-01 ter 0.17383
it 7   dw 3.889e+00 pp 1.86847e-01 er 0.00754 dpp 2.23746e-01 der 0.04086 tpp 2.47292e-01 ter 0.05008
it 8   dw 6.446e-01 pp 1.85168e-01 er 0.00754 dpp 2.22633e-01 der 0.04086 tpp 2.46052e-01 ter 0.05008
it 9   dw 0.000e+00 pp 1.85168e-01 er 0.00754 dpp 2.22633e-01 der 0.04086 tpp 2.46052e-01 ter 0.05008
final dev error=0.0408649 test error=0.0500767
optimizing with lambda = 0.5
it 1   dw 2.740e-01 pp 1.68328e-01 er 0.00754 dpp 2.23000e-01 der 0.04503 tpp 2.36590e-01 ter 0.05238
it 2   dw 1.630e-01 pp 1.46115e-01 er 0.00754 dpp 2.01874e-01 der 0.04503 tpp 2.18898e-01 ter 0.05238
it 3   dw 2.203e-01 pp 1.29020e-01 er 0.00754 dpp 1.86931e-01 der 0.04086 tpp 2.08886e-01 ter 0.05008
it 4   dw 1.613e-01 pp 1.16159e-01 er 0.00754 dpp 1.79277e-01 der 0.04086 tpp 2.03915e-01 ter 0.05008
it 5   dw 4.141e-01 pp 1.05560e-01 er 0.00754 dpp 1.78317e-01 der 0.04086 tpp 2.08126e-01 ter 0.05008
it 6   dw 1.329e-01 pp 8.55081e-02 er 0.00754 dpp 1.69294e-01 der 0.04086 tpp 1.99470e-01 ter 0.05008
it 7   dw 2.131e-01 pp 4.94306e-02 er 0.00754 dpp 1.53596e-01 der 0.04503 tpp 1.82860e-01 ter 0.05257
it 8   dw 2.612e-01 pp 3.56392e-02 er 0.00754 dpp 1.51517e-01 der 0.04503 tpp 1.81390e-01 ter 0.05238
it 9   dw 1.729e-01 pp 3.39648e-02 er 0.00754 dpp 1.51171e-01 der 0.04344 tpp 1.81785e-01 ter 0.05104
it 10  dw 1.218e+00 pp 2.73632e-02 er 0.00754 dpp 1.73034e-01 der 0.04086 tpp 2.12550e-01 ter 0.05008
it 11  dw 0.000e+00 pp 2.73632e-02 er 0.00754 dpp 1.73034e-01 der 0.04086 tpp 2.12550e-01 ter 0.05008
final dev error=0.0408649 test error=0.0500767

```

