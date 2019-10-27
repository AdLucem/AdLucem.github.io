---
layout: post
title: Web Scraping From A Dictionary
date: 2019-04-21
category: blog
cssfile: post.css
published: true
---

This is a little code snippet I made to get the meanings of a list of words from an online dictionary, using web scraping and python's `beautifulsoup` library.

Libraries used: `beautifulsoup`, `re`

The dictionary in question is Monier-Williams' Sanskrit Dictionary. Using the web console, I found out that when given a word, the dictionary runs a GET request to the server, with the word in the url. Given a word, I write the GET request URL like so:

```python
	r = requests.get('https://www.sanskrit-lexicon.uni-koeln.de/cgi-bin/monier/monierv1a.pl?key=' + word + '&filter=SktRomanUnicode&noLit=off&transLit=SLP2SLP&scandir=../..MWScan/MWScanpng&filterdir=../../docs/filter')
```

## Picking Through HTML Using BeautifulSoup

The `content` field of the above result returns a pile of HTML, which I have to carefully pick through, At the end of it, I realise that the dictionary returns a bunch of different `head` words for a given string- like, say, the string 'bear' will return two head words- 'bear' as in the animal, and 'bear' as in the verb. Each head word is a blue-colored element in a HTML table, and the list of meanings for each head word consist of the table rows under that head word.

I encode all of this in Python and beautifulSoup like so:

```python
	table = soup.find_all('td')
	heads = []
	for td in table:
	    for n in range(1, 100):
	        s = 'H' + str(n)
	        if s in str(td):
	            heads.append(td)
	            break

	cur_head = ""
	meanings = {}
	for td in table:
	    if td in heads:
	        cur_head = td
	        meanings[cur_head] = []
	    else:
	        meanings[cur_head].append(td)
```

## Regex and Formatting

So now my problem is that each meaning is a string inside a <td> HTML tag, with multiple odd text formatting tags within the meaning string itself. Some regex witchery ensues:

```python
	ptrn1 = re.compile("</*\s*[^>]*>")
	ptrn2 = re.compile("\&amp\;c")
	ptrn3 = re.compile("\[L\=[0-9]+\]")

	# for each 'meaning' tag
	m_ = re.sub(ptrn1, "", str(meaning_tag))
	m_ = re.sub(ptrn2, "", m_)
	m_ = re.sub(ptrn3, "", m_)
	m_ = m_.rstrip().lstrip()
	if m_ != "":
	    print("->  " + m_)
```

And I can now extract the meaning string from each tag!

Some headword formatting later, the code can, given a word, return its headwords and meanings in a nice clean format, like so:

```bash
$ python get.py <word- in the correct sanskrit formatting>
headword 1
-----------------------------------
-> meaning 1
-> meaning 2
===================================
headword 2
-----------------------------------
-> meaning 1
-> meaning 2
===================================
```

## Sanskrit Aside

The point of this code is to give an example snippet of webscraping from a standard dictionary. Thus, the details of the sanskrit words is irrelevant.

However, for those who have a knowledge of sanskrit, the input encoding used is SLP1, and the output is returned in Roman Unicode encoding. Although monier-william's online dictionary accepts other sanskrit formatting types, you'll have to find out the URL for those by changing the 'transLit' field in the above URL to the keyword for your desired input encoding. Just as a reference, the encoding styles and keywords are:

<ul>
	<li> Harvard-Kyoto: HK </li>
	<li> SLP1: SLP2SLP </li>
	<li> ITRANS: ITRANS </li>
</ul>

## Generalizing to Getting Words From A File

At the end, I wrap all of it together in a function called `get`, purely so I can import it into another file.

And this function runs the above code on a `text` file of words:

```python

from get import get

def get_from_file(fname):

	with open(fname, "r+") as f:
		words = f.readlines()
		words = list(map(
			lambda x: x.rstrip("\n"), words))
		
		for index, word in enumerate(words):
			print(str(index) + ") " + word)
			get(word)
```

This little piece of code can be found [here](https://github.com/AdLucem/monier-api-getter)
