# bicleaner

Bicleaner (bicleaner-classify) is a tool in Python that allows to classify a parallel corpus, 
indicating the likelihood of a pair of sentences being mutual translations (with a value near to 1) or not  (with a value near to 0)

Although a training tool (bicleaner-train) is provided, you may want to use the available ready-to-use language packages. 
Please, visit https://github.com/bitextor/bitextor-data/tree/master/bicleaner to download the language packages and the documentation on how to use them.

## Citation 

If you find Bicleaner useful, please consider citing the following paper:

> V. M. Sánchez-Cartagena, M. Bañón, S. Ortiz-Rojas and G. Ramírez-Sánchez,\
> "Prompsit's submission to WMT 2018 Parallel Corpus Filtering shared task",\
>in *Proceedings of the Third Conference on Machine Translation, Volume 2: Shared Task Papers*.\
>Brussels, Belgium: Association for Computational Linguistics, October 2018

```
@InProceedings{prompsit:2018:WMT,
  author    = { V\'{i}ctor M. S\'{a}nchez-Cartagena and Marta Ba{\~n}\'{o}n and Sergio Ortiz-Rojas and Gema Ram\'{i}rez-S\'{a}nchez},
  title     = {Prompsit's submission to WMT 2018 Parallel Corpus Filtering shared task},
  booktitle = {Proceedings of the Third Conference on Machine Translation, Volume 2: Shared Task Papers},
  month     = {October},
  year      = {2018},
  address   = {Brussels, Belgium},
  publisher = {Association for Computational Linguistics}
}
```

## Installation & Requirements

Bicleaner works with Python3 and can be instaled with `pip`:

`python3 -m pip install bicleaner`

Extra modules will be downloaded and installed/upgraded as well, if required.


## Cleaning

`bicleaner-classify` is a Python tool that allows to classify a parallel corpus, 
indicating the likelihood of a pair of sentences being mutual translations (with a value near to 1) or not  (with a value near to 0)

The input file  (the parallel corpus to be classified) must contain at least four columns:

* col1: URL 1
* col2: URL 2
* col3: Source sentence
* col4: Target sentence

Any extra columns will be ignored.

The generated output file will contain the same lines and columns that the original input file, 
adding an extra column containing the Bicleaner classifier score.

This tool can be run with

```
bicleaner-classify [-h] -m METADATA [-s SOURCE_LANG]
                                    [-t TARGET_LANG] [--tmp_dir TMP_DIR]
                                    [-b BLOCK_SIZE] [-p PROCESSES]
                                    [--normalize_by_length] [--treat_oovs]
                                    [--qmax_limit QMAX_LIMIT]
                                    [--disable_features_quest]
                                    [-g GOOD_EXAMPLES] [-w WRONG_EXAMPLES]
                                    [--good_test_examples GOOD_TEST_EXAMPLES]
                                    [--wrong_test_examples WRONG_TEST_EXAMPLES]
                                    [-d DISCARDED_TUS] [--threshold THRESHOLD]
                                    [-q] [--debug] [--logfile LOGFILE] [-v]
                                    input [output]
```

### Parameters

* positional arguments:
  * input: Tab-separated files to be classified  (line format: URL1 URL2 SOURCE_SENTENCE TARGET_SENTENCE [EXTRA_COLUMNS], tab-separated)
  * output: Output of the classification (default: standard output)
* optional arguments:
  * -h, --help: show this help message and exit
* Mandatory:
  * -m METADATA, --metadata METADATA: Training metadata (YAML file). Take into account that explicit command line arguments will overwrite the values from metadata file (default: None)
