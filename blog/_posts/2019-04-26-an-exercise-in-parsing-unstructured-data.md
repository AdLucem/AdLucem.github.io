---
layout: post
title: An Exercise in Annotating Unstructured Data
date: 2018-04-12
published: false
category: blog
cssfile: post.css
---


## Steps

* Scrape all the words from `xml` format and put them in a `txt` file with one word per line:

** Getting the `other="word"` column of the file:

```bash
$ awk '{print $2}' words.xml
other="Candas"
other="CandonAman"
other="Candonaman"
other="DAnya"
other="DAtvarTa"
other="DAvati"
other="Dana"
```

** Extracting the word itself from the above column using a cascade of `sed` replacement operators connected with pipes:

```bash
$ awk '{print $2}' words.xml | sed 's/other="//g' | sed 's/"//g'
Candas
CandonAman
Candonaman
DAnya
DAtvarTa
DAvati
Dana
```

** Great! Now I read that list to a file called `my_words.txt`.

* Run `monier-api-getter` script on `my_words.txt`

* Next

```bash
python read_data_to_dict.py
```

* Then Categorize

## Blogpost
As the course project from Prof. Peter Scharf's amazing course, Indian Semantics and Ontology, we were given the task to take a number of words- in Sanskrit- and add them to an existing ontology.

The computational portion of the project involved a problem I see far too often in NLP projects- working with a lot of data, completely unstructured, with possible void fields, in an unknown language for which no/barely any library support is available.

I largely dislike looking at data manually. Don't get me wrong- I love languages, which is why I decided to pursue a degree in computational linguistics. I am also, however, lazy, and want to make the computer do as much of the information processing as I can. Here's how I do it.

## The Words


## Multiple Meanings For A Single Word

Immediately, I run into a problem: a single sanskrit word form may have multiple homonyms, and the requirement is to use the meaning of the word as it's used in an ancient Indian grammatical text- the Astadhyayi.

[...] 255 meanings for around 100-105 words. Let's pare it down. Usually, the word with the greatest number of senses is the primary meaning of the word- Monier-William's dictionary has an odd habit of returning words that do not relate to the primary encoding, but on a quick look through the data, the 'secondary' words have very specific senses instead of the broad, generalized set of senses that the 'primary' word have.

...

Pared down to a manageable 79 word-senses pairs. Okay, we can handle this.

## Looking At The Corpus, Command-Line Style

First of all, let's see what kind of words there are. I'll print each word and its first meaning onto my terminal.

```bash
$ grep -A 3 "[0-9]*[)]" meanings.txt
```

Huh. I think most of my words are nouns. Let's check- there are 100 words, each with one meaning printed, and most root nouns have an "n." in the beginning of the primary meaning, followed by a space:

```bash
$ grep "n[.]\s" meanings.txt > out
$ wc -l out
171
```

171 words! That is... a lot of noun words. There's a problem though- not all of these may be primary meanings. I want the word meaning right below the "-----------------" line, and I want to check if _that_ has a "n. " marker in it.

For this sort of inventive regexing I turn to a python CLI.

```python
f = open("meanings.txt", "r+")
s = f.readlines()
```

### Lambda Functions Are Your Friend

Defining one-line lambda functions based on each pattern I want to identify, makes the downstream code much easier to write- and more readable, to boot.

```python
import re
p = re.compile("n[.]\s")
cond_prim = lambda x: "--------------" in x
cond_noun = lambda x: re.search(p, x)
```

### List Comprehensions Are Also Your Friend

And putting all of it together:

```python
>>> ls = [(s[i-1], l) for (i, l) in enumerate(s) if (cond_prim(s[i-1]) and cond_noun(l))]
>>> len(s)
144 
```

That's a lot of meanings. Okay, I infer that most of the words are nouns, with a few verbs.

## Categorizing Nouns

The second question is, what _kind_ of nouns are there?

Here are some randomly sampled word-meanings from the list.

```
0) SEzika
śaiṣika
-----------------------------------------------------------------------
->  mf(ī)n. (fr. śeṣa) relating to the remainder , holding good in the remaining cases (but only now and not in previous cases)  Ka1r.  on  Pa1n2.   S3is3.  Sch.
=======================================================================

5) ADAra
ā-dhāra
-----------------------------------------------------------------------
->  » under ā- √dhṛ.
=======================================================================

13) ASaMsA
ā-śaṃsā
-----------------------------------------------------------------------
->  f. hope , expectation , desire , wish  Pa1n2.   Ragh.   Vikr.   Sa1h.

21) Adi
ādi
-----------------------------------------------------------------------
->  m. beginning , commencement
=======================================================================

35) Anulomya
ānulomya
-----------------------------------------------------------------------
->  mf(ī)n. in the direction of the hair , produced in natural or direct order
=======================================================================

63) BAsana
bhāsana
-----------------------------------------------------------------------
->  n. shining , glittering , brilliance , splendour  Pa1n2.   Nir.
=======================================================================
```

* Most of them are abstract nouns. This observation holds for most of the words in the full list- you can see it here (TODO: add link). An ontology based on concrete object/process/verb classification- like the Suggested Upper Merged Ontology (Nair et al, 2011), will not be useful.
* There are no clumps of words related to a single concept- the meanings are all over the place in concept-land. So something based on concepts/attributes like ConceptNet will not be useful.

What will be useful?

## Roget's Thesaurus Ontology



## Let Me Categorize The Words Easily

```python
for index, word in enumerate(ls):
    print(word['text'])
    print(word['meaning'])
    cat = input("Category: ")
    ontology[index] = cat
```



