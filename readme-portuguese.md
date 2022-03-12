# Extending ERRANT to a new language
In order to extend ERRANT to a new language a couple of steps are needed, that are not well documented in previous approaches.

First you need to install the dependecies:
```bash
$ pip install -r requirements.txt
```

Then download spacy for the language you want to use:
```bash
$ python -m spacy download es
```

## Install treetagger
Choose a directory, which we will refer to as `tree_dir` from now on, to install treetagger.
Now run the following commands:

```bash
$ cd tree_dir
```
```bash
$ wget https://www.cis.lmu.de/~schmid/tools/TreeTagger/data/tree-tagger-MacOSX-3.2.3.tar.gz
```
```bash
$ wget https://www.cis.lmu.de/~schmid/tools/TreeTagger/data/tagger-scripts.tar.gz
```
```bash
$ wget https://www.cis.lmu.de/~schmid/tools/TreeTagger/data/install-tagger.sh
```
```bash
$ wget https://www.cis.lmu.de/~schmid/tools/TreeTagger/data/german.par.gz
```
```bash
$ sh install-tagger.sh 
```

Now to test if the installation was successfull:
```bash
$ echo 'Das ist ein Test.' | cmd/tree-tagger-german
```

Under `tree_dir/lib` you should now have a file named `german.par`, duplicate it with the name `german-utf8.par`.

Before running the last `install` command you should download the files for all the languages you want, which you can get [here](https://www.cis.lmu.de/~schmid/tools/TreeTagger/#parfiles).

Since [treetaggerwrapper](https://treetaggerwrapper.readthedocs.io/en/latest/) will be used, the supported languages for the code "as is" are:
* spanish (es)
* french (fr)
* english (en)
* german (de)
* bulgarian (bg)
* dutch (nl)
* estonian (et)
* finnish (fi)
* galician (gl)
* italian (it)
* korean (kr)
* latin (la)
* mongolian (mn)
* polish (pl)
* russian (ru)
* slovak (sk’)
* swahili (sw)

## Create a new `cat_rules` file under scripts
In our case we created `scripts/cat_rules_es.py`.
In this file we changed line 11, which contained a `verb_tags` dictionary, which we updated for spanish, but we kept the remaining POS tags the same, since they come from the Stanford Universal Dependency, and are available to every language.

## Update `toolbox`
We then updated `toolbox.py` to use tags from the treetagger and not spacy, since tags from spacy in Spanish are quite complex/descriptive and do not add more information for this use case.

You might not want to do this depending on the Spacy tags for the language you are using, for German they used mostly the Spacy tags.

## Resources
Now comes the more troublesome part, where we will have to generate a list of words and a mapping between PoS tags.

Under `resources` you can find these files, you will have to generate new ones for the languages you want to use.

### List of words for the language, based on  `Hunspell` dicts
For the list of words, go to this [Github](https://github.com/wooorm/dictionaries/tree/main/dictionaries) and choose the `Hunspell` dictionary you want, or choose your own if you already have one.

Then run the following commands:
```bash
$ mkdir dict_generate
$ cd dict_generate
```

```bash
$ wget -O unmunch.cxx "https://raw.githubusercontent.com/hunspell/hunspell/master/src/tools/unmunch.cxx"
```

```bash
$ wget -O unmunch.h "https://raw.githubusercontent.com/hunspell/hunspell/master/src/tools/unmunch.h"
```

```bash
$ g++ -o unmunch unmunch.cxx
```

```bash
$ unmunch es_ES.dic es_ES.aff 2> /dev/null > es_ES.txt.bk
$ sort es_ES.txt.bk > es_ES.txt
$ rm es_ES.txt.bk
```

You should change `es_ES.x` to the dictionary you downloaded.

After these steps you should have the `.txt` file for your language, which you will add to the `resources` folder.

### Part of Speech mapping
For spanish there was, to the best of our knowledge, no conversion available between the `treetagger` tags and the Stanford Universal Dependency.

So this file had to be created manually between the [`treetagger` tags](https://www.sketchengine.eu/spanish-treetagger-part-of-speech-tagset/) and [Stanford Universal Dependency tags](https://universaldependencies.org/u/pos/).

But there are some mappings available [here](https://universaldependencies.org/tagset-conversion/), your language might have a good mapping.

