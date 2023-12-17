## DynamoDB Practice

### tools
- Amazon DynamoDB
- Amazon CLI

## commands

#### create table
```dynamo
aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema \
        AttributeName=Artist,KeyType=HASH \
        AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
```

#### insert an item
```dynamo
aws dynamodb put-item \
    --table-name Music \
    --item file://itemmusic.json \
```

#### insert multiplos itens
```dynamo
aws dynamodb batch-write-item \
    --request-items file://batchmusic.json
```

#### create a secondary global index based on the album title
```dynamo
aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions AttributeName=AlbumTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"AlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

#### create a secondary global index based on artist name and album title
```dynamo
aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions\
AttributeName=Artist,AttributeType=S \
AttributeName=AlbumTitle,AttributeType=S \
    --global-secondary-index-updates \
"[{\"Create\":{\"IndexName\": \"ArtistAlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"Artist\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"RANGE\"}], \
\"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

#### create a secondary global index based on song title and year
```dynamo
aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions\
AttributeName=SongTitle,AttributeType=S \
AttributeName=SongYear,AttributeType=S \
    --global-secondary-index-updates \
"[{\"Create\":{\"IndexName\": \"SongTitleYear-index\",\"KeySchema\":[{\"AttributeName\":\"SongTitle\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"SongYear\",\"KeyType\":\"RANGE\"}], \
\"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

#### search item by artist
```dynamo
aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :artist" \
    --expression-attribute-values  '{":artist":{"S":"Emicida"}}'
```

#### search item by artist and songtitle
```dynamo
aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :artist and SongTitle = :title" \
    --expression-attribute-values file://keyconditions.json
```

#### search by secondary index based on album title
```dynamo
aws dynamodb query \
    --table-name Music \
    --index-name AlbumTitle-index \
    --key-condition-expression "AlbumTitle = :name" \
    --expression-attribute-values  '{":name":{"S":"Sobre Crianças, Quadris, Pesadelos e Lições de Casa..."}}'
```

#### search by secondary index based on artist name and album title
```dynamo
aws dynamodb query \
    --table-name Music \
    --index-name ArtistAlbumTitle-index \
    --key-condition-expression "Artist = :v_artist and AlbumTitle = :v_title" \
    --expression-attribute-values  '{":v_artist":{"S":"IEmicida"},":v_title":{"S":"AmarElo"} }'
```

#### search by secondary index based on song title and year
```dynamo
aws dynamodb query \
    --table-name Music \
    --index-name SongTitleYear-index \
    --key-condition-expression "SongTitle = :v_song and SongYear = :v_year" \
    --expression-attribute-values  '{":v_song":{"S":"Hoje Cedo"},":v_year":{"S":"2013"} }'
```
