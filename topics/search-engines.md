# Building a search engine
- processes and metrics for evaluation and tuning into the product and development cycles is key
- processes 
- metrics
- technologies

## Define the problem
### Functional
- corpus
  - media: img, txt, graph, geo, movie
  - control: in-house data, third party data, adversarial data
  - quality: cleanup/scrubbing needed, correlation needed, ready to parse, structured
  - languange
    - unilingual, multilingual
    - unicode

- queries
  - language
    - structured, unstructured
    - unilingual, multilingual
  - type: textual, image based, sound based, street, id, face
  - context: personalized, query history, location, time of day
  - autosuggest

- reponse
  - media
  - language
  - context

### Non functional
- corpus:
  - size
  - digestion speed: batch, real-time indexing
  - compliance: organizational limitations
- query:
  - logging
  - history
- response:
  - serving latency
  - access control: public, restricted subset

## Search pipeline
- DAG of subsystems
- information edge: source=upstream, arrow=downstream
- consider: upstream change propagates to downstream?

### Index selection
- include only most important document as search result to the index

#### Document filtering
##### Spamming (spamdexing)
- Taxonomy: http://airweb.cse.lehigh.edu/2005/gyongyi.pdf
- artificially boost relevance and importance of web pages
- *relevance score*: textual similarity between query and page 
- *importance score*: query independent page popularity
- *ranking score*: comibned relevance and importance score

###### Boosting
- techniques to game relevance (tfidf) and importance (page ranke) measuring algorithms
[Boosting](../img/spamdexing-boosting-techniques.png)
- term spamming: tailor html text field content
- 

###### Hiding
- techniques to hide marks of boosting

##### Others
- undesirable document - similar to spam
- duplicate detection - http://infolab.stanford.edu/~ullman/mmds/ch3.pdf
  - locality sensitive hashing
  - similarity measures
  - clustering 
  - clickthrough data
- low-utility document
  - utility function
  - learn from user behavior

### Index construction
- inverted index - search term to document mapping
- posting list - documents for a search term
- term extraction - NLP techniques
  - stop words
  - stemming
  - entity extraction
  - computer vision
- metadata mining
  - references to other docs
  - topics
  - term frequency
  - entities
  - size
- format
  - posting list compression
  - mmap()able data
  - Log Structured Merge Tree (LSM-tree)

### Query analysis
- non structured - NLP based extraction
- term reweighting
- spell checking - historical query log dictionary
- synonym matching
- named entity recognition 
  - hidden markov model language modeling
- query classification
  - dispatches corpora lookup
- personalization

### Document retrieval

### Ranking
- signals
  - page rank
  - statistical language model output
- hand tuned
- supervised learning
  - signal based discriminative
  - McRank
  - LambdaRank
  - MatrixNet
- vector space based approach
  - transform corpus and queries into same vector space
  - use distance metric for ranking and retrieval

## Index pipeline
- batched
- streamed
- layered/mixed
  - frequently changing pages - streamed
  - non frequently changing - batched
- multiple index types
  - shard
  - media
  - indexing cadence (fresh vs long-term)

## Serving system
- scatter gather responses of different systems

### Latency requirements
- 300ms slower = 0.6% less search
- recommended: sub 200ms serving time

### Caching
- become stale (blacklist, index update)
- after purge: prewarm needed
- sizing
- replacement algorithms

### Availability
- uptime/(uptime+downtime)
- if one shard would fail serving a query, the whole query would fail

## Quality control
- hardest among all problems: continuous search quality evaluation and improvement

### Quality metrics
#### What to measure
- self reported user satisfaction
- perceived result relevance
- satisfaction vs competitors
- relative performance to previous version
- user engagement
#### How to quantify
|                |Expected       |Unexpected    |
|----------------|---------------|--------------|
|Retrieved(+)    |TruePositive   |FalsePositive |
|Not retrieved(-)|FalseNegative  |TrueNegative  |

- Precision=#TruePositive/#Retrieved
- Recall=Sensitivity=#TruePositive/#Expected (100% recall = return all documents)
- FallOut=#FalsePositive/#Unexpected (0% fallout = return no documents)
- F1=2*precision*recall/(precision+recall)
- Fbeta=(1+beta)*precision*recall/(beta*beta*precision+recall)
- P@N=Precision at N (N=displayed docs on first page)
- AveragePrecision=IntegralOf(PrecisionFunctionGiven(recallAt()))
- MeanAveragePrecision=sum{q in Q queries| AveragePrecision(q)}/|Q|
- NormalizedDiscountedCumulativeGain=nDCG
- LongAndShortClicks

##### Human rating
- user satisfaction ranking
- comparative evaluation (vs previous)
- golden set = manual query set + marked relevant answers
- document partial ordering based on user input
#### A/B testing