* Optional:
  * --tmp_dir TMP_DIR: Temporary directory where creating the temporary files of this program (default: user's temp dir)
  * -b BLOCK_SIZE, --block_size BLOCK_SIZE Sentence pairs per block (default: 10000)
  * -p PROCESSES, --processes PROCESSES: Number of processes to use (default: all CPUs minus one)
  * --normalize_by_length: Normalize by length in qmax dict feature 
  * --treat_oovs: Special treatment for OOVs in qmax dict feature
  * --qmax_limit: Number of max target words to be taken into account, sorted by length (default: 20)
  * --disable_features_quest: Disable less important features
  * -g GOOD_EXAMPLES, --good_examples GOOD_EXAMPLES: Number of good examples (default: 50000)
  * -w WRONG_EXAMPLES, --wrong_examples WRONG_EXAMPLES: Number of wrong examples (default: 50000)
  * --good_test_examples GOOD_TEST_EXAMPLES: Number of good test examples (default: 2000)
  * --wrong_test_examples WRONG_TEST_EXAMPLES: Number of wrong test examples (default: 2000)
  * -d DISCARDED_TUS, --discarded_tus DISCARDED_TUS: TSV file with discarded TUs. Discarded TUs by the classifier are written in this file in TSV file. (default: None)
  * --threshold THRESHOLD: Threshold for classifier. If accuracy histogram is present in metadata, the interval for max value will be given as a default instead the current default. (default: 0.5)
* Logging:
  * -q, --quiet: Silent logging mode (default: False)
  * --debug: Debug logging mode (default: False)
  * --logfile LOGFILE: Store log to a file (default: <_io.TextIOWrapper name='<stderr>' mode='w' encoding='UTF-8'>)
  * -v, --version: show version of this script and exit

### Example

```
bicleaner-classify  \
        corpus.en-es.raw  \
        corpus.en-es.classifed  \
        -m training.en-es.yaml \
        -b 100  \
        --tmp_dir /home/user/tmp 
```

This will read the "corpus.en-es.raw" file, 
classify it with the classifier indicated in the "training.en-es.yaml" metadata file,
writing the result of the classification in the "corpus.en-es.classified" file.
Each line of the new file will contain the same content as the input file, adding a column with the score given by the Bicleaner classifier.


## Training classifiers

In case you need to train a new language model (i.e. because it is not available in the language packs provided in [bitextor-data](https://github.com/bitextor/bitextor-data/tree/master/bicleaner)), 
you can use `bicleaner-train` .
`bicleaner-train` is a Python3 tool that allows to train a classifier which predicts 
whether a pair of sentences are mutual translations or not.
It can be used as follows:


```
 bicleaner-train [-h] -m METADATA -c CLASSIFIER -s SOURCE_LANG -t
                          TARGET_LANG -d SOURCE_DICTIONARY -D
                          TARGET_DICTIONARY [--normalize_by_length]
                          [--treat_oovs] [--qmax_limit QMAX_LIMIT]
                          [--disable_features_quest] [-g GOOD_EXAMPLES]
                          [-w WRONG_EXAMPLES]
                          [--good_test_examples GOOD_TEST_EXAMPLES]
                          [--wrong_test_examples WRONG_TEST_EXAMPLES]
                          [--classifier_type {svm,nn,nn1,adaboost,random_forest}]
                          [--dump_features DUMP_FEATURES] [-b BLOCK_SIZE]
                          [-p PROCESSES]
                          [--wrong_examples_file WRONG_EXAMPLES_FILE] [-q]
                          [--debug] [--logfile LOGFILE]
                          [input]
```                          
              
### Parameters

* positional arguments:
  * input: Tab-separated bilingual input file (default: Standard input)(line format: SOURCE_SENTENCE TARGET_SENTENCE, tab-separated)
* optional arguments:
  * -h, --help: show this help message and exit
* Mandatory:
  * -m METADATA, --metadata METADATA: Training metadata (YAML file)
  * -c CLASSIFIER, --classifier CLASSIFIER: Classifier data file 
  * -s SOURCE_LANG, --source_lang SOURCE_LANG: Source language code 
  * -t TARGET_LANG, --target_lang TARGET_LANG: Target language code
  * -d SOURCE_DICTIONARY, --source_dictionary SOURCE_DICTIONARY: LR gzipped probabilistic dictionary 
  * -D TARGET_DICTIONARY, --target_dictionary TARGET_DICTIONARY: RL gzipped probabilistic dictionary
* Options:
  * --normalize_by_length: Normalize by length in qmax dict feature 
  * --treat_oovs: Special treatment for OOVs in qmax dict feature
  * --qmax_limit: Number of max target words to be taken into account, sorted by length (default: 20)
  * --disable_features_quest: Disable less important features 
  * -g GOOD_EXAMPLES, --good_examples GOOD_EXAMPLES: Number of good examples (default: 50000)
  * -w WRONG_EXAMPLES, --wrong_examples WRONG_EXAMPLES: Number of wrong examples (default: 50000)
  * --good_test_examples GOOD_TEST_EXAMPLES: Number of good test examples (default: 2000)
  * --wrong_test_examples WRONG_TEST_EXAMPLES: Number of wrong test examples (default: 2000)
  * --classifier_type {svm,nn,nn1,adaboost,random_forest}: Classifier type (default: random_forest)
  * --dump_features DUMP_FEATURES: Dump training features to file (default: None)
  * -b BLOCK_SIZE, --block_size BLOCK_SIZE: Sentence pairs per block (default: 10000)
  * -p PROCESSES, --processes PROCESSES: Number of process to use (default: all CPUs minus one)
  * --wrong_examples_file WRONG_EXAMPLES_FILE: File with wrong examples extracted to replace the synthetic examples from method used by default (default: None)
* Logging:
  * -q, --quiet: Silent logging mode (default: False)
  * --debug: Debug logging mode (default: False)
  * --logfile LOGFILE: Store log to a file (default: <_io.TextIOWrapper name='<stderr>' mode='w' encoding='UTF-8'>)

### Example

```shell
bicleaner-train \
          corpus.en-cs.train\
          --treat_oovs \
          --normalize_by_length \
          -s en \
          -t cs \
          -d dict-en.gz \
          -D dict-cs.gz \
          -b  1000 \
          -c en-cs.classifier \
          -g 50000 \
          -w 50000 \
          -m training.en-cs.yaml \
          --classifier_type random_forest
```

This will train a Random Forest classifier for english-czech using the corpus corpus.en-cs.train and the probabilistic dictionaries dict-en.gz and dict-cs.gz. 
This training will use 50000 good and 50000 bad examples, and a block size of 1000 sentences.
The classifier data will be stored in en-cs.classifier, with the metadata in training.en-cs.yaml.

The generated .yaml file provides the following information, that is useful to get a sense on how good or bad was the training  (and is also a needed input file for classifying):

```
classifier: en-cs.classifier
classifier_type: random_forest
source_lang: en
target_lang: cs
source_dictionary: dict-en.gz
target_dictionary: dict-cs.gz
normalize_by_length: True
treat_oovs: True
qmax_limit: 20
disable_features_quest: True
good_examples: 50000
wrong_examples: 50000
good_test_examples: 2000
wrong_test_examples: 2000
good_test_histogram: [0, 2, 18, 83, 217, 450, 1596, 7245, 389, 0]
wrong_test_histogram: [3, 3114, 3949, 2309, 516, 86, 22, 1, 0, 0]
precision_histogram: [0.5000000, 0.5000750, 0.5922635, 0.7728047, 0.9406006, 0.9888651, 0.9975143, 0.9998690, 1.0000000, nan]
recall_histogram: [1.0000000, 1.0000000, 0.9998000, 0.9980000, 0.9897000, 0.9680000, 0.9230000, 0.7634000, 0.0389000, 0.0000000]
accuracy_histogram: [0.5000000, 0.5001500, 0.6557500, 0.8523000, 0.9636000, 0.9785500, 0.9603500, 0.8816500, 0.5194500, 0.5000000]
length_ratio: 0.9049934
```
