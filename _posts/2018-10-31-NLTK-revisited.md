---
layout: post
title: NLTK revisited
category: nlp
tags: nlp,nltk,text
---

## NLTK revisited: why
When you start working with some text-analysis project, sooner or later you will encounter the following problem: Where to find sample text, how to get resources, where should I start.  When I [first had a contact (Polish language post)](http://lukcreates.pl/dajsiepoznac2017/porownanie-tekstu-scikit-learn-i-nltk-nlp/) with NLP I didn't appreciate the power that lies behind the NLTK - the Python first-choice library for NLP.  However, after several years, I see that I could use it earlier.  Why?  NLTK comes with an easy access to various sources of text. And I am going to show you what particularly I like and what caught my attention when studying the first 3 chapters of an [official book](http://nltk.org/book/).

But the main business is what?  I would like to (finally) build some *Suggestion tool* that will allow providing some Virtual Assistant to help in decision making progress.

## Bundled resources available
### brown and its categories
NLTK comes with various corpora, so big packs of text.  You can utilize them as shown in the following example.  All you need to do is to download appropriate corpus and start exploring that.  Let us see now.




```python
import nltk
nltk.download('brown')
files = nltk.corpus.brown.fileids()
print(f"'Brown' corpus contain {len(files)} files")
```

    [nltk_data] Downloading package brown to /root/nltk_data...
    [nltk_data]   Unzipping corpora/brown.zip.
    'Brown' corpus contain 500 files
    

In this corpus you will find different text categorized into categories.  So it nicely fits into classification area of machine learning.  Following there are categories of these texts together with some samples.


```python
print("'brown' contains following categories %s" % nltk.corpus.brown.categories())
brown_adventure = nltk.corpus.brown.sents(categories='adventure')[0:5]
brown_government = nltk.corpus.brown.sents(categories='government')[0:5]
print("Following we have some sentences from 'adventure' category:")
for sent in brown_adventure:
  print(" > "+ " ".join(sent))
print("And here we have some sentences from 'government' category:")
for sent in brown_government:
  print(" > "+ " ".join(sent)) 
```

    'brown' contains following categories ['adventure', 'belles_lettres', 'editorial', 'fiction', 'government', 'hobbies', 'humor', 'learned', 'lore', 'mystery', 'news', 'religion', 'reviews', 'romance', 'science_fiction']
    Following we have some sentences from 'adventure' category:
     > Dan Morgan told himself he would forget Ann Turner .
     > He was well rid of her .
     > He certainly didn't want a wife who was fickle as Ann .
     > If he had married her , he'd have been asking for trouble .
     > But all of this was rationalization .
    And here we have some sentences from 'government' category:
     > The Office of Business Economics ( OBE ) of the U.S. Department of Commerce provides basic measures of the national economy and current analysis of short-run changes in the economic situation and business outlook .
     > It develops and analyzes the national income , balance of international payments , and many other business indicators .
     > Such measures are essential to its job of presenting business and Government with the facts required to meet the objective of expanding business and improving the operation of the economy .
     > Contact
     > For further information contact Director , Office of Business Economics , U.S. Department of Commerce , Washington 25 , D.C. .
    

There is a very important component of NLP in the *brown* corpus, namely Part Of Speech tagging (POS).  How is it organized?  Let us see the example from one of the sentences printed just minutes ago.


```python
adv_words = nltk.corpus.brown.words(categories='adventure')
print(adv_words[:10])
adv_words = nltk.corpus.brown.tagged_words(categories='adventure')
print(adv_words[:10])
```

    ['Dan', 'Morgan', 'told', 'himself', 'he', 'would', 'forget', 'Ann', 'Turner', '.']
    [('Dan', 'NP'), ('Morgan', 'NP'), ('told', 'VBD'), ('himself', 'PPL'), ('he', 'PPS'), ('would', 'MD'), ('forget', 'VB'), ('Ann', 'NP'), ('Turner', 'NP'), ('.', '.')]
    

Yes! It is tagged and ready to be analyzed.  What does these symbols mean?  They are symbols of parts of speech, nicely described [here](https://www.ling.upenn.edu/courses/Fall_2003/ling001/penn_treebank_pos.html).  F.e. `NP` stands fo *Proper Noun* and `VBD` is *Verb, past tense*.

### sentiments
One of the areas where NLP is used is a Sentiment Analysis, playing an important role in digital marketing.  Imagine how nice it is to process vast amount of opinions and instantly recognize whether the product is approved or rejected by a community.  Seeing trends is also possible with that.  So what is the one of the NLKT bundled tools to deal with sentiments?  This is `opinion_lexicon`


```python
from nltk.corpus import opinion_lexicon
nltk.download('opinion_lexicon')
negatives = opinion_lexicon.negative()[:10]
positives = opinion_lexicon.positive()[:10]
print("If you find some negative words, here you are: %s" % negatives)
print("But let us try to see the positive side of life! Described with these words: %s" % positives)
```

    [nltk_data] Downloading package opinion_lexicon to /root/nltk_data...
    [nltk_data]   Unzipping corpora/opinion_lexicon.zip.
    If you find some negative words, here you are: ['2-faced', '2-faces', 'abnormal', 'abolish', 'abominable', 'abominably', 'abominate', 'abomination', 'abort', 'aborted']
    But let us try to see the positive side of life! Described with these words: ['a+', 'abound', 'abounds', 'abundance', 'abundant', 'accessable', 'accessible', 'acclaim', 'acclaimed', 'acclamation']
    

### and much more...
There is a lot of other corpora available there.  This is not the task of this post/notebook to repeat sth what one can read him/herself in the [official papers as here](http://www.nltk.org/howto/corpus.html#tagged-corpora).  With NLTK after downloading some material, you have access to such materials as:
* multilingual corpora (like *Universal Declaration of Human Rights*	with 300+ languages)
* lexical resources (*WordNet*)
* pronouncing dictionaries (*CMU Pronouncing Dictionary*)

Lots of things to browse.  But it is worth just to take a look on some of them, to have this feeling "I saw it somewhere.." when facing some NLP task.


## Fetch anything
If attached resources will not be enough for you, just start using different resources.

### requests
Python has libraries for anything so this is possible to use available NET resources in your app just having their URL available.




```python
import requests
url = 'https://databricks.com/blog/2018/09/26/whats-new-for-apache-spark-on-kubernetes-in-the-upcoming-apache-spark-2-4-release.html'
resp = requests.get(url)
blog_text = resp.text
blog_text[:200]
```




    '\r\n<!DOCTYPE html>\r\n<html lang="en-US" prefix="og: http://ogp.me/ns# fb: http://ogp.me/ns/fb# video: http://ogp.me/ns/video# ya: http://webmaster.yandex.ru/vocabularies/">\r\n<head>\r\n  <meta charset="UTF'



### RSS
Ready to consume RSS?  With Python, nothing is easier.  You can create an instance of `nltk.Text` having RSS feed as the input.  This is a snippet how it could be done


```python
!pip install feedparser
import feedparser
url = 'http://jvm-bloggers.com/pl/rss.xml'
d = feedparser.parse(url)
title = d['feed']['title']
entries = d['entries']
print("Look ma! I've just parsed RSS from a very nice Polish blogging platform. It has a title %s" % title)
print("And there we go with 5 exemplary entries:")
for entry in entries[:5]:
  print(' > ' + entry.title)
```

    Collecting feedparser
    [?25l  Downloading https://files.pythonhosted.org/packages/91/d8/7d37fec71ff7c9dbcdd80d2b48bcdd86d6af502156fc93846fb0102cb2c4/feedparser-5.2.1.tar.bz2 (192kB)
    [K    100% |████████████████████████████████| 194kB 7.2MB/s 
    [?25hBuilding wheels for collected packages: feedparser
      Running setup.py bdist_wheel for feedparser ... [?25l- \ | / - done
    [?25h  Stored in directory: /root/.cache/pip/wheels/8c/69/b7/f52763c41c5471df57703a0ef718a32a5e81ee35dcf6d4f97f
    Successfully built feedparser
    Installing collected packages: feedparser
    Successfully installed feedparser-5.2.1
    Look ma! I've just parsed RSS from a very nice Polish blogging platform. It has a title JVMBloggers
    And there we go with 5 exemplary entries:
     > Odpowiedź: 42
     > Thanks for explaining the behaviour of dynamic (partition overwrite) mode.
     > Non-blocking and async Micronaut - quick start (part 3)
     > Strefa VIP
     > Mikroserwisy – czy to dla mnie?
    

### cleaning
When your HTML doc is fetched you probably have a doc that is full of HTML mess and there is no added value of having `<body>` in your text.  So there is some clean-up work that can be done and there are tools that can make it happen, but they are not part of nltk package.  So let us have BeautifulSoap as an example.


```python
from bs4 import BeautifulSoup
soup = BeautifulSoup(blog_text)
content = soup.find("div", {'class':"blog-content"})
text_without_markup = content.get_text()[:100]
text_without_markup
```




    '\n\n\nWhat’s New for Apache Spark on Kubernetes in the Upcoming Apache Spark 2.4 Release\n\n\nSeptember 26'



## Normalization - languages are not easy
Your language is not easy.  If you are Polish like me, it is soooo true.  But even English and other European languages add complexity to NLP.  Why?  Words have different forms and we have to conform grammar rules to be respected when analyzing text by the machine.  Taking English `going` word as an example, you mean `go` verb, but there is also its lemma `-ing` that has to be recognized and skipped for the moment of analysis.  What are 3 processes that have in-built support in NLTK?  Read the following.

### Tokenization
Text consists of sentences and these contain words.  Often times, we would like to have words presented as vectors as we will apply some algebra to that.  The simplest approach of tokenization can be implemented as follows, however, there are some limitations.  You can use a variety of *tokenizers* available in `nltk.tokenize` [package](https://www.nltk.org/api/nltk.tokenize.html). 


```python
# write tokenizer yourself?
import re
text = "Two smart coders are coding very quickly. Why? The end of the sprint is coming! The code has to be finished!"
tokens_manual = re.split(r"[\s+]", text)
print("Tokens taken manually %s " % tokens_manual)


# or maybe choose the one from the abundance in `nltk`
import nltk
from nltk.data              import load
from nltk.tokenize.casual   import (TweetTokenizer, casual_tokenize)
from nltk.tokenize.mwe      import MWETokenizer
from nltk.tokenize.punkt    import PunktSentenceTokenizer
from nltk.tokenize.regexp   import (RegexpTokenizer, WhitespaceTokenizer,
                                    BlanklineTokenizer, WordPunctTokenizer,
                                    wordpunct_tokenize, regexp_tokenize,
                                    blankline_tokenize)
from nltk.tokenize.repp     import ReppTokenizer
from nltk.tokenize.sexpr    import SExprTokenizer, sexpr_tokenize
from nltk.tokenize.simple   import (SpaceTokenizer, TabTokenizer, LineTokenizer,
                                    line_tokenize)
from nltk.tokenize.texttiling import TextTilingTokenizer
from nltk.tokenize.toktok   import ToktokTokenizer
from nltk.tokenize.treebank import TreebankWordTokenizer
from nltk.tokenize.util     import string_span_tokenize, regexp_span_tokenize
from nltk.tokenize.stanford_segmenter import StanfordSegmenter
from nltk.tokenize import word_tokenize

nltk.download('punkt')
tokens = word_tokenize(text)
print(tokens)
```

    Tokens taken manually ['Two', 'smart', 'coders', 'are', 'coding', 'very', 'quickly.', 'Why?', 'The', 'end', 'of', 'the', 'sprint', 'is', 'coming!', 'The', 'code', 'has', 'to', 'be', 'finished!'] 
    [nltk_data] Downloading package punkt to /root/nltk_data...
    [nltk_data]   Unzipping tokenizers/punkt.zip.
    ['Two', 'smart', 'coders', 'are', 'coding', 'very', 'quickly', '.', 'Why', '?', 'The', 'end', 'of', 'the', 'sprint', 'is', 'coming', '!', 'The', 'code', 'has', 'to', 'be', 'finished', '!']
    

### Stemming
So one of the tasks that can be done is *stemming*, so getting rid of the words ending.  Let us see what does the popular *stemmer* does to the text we already tokenized before.


```python
porter = nltk.PorterStemmer()
tokens_stemmed = [porter.stem(token) for token in tokens]
print(tokens_stemmed)
```

    ['two', 'smart', 'coder', 'are', 'code', 'veri', 'quickli', '.', 'whi', '?', 'the', 'end', 'of', 'the', 'sprint', 'is', 'come', '!', 'the', 'code', 'ha', 'to', 'be', 'finish', '!']
    

### Lemmatization
If stemming is not enough, there has to be *lemmatization* done, so your words can be classified against the real dictionary.  Following there is an example of running this for our text sample.


```python
nltk.download('wordnet')
wnl = nltk.WordNetLemmatizer()
lemmas = [wnl.lemmatize(token) for token in tokens]
print(lemmas)
```

    [nltk_data] Downloading package wordnet to /root/nltk_data...
    [nltk_data]   Unzipping corpora/wordnet.zip.
    ['Two', 'smart', 'coder', 'are', 'coding', 'very', 'quickly', '.', 'Why', '?', 'The', 'end', 'of', 'the', 'sprint', 'is', 'coming', '!', 'The', 'code', 'ha', 'to', 'be', 'finished', '!']
    

## Summary
So where did we go?  I have just analyzed NLKT book available online, chapters no 2 and 3.  I gave a try for few tools from the big number available in this Natural Language Toolkit.  Now there is a time to explore other chapters over there.  Stay tuned.  I have to build my *Suggestion tool*.

All of these was created as a Jupyter notebook.