
## Setup

```
pip3 install --user beautifulsoup4 sklearn testfixtures unittest2 pyemd morfessor
pip3 install --user spacy
pip3 install --user https://github.com/explosion/spacy-models/releases/download/en_core_web_md-1.2.1/en_core_web_md-1.2.1.tar.gz
python3 -m spacy link en_core_web_md en_default
pip3 install --user nltk
printf 'd\nwordnet\nq' | python3 -c 'import nltk; nltk.download()'
pip3 install --user tensorflow-gpu
```

Put a link to your copy of Gigaword 5th edition

```
ln -s /path/to/gigaword/5ed data/gigaword
```

Install the modified version of gensim in order to train sense embeddings.

```
./install-gensim-modified.sh
```

Make sure you have a Java JDK installed.

### Specific instructions for DAS-5

If you get error `No module named 'pip'` while importing spacy, you might want
to log in to one of the compute nodes and install pip.
For example:

```
ssh node057
easy_install-3.4 --user pip
python3 -c 'import pip'
exit
```

If you get `No module named 'UserString'` while loading a Word2vec model from
disk, probably it is caused by the difference between compute node and login
node. You might ssh to one of the compute nodes to continue your work.


### Specific instructions for Babelfied Wikipedia conversion

1. `wget` Babelfied Wikipedia in XML from [here](http://lcl.uniroma1.it/babelfied-wikipedia/files/babelfied-wikipediaXML.tar.gz) and untar it.
2. make sure the variable **main_input_folder** in scripts/convertbn2wn_v2.py points to the untarred folder from step 1.
3. Depending on the number of CPU's in the environment in which you run the conversion, indicate how many parallel processes you want to run by changing the value of the variable **num_workers** in scripts/convertbn2wn_v2.py line 43.
4. Conversion was tested in python3.5 with the following external modules:
	* lxml (lxml.etree version 3.6.4)
	* nltk (version 3.2.1)
	* pandas (version 0.18.1)

## Steps

1. Pre-process GigaWord into plain text: `python3 process-gigaword.py > output/gigaword.txt`
1. Create a word2vec model from Gigaword corpus: 
`./train-word-embeddings.sh output/gigaword.txt output/gigaword`
2. Extract BabelNet-to-WordNet mappings: `./extract-bn-wn-mappings.sh`
3. Convert [Babelfied Wikipedia](http://lcl.uniroma1.it/babelfied-wikipedia/)
	* `cd scripts`
	* `python convertbn2wn_v2.py`
4. Train sense embeddings: `./train-sense-embeddings.sh`
5. Check your sense embeddings: `python3 examine-sense-embeddings.py`
