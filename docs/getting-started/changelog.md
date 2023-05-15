# Changelog

## 0.6.0

* Moved Prompt and OutputParser to LanguageModelChain namespace
* Moved TextSplitter and Tokenizer to DocumentLoader namespace
* Refactored chains for easier usage and clarity
* Removed superfluous event dispatching logic
* Switched to twig-based prompt templates
* LanguageModelChain now always has an output parser
* Refactored agents for easier usage and clarity
* Added OpenAI completions model

## 0.5.1

* Fix incorrect prompt template value replacement

## 0.5.0

* Extracted common data structures to contracts for interoperability
* Reorganised namespaces
* Added VectorStore component
* Added Retriever component
* Added LanguageModelChain factory and initial blueprints
* Added ConcatenateDocuments Chain
* Added DocumentsRetrieverChain
* Added CsvFileLoader

## 0.4.0

* Added Compare component

## 0.3.1

* Cleaned up library dependencies

## 0.3.0

* Lots of coding style fixes
* Renamed Vexo\Model to Vexo\LanguageModel
* Made FinishedGeneratingCompletion event have the whole response instead of just completions
* Added Vexo\LanguageModel\BaseLanguageModel to implement custom models
* Removed unneeded Vexo\LanguageModel\Parameters class
* Added first version of embeddings
* Added Tokenizer abstraction
* Simplified TextSplitters
* Added TokenTextSplitter that can split text by tokens

## 0.2.0

* FakeLanguageModel now also implements EventDispatcherAware for interoperability
* RegexOutputParser now has default prompt instructions
* Chain\Input and Chain\Output are now complete collection classes
* Added ability for WebTextChain to auto discover HTTP client
* Allow for injection of a custom TextExtractor in WebTextChain
* Added ability to override the default generated cache key prefix to CachingChain

## 0.1.0

* Initial release.
