# ES-autocomplete
ES index that serve autocomplete needs with leveraging typos and errors.

- `docker-compose up -d`
- Run query for store index
```
curl --location --request PUT 'http://localhost:9200/my_index/' \
--data-raw '{
   "settings":{
      "analysis":{
         "filter":{
            "trigrams_filter":{
               "type":"ngram",
               "min_gram":3,
               "max_gram":3
            }
         },
         "analyzer":{
            "trigrams":{
               "type":"custom",
               "tokenizer":"autocomplete",
               "filter":[
                  "lowercase"
               ]
            },
            "autocomplete_search":{
               "tokenizer":"lowercase",
               "filter":[
                  "lowercase",
                  "trigrams_filter"
               ]
            }
         },
         "tokenizer":{
            "autocomplete":{
               "type":"edge_ngram",
               "min_gram":2,
               "max_gram":20,
               "token_chars":[
                  "letter",
                  "digit"
               ]
            }
         }
      }
   },
   "mappings":{
      "properties":{
         "title":{
            "type":"text",
            "analyzer":"trigrams",
            "search_analyzer":"autocomplete_search"
         }
      }
   }
}'
```
attention to `trigrams`, `autocomplete_search` analyzer.

- make document

`curl --location --request PUT 'http://localhost:9200/my_index/_doc/1' \
--header 'Content-Type: application/json' \
--data-raw '{
"title": "Elasticsearch is a search engine"
}'`

- example of search queries

We made 3 errors in word `Elasticsearch`


