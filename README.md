# GerDaLIR

The **Ger**man **Da**taset for **L**egal **I**nformation **R**etrieval (GerDaLIR) is a legal information retrieval dataset comprising a large collection of documents, passages and relevance labels. The large amount of training data we provide enables GerDaLIR to be used as a downstream task for German or multilingual language models. The task provided is a precedent retrieval task based on case documents from the open legal information platform [Open Legal Data](http://openlegaldata.io/). Relevance labels are derived from references: If a passage contains a reference to one or more available documents, the passage is used as a query while the referenced cases are labelled as relevant.
 
More information about the dataset and its generation can be taken from the official [GerDaLIR paper](https://aclanthology.org/2021.nllp-1.13/) 


## Download

All files are formatted with tab-separators (*.tsv) and packed with gzip. Some files are packed to tarballs and compressed (\*.tar.gz). Sizes specified refer to decompressed size. Details about the individual Files can be found in the [dataset details section](#dataset-details)


### Essential Files

The collection, the queries and the relevance labels can be downloaded with the links in the table below.

| Filename               | Description                          | Num Records | Size    | Format                  |
|------------------------|-------------------------------------:|------------:|--------:|------------------------:|
| [`collection.tsv.gz`](http://lavis.cs.hs-rm.de/storage/gerdalir/data/collection.tsv.gz)    | Collection - one passage per line    | 3.095.383   | 2.0 GB  | `d_id, passage`         |    
| [`queries.tar.gz`](http://lavis.cs.hs-rm.de/storage/gerdalir/data/queries.tar.gz)       | Queries - train, dev and test        |   122.975   | 127 MB  | `q_id, query`           |   
| [`qrels.tar.gz`](http://lavis.cs.hs-rm.de/storage/gerdalir/data/qrels.tar.gz)         | Labels - train, dev and test         |   144.324   | 1.7 MB  | `q_id, d_id`            | 


### Optional Files

Beneath the essential files, also a bunch of optional files can be downloaded. Among them is a file that maps document-ids back to the original file numbers (see [details](#reference-number-map)) . For convenience, we also provide passage-wise and document-wise BM25 rankings (candidates) that can be used for training or re-ranking. Passage-wise candidate files contain the whole passages as text, while document-wise candidate files only specify document-ids.
  

| Filename               | Description                          | Num Records | Size    | Format                  |
|------------------------|-------------------------------------:|------------:|--------:|------------------------:|
| [`refmap.tsv.gz`](http://lavis.cs.hs-rm.de/storage/gerdalir/data/filemap.tsv.gz)        | Doc-Ids to reference number          |   131.446   | 6.2 MB  | `d_id, slug, file`      | 
| [`pass-candidates.train.tsv.gz`](http://lavis.cs.hs-rm.de/storage/gerdalir/data/pass-candidates.train.tsv.gz)        | Top-1000 _passage_ candidates with text (train)   |  98.380.000    | 85 GB  | `q_id, d_id, rank, text`      | 
| [`pass-candidates.dev.tsv.gz`](http://lavis.cs.hs-rm.de/storage/gerdalir/data/pass-candidates.dev.tsv.gz)        | Top-1000 _passage_ candidates with text (dev)  |   12.297.000   | 11 GB  | `q_id, d_id, rank, text`      | 
| [`pass-candidates.test.tsv.gz`](http://lavis.cs.hs-rm.de/storage/gerdalir/data/pass-candidates.test.tsv.gz)        | Top-1000 _passage_ candidates with text (test)   |  12.298.000 | 11 GB  | `q_id, d_id, rank, text`      | 
| [`doc-candidates.train.tsv.gz`](http://lavis.cs.hs-rm.de/storage/gerdalir/data/doc-candidates.train.tsv.gz)        | Top-1000 _document_ candidates (train)   |  98.380.000    | 1.5 GB  | `q_id, d_id, rank`      |
| [`doc-candidates.dev.tsv.gz`](http://lavis.cs.hs-rm.de/storage/gerdalir/data/doc-candidates.dev.tsv.gz)        | Top-1000 _document_ candidates (dev)   |  12.297.000     | 189 MB  | `q_id, d_id, rank`      |
| [`doc-candidates.test.tsv.gz`](http://lavis.cs.hs-rm.de/storage/gerdalir/data/doc-candidates.test.tsv.gz)        | Top-1000 _document_ candidates (test)   |  12.298.000   | 189 MB  | `q_id, d_id, rank`      |



## Dataset details

### Pre-processing

All documents have been pre-processed with the goal to remove all text that is not natural language and remove hints that neural models might exploit. This includes html markup, margin numbers, references, dates and numbers in general. We parse dates as well as references to statutes and other cases using regular expressions, and replace the occurrences with a *[DATE]* or *[REF]* token respectively. Braced contents are removed (including the braces), which in the most cases are comprehensive reference descriptions. All remaining numbers are replaced by zeros.

### Collection   

The collection file contains potentially relevant documents split into passages. Note that passages have no own passage-ids assigned to them. Each line represents one passage and begins with the corresponding document-id (d_id). For document-level retrieval, all passages with the same document-id must be concatenated. 
```
1       Das Zulassungsvorbringen der Klägerin begründet keine ernstlichen Zweifel an der Richtigkeit des angefochtenen Urteils . Zweifel in diesem Sinn si..
1       Daran fehlt es hier. Die Antragsbegründung, wonach die Klägerin entgegen den Ausführungen im angefochtenen Urteil zuverlässig sei und die Urteil...
1       In der Antragsbegründung fehlt es an jeglichen Ausführungen zu der ausführlichen Würdigung des Verwaltungsgerichts, die sich im Einzelnen mit de...
2       Tenor Auf die Beschwerden der Antragsteller wird der Beschluss des Verwaltungsgerichts Göttingen 0. Kammer vom [DATE] geändert. Die Antragsgegneri..
2       Durch Beschluss vom [DATE] , auf den wegen der Einzelheiten des Sachverhalts und der Begründung Bezug genommen wird, hat das Verwaltungsgericht den...
```

### Queries

Queries are passages that referenced one or more collection documents. There is one query file each for training, development and testing. Query ids (q_id) are assigned globally so that the query files could be concatenated without any problem.

```
2       Nach [REF] ist eine Erlaubnis zu widerrufen, wenn nachträglich bekannt wird, dass die Voraussetzung nach § 0 Nummer 0 nicht erfüllt ist. Gem...
3       Erforderlich ist mithin eine Prognoseentscheidung unter Berücksichtigung aller Umstände des Einzelfalls dahingehend, ob der Betreffende wille...
4       [REF] ist in Reaktion auf das Urteil des Schleswig-Holsteinischen Landesverfassungsgerichts neu gefasst worden, vor dem Hintergrund, dass sich...
5       Die streitgegenständliche Satzung gibt nicht die Rechtsvorschriften an, welche zum Erlass der Satzung berechtigen, [REF] . Dies ist aber insbe...
6       Insofern gehört zur zutreffenden Angabe der zum Erlass der Satzung berechtigenden Rechtsvorschriften im Sinne des [REF] nicht nur die genaue A...
```

### Qrels

The relevance labels to the queries are also split into sets for training, development and testing. To each query at least one relevance label exist. Multiple relevance labels for a single query result in multiple lines with one target document-id each.

```
2       118149
3       72511
4       74503
5       4240
5       72939
```

### Reference Number Map

To each assigned document-id, we provide the corresponding _Open Legal Data_ slug and the official file number. With the slug, the original case document can directly be accessed on the Open Legal Data website as follows: [https://de.openlegaldata.io/case/\<slug\>](https://de.openlegaldata.io/case/ovgnrw-2020-11-03-4-a-236320).

```
1       ovgnrw-2020-11-03-4-a-236320                        4 A 2363/20
2       ovgni-2020-11-03-2-nb-25120                         2 NB 251/20
3       vg-regensburg-2020-11-03-ro-12-k-192080             12 K 19/2080
4       ovgnrw-2020-11-03-18-a-102119                       18 A 1021/19
5       vg-schleswig-holsteinisches-2020-11-03-1-b-12520    1 B 125/20
```

## Benchmarking with GerDaLIR

As described in the paper, we take two types of ranking modalities into account: passage-wise and document-wise ranking. In the passage-wise ranking setting, each passage is mapped to the document it originates from. Thus, in a ranking of passages, multiple ranks may refer to the same document. To cast this to a regular ranking of documents, we discard all passages that refer to a document that was listed at a higher rank. This is equivalent to score max-pooling.

### Baselines

We provide several baselines on our dataset. With Mode "P" and "D" we refer to passage-wise and document-wise ranking as described above. More details to the baseline methods can be found in our [paper](.).

<!--- There, we perform retrieval in two modalities: document-wise and passage-wise retrieval. For Document-wise retrieval, we concat all passages belonging to a document to from the passages back to documents. Passage-wise retrieval uses the passages as items to be retrieved. In the ranking, only the first source document occurence counts so that every document that was refered to on a higher rank, is ignored. This is equivalent to score max-pooling along the document ids. -->


| Method                                     | Mode | MRR@10         | nDCG@20        | Recall@100     | Recall@1000    |
|--------------------------------------------|------|----------------|----------------|----------------|----------------|
| TF-IDF                    | P    | 0.333          | 0.375          | 0.651          | 0.768          |
|                                            | D    | 0.336          | 0.386          | 0.701          | 0.809          |
| BM25  (k1=1.20, b=0.75) | P    | 0.365          | 0.409          | 0.693          | 0.800          |
|                                            | D    | 0.386          | 0.434          | 0.734          | 0.827          |
| BM25 tuned (k1=0.51, b=0.72)         | P    | 0.372          | 0.417          | 0.703          | 0.803          |
| BM25 tuned (k1=0.90, b=0.98)         | D    | 0.391          | 0.439          | 0.737          | __0.829__ |
| WCS - GloVe              | P    | 0.242          | 0.278          | 0.539          | 0.695          |
|                                            | D    | 0.134          | 0.166          | 0.420          | 0.625          |
| WCS - fastText          | P    | 0.257          | 0.295          | 0.582          | 0.726          |
|                                            | D    | 0.153          | 0.188          | 0.468          | 0.668          |
| Neural Re-ranking - BERT                  | P    | 0.416          | 0.465          | __0.745__ | 0.789          |
| Neural Re-ranking - ELECTRA               | P    | __0.436__ | __0.481__ | __0.743__ | 0.789          |


## How do I cite this work?

If you use this dataset for your research, please consider citing our Paper: 

```
@inproceedings{wrzalik-krechel-2021-gerdalir,
    title = "{G}er{D}a{LIR}: A {G}erman Dataset for Legal Information Retrieval",
    author = "Wrzalik, Marco  and
      Krechel, Dirk",
    booktitle = "Proceedings of the Natural Legal Language Processing Workshop 2021",
    month = nov,
    year = "2021",
    address = "Punta Cana, Dominican Republic",
    publisher = "Association for Computational Linguistics",
    url = "https://aclanthology.org/2021.nllp-1.13",
    pages = "123--128",
    abstract = "We present GerDaLIR, a German Dataset for Legal Information Retrieval based on case documents from the open legal information platform Open Legal Data. The dataset consists of 123K queries, each labelled with at least one relevant document in a collection of 131K case documents. We conduct several baseline experiments including BM25 and a state-of-the-art neural re-ranker. With our dataset, we aim to provide a standardized benchmark for German LIR and promote open research in this area. Beyond that, our dataset comprises sufficient training data to be used as a downstream task for German or multilingual language models.",
}
```

## Related links

 - [awesome-legal-data](https://github.com/openlegaldata/awesome-legal-data)
 - [German-NLP](https://github.com/adbar/German-NLP)
 - [FIRE IRLeD](https://sites.google.com/view/fire2017irled)
 - [COLIEE 2020](https://sites.ualberta.ca/~rabelo/COLIEE2020/)
 - [SigmaLaw](https://osf.io/qvg8s/)
