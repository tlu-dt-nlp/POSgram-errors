# POS-gram Error Finder

The tool helps to find unlikely word sequences in Estonian texts based on the words' part-of-speech (POS). It converts each sentence to a POS string and detects potential errors by looking at the usage context of trigrams, i.e., three-word sequences. The probability of a preceding or subsequent context (either a certain POS, the beginning or end of a sentence) is estimated using a statistical model which is based on the Estonian Reference Corpus (the version available as a subcorpus of the [Estonian National Corpus](https://metashare.ut.ee/repository/browse/estonian-national-corpus-2021-vert/f176ccc0d05511eca6e4fa163e9d454794df2849e11048bb9fa104f1fec2d03f/)). To build another model, the [POS-gram Context Finder](https://github.com/tlu-dt-nlp/POSgram-contexts) tool can be applied.

## Usage
Usage examples can be found in the Python script `example.py`, and Colab files `error_finder_demo_en.ipynb` and `error_finder_demo_et.ipynb`.

The [Stanza NLP package](https://stanfordnlp.github.io/stanza/) is applied for POS tagging. Language-specific ('XPOS') tags are used to group words into POS n-grams, or POS-grams. Custom tags are used to denote sentence onset and ending. The tagset is explained in the following table.
| Tag | Meaning |
|:--------------:|:-------------:|
| ^ | Sentence onset |
| A | Adjective |
| D | Adverb |
| G | Genitive attribute |
| I | Interjection |
| J | Conjunction |
| K | Adposition |
| N | Numeral |
| P | Pronoun |
| S | Substantive/Noun |
| V | Verb |
| X | Auxiliary adverb |
| Y | Abbreviation |
| Z | Punctuation |
| $ | Sentence ending |


In the current version, punctuation is skipped when analysing word sequences. It means that words in a trigram or their preceding/subsequent words may not be adjacent. For example, 'SZSZS' is considered an instance of the trigram 'SSS', however, punctuation marks are shown in the POS-gram representation of the sentence (e.g., '^DVDSZSZSZ$') and potential error span that covers the trigram and its context (e.g., 'DSZSZS').

By default, a trigram context is detected as low-probability if its relative frequency is <5% in the language model. This value can be changed using the `lower_percentage_limit` argument:
```
PosgramFinder(lower_percentage_limit=2.5)
```

## Evaluation

Although a rare POS sequence does not always indicate an error, POS-gram analysis is useful in pointing out word order, word choice, unnecessary and missing word errors. The tool has been evaluated on the [EstGEC-L2](https://github.com/tlu-dt-nlp/EstGEC-L2-Corpus) test set. Its error detection precision was **75%**, taking into account the number of unlikely POS-grams that fully or partly overlap with gold standard error annotation. Error detection recall was **39.2%**, considering all annotated error types, and the F0.5 score **63.4%**. Reducing the low-probability boundary increases precision but decreases recall.

Precision can be improved by exploring which POS-grams indicate errors more reliably and which should be disregarded. Qualitative analysis of the test results along with the most probable POS combinations revealed by the statistical model can inform the development of a correction suggestion functionality. Including punctuation marks in the analysis can also be beneficial.

## References

Associated conference presentation: [20th Annual Conference of Applied Linguistics, April 27-28, 2023, Tallinn](https://www.rakenduslingvistika.ee/wp-content/uploads/2023/05/03_Eslon_Kippar_Keelekasutusmustrid_kontekstid1.pdf) (in Estonian)
