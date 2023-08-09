Instructions from https://github.com/hackolade/docker/tree/main/Studio

# Building the image
docker build --no-cache --pull -f Dockerfile.app -t hackolade:latest .

# Installing the plugins based on Dockerfile.plugins file
docker build --no-cache -f Dockerfile.plugins -t hackolade:latest .


# Example commands

## Generate documentation in html format

### For Neo4j
docker compose run --rm hackoladeStudioCLI genDoc --model=/home/hackolade/Documents/data/neo4j.hck.json --format=html --doc=/home/hackolade/Documents/data/neo4jdoc.html

### For Mongo
docker compose run --rm hackoladeStudioCLI genDoc --model=/home/hackolade/Documents/data/mongo.hck.json --format=html --doc=/home/hackolade/Documents/data/mongodoc.html


## Generate documentation in pdf format

### For Neo4j
docker compose run --rm hackoladeStudioCLI genDoc --model=/home/hackolade/Documents/data/neo4j.hck.json --format=pdf --doc=/home/hackolade/Documents/data/neo4jdoc.pdf

### For Mongo
docker compose run --rm hackoladeStudioCLI genDoc --model=/home/hackolade/Documents/data/mongo.hck.json --format=pdf --doc=/home/hackolade/Documents/data/mongodoc.pdf

## Compare two versions of a model
docker compose run --rm hackoladeStudioCLI compMod --model1=/home/hackolade/Documents/data/neo4j.hck.json --model2=/home/hackolade/Documents/data/neo4j_v2.hck.json --deltamodel=/home/hackolade/Documents/data/neo4j_deltamodel.hck.json

docker compose run --rm hackoladeStudioCLI compMod --model1=/home/hackolade/Documents/data/mongo.hck.json --model2=/home/hackolade/Documents/data/mongo_v2.hck.json --deltamodel=/home/hackolade/Documents/data/mongo_deltamodel.hck.json

## Reverse Engineering MongoDB atlas based on connection file

docker compose run --rm hackoladeStudioCLI revEng 