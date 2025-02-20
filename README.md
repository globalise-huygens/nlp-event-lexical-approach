# Lexical event extraction for GLOBALISE

This document serves as documentation on the lexical approach to event extraction for GLOBALISE that was run on the complete corpus in February 2025. This is the first release of our lexical approach. Updates may follow.

## Preliminaries

The lexicon is built to fit within the event model of GLOBALISE. This means we identify words that relate to one of the event classes defined within GLOBALISE’s event ontology.

The current lexical approach consists of a simple token-type matching through string matching, i.e., everytime the word “aenkomst” appears, we link it to its corresponding event class Arriving. As mentioned, the event types we link to are those defined for event detection for GLOBALISE. These are a little over 80 event types. For a list of all the event types and their definition, see our [wiki](https://github.com/globalise-huygens/nlp-event-detection/wiki). For the motivation behind the choice of event types and see our [paper](https://ceur-ws.org/Vol-3558/paper3332.pdf). 


The lexicon is limited in three ways. Firstly with intent, because we only want to include token-type pairs of which we are fairly sure they are constant. This means we try not to include ambiguous tokens in the lexicon. Secondly without intent: we only include token-type pairs that we have come across in our creation method (Section 3) and this definitely does not cover all possible token-type pairs corresponding to our event type selection. Also, we try to cover as many spelling variations as possible, but we realise these are not complete. Any feedback on the lexicon and ideas about what should be added in a next version are therefore always very welcome. Thirdly, the current version of the lexicon only matches single tokens to an event type. It does not link “het hazenpad nemen” to Leaving, but it does link “hazenpad”. 

The lexicon was built to serve as a pre-annotation lexicon, which it is also used for within our annotation pipeline at the moment. That means we use it to automatically annotate documents before distributing them to human annotators. We do this to relieve annotation effort. Human annotators can correct pre-annotations. 

## Creation

The lexicon was created through an iterative process of annotation analysis, expert input and feedback and synonym / spelling variation search through using a specialized word2vec model. We will go through the creation of each version of the lexicon, leading up to this release. 

### 3.1 Lexicon_v1

The first version of the lexicon was a combination of (manual) expert input and automatic analysis of annotation round 2

#### Expert input:
Based on experience with the corpus, experts provided strings (words) and the event class they refer to

#### Automatic analysis of annotation round 2
We extracted strings (words) that were annotated at least 2 times with the same 	event class. These pairs were evaluated manually and added to the lexicon.

### 3.2 Lexicon_v2

#### Using a word2vec model
The biggest addition to this version of the lexicon came from an elaborate word2vec search using a word2vec model trained on the VOC corpus. If you do not know what a word2vec model is, see the [Additional material] section for an explanation. I used the first version of the lexicon as a ‘seed’. This means I put in a word from the first lexicon and look for the most similar words according to the word2vec model. I went through them, asking for advice or consulting the WNT when necessary, and added them to Lexicon_v2 if I agreed with the word2vec model. Sometimes I would use words I accidentally came across while doing this task, either through word2vec or through the WNT, again as a new seed. This was the biggest update to the lexicon of all updates leading up to the release.

#### Feedback annotation round 3 (Expert input)
Since we use the lexicon as a pre-annotation step for human annotation, any type 	of token-type pair that has been observed to not fit well, for example because of 	ambiguity, can be deleted. Some token-type pairs were deleted from the lexicon 	because of this. Additionally, I asked annotators to provide suggestions for the 		lexicon that they come across during annotation that were not in the lexicon yet. These were added.

### 3.3 Lexicon_v3

#### Expert feedback
Once again, we evaluated the lexicon after using lexicon_v2 for pre-annotation. Again, some token-type pairs were deleted and some were added. This was a smaller update, but an important one since it was a practical human evaluation of the bigger word2vec update. 

### 3.4 Lexicon_v4 **(this release)**

Small additions such as the addition of spelling variations and token-type pairs that experts came across during their regular work at GLOBALISE were added. This was a very small update.

## Evaluation
The current version of the lexicon was evaluated on the complete training data set we had for event detection in February. This dataset consists of (parts of) 15 documents annotated by three teams of two expert historians. The longest annotated document is 18 pages long and the shortest 1. In total, the set contains 107 handwritten pages/scans. We evaluate the lexical approach by performing 15-fold cross validation (we split the data differently 15 times). Each time we take one document out of the dataset to serve as test data. We then calculate performance of the lexical approach by averaging its performance over these 15 datasplits. We refrain from testing the lexical approach on the test data we set apart since the approach is still under development.

See the table underneath for precision, recall and f1 score of the lexical approach on event trigger detection (no classification). Since the token-type pairs were selected for their lack of ambiguity, we expect classification to show very similar scores with probably a bit less precision. We have yet to verify this officially. 



## Use

### 5.1 General

The lexicon is a first step towards helping you to search for concepts instead of literal words in the transcribed VOC corpus when it comes to events. You can group, for example, on “Destroying”, and thereby search for all these terms in the corpus: 

> verdestrueert; verdestrueren; verdestrueeren; destructie; destrueren; destrueeren; gelicht; demolieren; demolieeren; verwoesting; verwoesten; verwoestingen; verwoestinge; uijtgeplundert; uitplunderen; uijtplunderen; uijtgeplundert; plonderen; geplondert; uijtgeplondert; verdelging; verdelginge; verdelgen; verdelgende; verdelgt; verdelgd; verdelgde

The concepts (i.e., event types) itself are organized in a hierarchy. We therefore know that every instance of “aenkomst” which refers to Arriving also refers to Translocation, because Arriving is a type of Translocation. By doing so, events can be grouped on more or less fine grained levels of semantics. 

### 5.1 Explanation of the columns in Lexicon_release1.csv

_tokens_ - these are the strings looked for in the corpus

_relationtype_ - this is the type of reference the string has to the event type. ‘isOfType’ means there is a direct reference, for example ‘vertrocken’ referring to Leaving and ‘evokes’ means there is an indirect reference, for example ‘diefte’ referring to Getting. 

_label_ - this is the event type the string refers to

_upperclass_1_ - the event type one step above the event type referred to in the GLOBALISE event ontology/taxonomy (see section 7.2)

_upperclass_2_  - the event type two steps above the event type referred to in the GLOBALISE event ontology/taxonomy (see section 7.2)

## Additional materal

### What is a word2vec model?

A word2vec model is a collection of word embeddings: a computational representation of meaningl where words that often appear in the same context in text are clustered together and seen as similar. An example to explain this concept is the following: “I could really use a warm cup of [blank] right now”. As humans, we have an intuition of what should be in the blank space. Something like tea, coffee, hot chocolate. These are all drinkable liquids that humans enjoy to drink at the same temperature, namely hot. These are similar concepts. A word2vec model does not know anything about hot chocolate but it can learn the contexts it is used in and thus infer it is similar to coffee. 

A word2vec model represents semantics that it encounters in its training data. For example, if you would train a word2vec model on text from fashion magazines, it would know the word ‘clutch’ and know it is similar to a tote bag but not to a baret, but if you would train it on cookbooks it would probably not. It is thus relevant on what text you train your word2vec model. The model we used was created by Leon van Wissen, who only trained on the VOC corpus

## The GLOBALISE hierarchical organization of Dynamic Events






