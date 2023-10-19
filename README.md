Kibana
======

- [Kibana Query Language (KQL)](https://www.elastic.co/guide/en/kibana/current/kuery-query.html)
  - [Lucene](https://www.elastic.co/guide/en/elasticsearch/reference/8.10/query-dsl-query-string-query.html#query-string-syntax)
- [Mucho live data](https://demo.elastic.co/app)
- [Advanced Install](https://www.youtube.com/watch?v=gZb7HpVOges)


## Docker Install

[deviantony/docker-elk](https://github.com/deviantony/docker-elk) docker-compose (minimal)

```
docker-compose up setup
docker-compose up -d
```

Or opt for a [completely configured docker-compose](https://github.com/sherifabdlnaby/elastdocker)



### Manual

```sh
docker network create elastic

# Will output the password for the user "elastic" and the JWT "enrollment token"
docker run --name es01 --net elastic -p 9200:9200 -it -m 1GB docker.elastic.co/elasticsearch/elasticsearch:8.10.3
# /usr/share/elasticsearch/config/elasticsearch.yml

# Start Kibana
docker run --name kib01 --net elastic -p 5601:5601 docker.elastic.co/kibana/kibana:8.10.3
# /usr/share/kibana/config/kibana.yml

# Required for Alerts: -e XPACK_ENCRYPTEDSAVEDOBJECTS_ENCRYPTIONKEY ""
# https://stackoverflow.com/questions/67777260/unable-configure-alerts-and-actions-in-kibana
# xpack.encryptedSavedObjects.encryptionKey: \"7dc1d8151798fcd725e66713e21b4f17\"
# xpack.encryptedSavedObjects:
#  encryptionKey: "min-32-byte-long-strong-encryption-key"
# Generate a key:
# docker exec -it kib01 sh
# bin/kibana-encryption-keys generate
# Also see: https://github.com/elastic/kibana/issues/129671


# Open localhost:5601
# And enter the "enrollment token" as printed to the elasticsearch logs earlier
# Or create a new one (30m timeout)
docker exec -it es01 /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana

# Or reset elastic user pwd
docker exec -it es01 /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic
```

## Kibana

Stack Management > Data Views (Previously: Index Patterns)


### KQL

When querying keyword, numeric, date, or boolean fields,
the value must be an exact match, including punctuation and case.
For non-exact matches use the `*` wildcard.


```
of undefined VS "of undefined"
Also quote to escape \():<>"*
And escape \"

method: *
method: GET

statusCode >= 400


NOT statusCode: 200
OR / AND with ()
method: (GET OR POST OR DELETE)

status\ code: 200
exception.*: orm
_exists_:exception

JSON:
user.name:{ first: "Alice" and last: "White" }
```

### Lucene

- `?` for one random char. `*` for zero or more.
- `/regex/`
- `quikc~ brwn~1 foks~`: fuzzy (default=2)
- `quick^2 fox`: boosting
