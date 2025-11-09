#### Introduction 
- Elastic search is full text search engine. Which store and analyze data. 
- Handles large volumes of data across multiple servers. 
- Support RESTful AIP to integrate with different systems. 

- Elastic Search Installation 
	- [Stand alone machine](https://linuxcapable.com/how-to-install-elasticsearch-8-on-ubuntu-linux/) 


#### Concepts 
- **Index**: is Logical namespace for storing data which have similar characteristics. or collection of documents
- **Documents:** Things which we searching for (*text, videos, and images*). 
	Exmaple 
	```json 
	{
	  "_index": "my-first-elasticsearch-index",
	  "_id": "DyFpo5EBxE8fzbb95DOa",
	  "_version": 1,
	  "_seq_no": 0,
	  "_primary_term": 1,
	  "found": true,
	  "_source": {
	    "email": "john@smith.com",
	    "first_name": "John",
	    "last_name": "Smith",
	    "info": {
	      "bio": "Eco-warrior and defender of the weak",
	      "age": 25,
	      "interests": [
	        "dolphins",
		        "whales"
      ]
	    },
	    "join_date": "2024/05/01"
	  }
	}
	```

- Every Index contain Data and meta Data. The following are the most important meta data 
	1. `_source` contains the original json document. 
	2. `_idex` the name of index where the document is sotred. 
	3. `_id` the document's ID. 


- **Mapping:** Each Index has mapping or schema for how fields in your documents are indexed. 
	- Types of Mapping 
		1. *Dynamic mapping* Elasticsearch automatically detect the data types and create the mappings for you. 
		2. *Explicit mapping* Defined up front by specifying data types of each field. 


- <span style="color:rgb(255, 0, 0)">TF-IDF</span> Term Frequency - Inverse Document Frequency
	1. *Term Frequency*: Indicate how many times this term appear in the document. 
	2. Inverse Document Frequency: Indicate that this term is <span style="color:rgb(0, 255, 0)">common word</span> or not. For example if this word is "The" this mean that this word is not important because it exist many times in all document. 

- **Tokenizer and analyzer:** Core Component of text processing and play essential roles in how data is indexed and searched. 
	- Analyzer 
	  Text Processing pipeline that breaks down text into terms or tokens that elastic search can index 
	  include the following 
		1. **character filters:** Modify the text at the character level. 
		2. **Stemming:** is a process in natural language processing (NLP) used to reduce words to their root or base form. 
		3. synonyms: are different words that have the same or similar meanings. 
		   
	- Tokenizer 
	  use for intial step of splitting text into tokens. 
	  
  Example, suppose we want to index the following text 
	```txt 
	"The quick brown fox jumps ove the lazy dog."
	```
- Step 1: Define the Analyzer
	let's say we define an analyzer with: 
	1. Lowercase filter to make all tokens lowercase. 
	2. whitespace tokenizer that splits  the text base on spaces 
	When apply this analyzer to the text the following happens 
	3. Tokenizer (whitesapce) splits the text into tokens by spaces: 
	```txt 
	"The", "quick", "brown", "fox", "jumps", "over", "the", "lazy", "dog."
	```
	4. Token filter (lowercae) converts each token to lowercase: 
	```txt 
	"the", "quick", "brown", "fox", "jumps", "over", "the", "lazy", "dog."
	```


- **Aggregations** The ability to group and perform calculations and statistics (sums, averages) on you data by using simple query 

  ![](precision-1.png)
    ![](precision-2.png)

- **Inverted Index:** Map each unique (term) to all documents to list of documents where that term appear. 
  ![](invertedindex.png)
  <span style="color:rgb(255, 0, 0)">Why its name is "inverted"? </span> 
  In a normal index, the term is associated to document (i.e., the term is inside the document). in the inverted index, the relationship is reversed it maps the term to the document. 
  
#### Hands on
##### Shakespear Plays 
 
- Get the status of elastic search 
	```bash 
	curl -XGET localhost:9200
	```

- Download shakespear  mapping 
	```bash 
	 sudo wget http://media.sundog-soft.com/es8/shakes-mapping.json
	```

- Add mapping to elastic search 
	```bash 
	curl -H "Content-Type: application/json" -XPUT localhost:9200/shakespeare --data-binary @shakes-mapping.json
	```

- Download shakespaer content 
	```bash 
	sudo wget http://media.sundog-soft.com/es8/shakespeare_8.0.json
	```
- Add Data to index 
	```bash 
	curl -H "Content-Type: application/json" -XPUT localhost:9200/shakespeare/_bulk --data-binary @shakespeare_8.0.json
	```

#### MoviLenss 

Is a free dataset of movie ratings gathered from movilens.org. It contains user ratings movie metadata, and user metadata 

- Create Alias for curl command 
	```bash 
	alias curl='curl -H "Content-Type: application/json" "$@"'
	```

- Create Mapping 
	```bash 
	curl -XPUT 127.0.0.1:9200/movies -d '
	{
	    "mappings": {
	        "properties": {
	            "year": {
	                "type": "date"
	            }
	        }
	    }
	}'
	
	```

- To check that mapping 
	```bash
	curl -XGET localhost:9200/movies/_mapping
	```
- Add Intersteller movie 
	```bash 
	curl -XPUT localhost:9200/movies/_doc/109487?pretty -d '
	{
	  "genre": ["IMAX", "Sci-Fi"],
	  "title": "Interstellar",
	  "year": 2014
	}'
	
	```

- To check that 
	```bash
	curl -XGET localhost:9200/movies/_search?pretty
	```
- Download the samples json file of movies 
	```bash 
	wget http://mdeia.sundog-soft.com/es8/movies.json
	```

- Add Movies to cluster 
	```bash 
	curl -XPUT localhost:9200/_bulk?pretty --data-binary @movies.json
	```
- Updating Document which already has been indexed. 
  Elastic search Document is immutable you can't simple change it. 
  Every document has a `_version` field. 
  New document is created with an incremented `_version`. 
	```bash
	curl -XPOST localhost:9200/movies/_update/109487 -d '
	{
	  "doc": {
	    "title": "Interstellar next"
	  }
	}'
	
	```

- Deleting Data in elastic search 
	```bash
	curl -XDELETE localhost:9200/movies/_doc/58559?pretty
	```
#### Index Life cycle managemnt [ILMA]
Automate the management of index lifecycel by applying policies that define how index transitions through different phases. This helps optimize performance, storage, and resource utilization.

 **ILM Phases:**
1. *Hot Phase* 🔥  Index is actively written and queried.
2. *Warm Phase* 🌡️ Data is **read-only**, optimized for searches.
3. Cold Phase ❄️ Data is stored in cheaper storage, queried less frequently.
4. Delete Phase🗑️  Index is deleted to free up space.