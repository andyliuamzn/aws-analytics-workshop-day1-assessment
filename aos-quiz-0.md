## OpenSearch

STEP 1.

```
PUT knn-test-index
{
    "settings": {
        "index": {
            "replication": {
                "type": "DOCUMENT"
            },
            "number_of_shards": "5",
            "knn": "true",
            "number_of_replicas": "1"
        }
    },
    "mappings": {
        "properties": {
        "vectors": {
                "type": "nested",
                "properties": {
                "vector": {
                    "type": "knn_vector",
                    "dimension": 2,
                    "method": {
                    "name": "hnsw",
                    "space_type": "l2",
                    "engine": "faiss"
                    }
                }
                }
            }
        }
    }
}
```

STEP 2.
```
PUT _bulk?refresh=true
{ "index": { "_index": "knn-test-index", "_id": "1" } }
{"vectors":[{"vector":[1,1]},{"vector":[2,2]},{"vector":[3,3]}]}
{ "index": { "_index": "knn-test-index", "_id": "2" } }
{"vectors":[{"vector":[10,10]},{"vector":[20,20]}]}
```

STEP 3.
```
GET knn-test-index/_search
{
  "query": {
    "nested": {
      "path": "vectors",
      "query": {
        "knn": {
          "vectors.vector": {
            "vector": [1,1],
            "k": 2
          }
        }
      }
    }
  }
}
```
