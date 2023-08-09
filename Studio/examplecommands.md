# Running the Hackolade Command-line-interface in a Docker container

Information from our manual about the CLI: 
* high level on https://hackolade.com/help/IntegratetheCLIwithDevOpsCICDpip.html
* detail on https://hackolade.com/help/CommandLineInterface.html

Information about the docker configuration on https://hackolade.com/help/Dockercontainer.html, which refers to detailed instructions on https://github.com/hackolade/docker/tree/main/Studio

Cloned that repo on my local machine in /Users/rvanbruggen/Documents/GitHub/docker/
All the commands below are run from /Users/rvanbruggen/Documents/GitHub/docker/Studio

## 1. Building the image
```
docker build --no-cache --pull -f Dockerfile.app -t hackolade:latest .
```
## 2. Installing the plugins based on Dockerfile.plugins file
Edited the `Dockerfile.plugins` file to prepare for the installation of the plugins.
Installation is done through this command:
```
docker build --no-cache -f Dockerfile.plugins -t hackolade:latest .
```

## 3. Running some example scenarios
Using Docker Desktop on my Mac.
Had to disable the "virtualisation framework" as I was getting segmentation faults. 

Checking install:
```
docker compose run --rm hackoladeStudioCLI version;
```

### A. Reverse Engineering MongoDB atlas based on connection file
Connection file is created and exported from Hackolade Studio UI: `Atlas_MONGODB_connection.bin`
#### First run: reverse engineering to a file
```
docker compose run --rm hackoladeStudioCLI revEng --target=MONGODB --connectFile=/home/hackolade/Documents/data/mongodb_atlas/Atlas_MONGODB_connection.bin --model=/home/hackolade/Documents/data/mongodb_atlas/docker_reveng_mongodb-atlas-mflix.hck.json --selectedObjects="sample_mflix" --inferRelationships=true --samplingValue=10;
```

#### Intermediate step: using Compass
Using MongoDB compass, we can make a change to the `sample_mflix` database and add a collection that has one or two attributes.
#### Second run: reverse engineering the modified MongoDB Atlas database
```
docker compose run --rm hackoladeStudioCLI revEng --target=MONGODB --connectFile=/home/hackolade/Documents/data/mongodb_atlas/Atlas_MONGODB_connection.bin --model=/home/hackolade/Documents/data/mongodb_atlas/docker_reveng_mongodb-atlas-mflix_v2.hck.json --selectedObjects="sample_mflix" --inferRelationships=true --samplingValue=10;
```
### B. Compare the two reverse-engineered models
We now have two versions of the reverse-engineered models. We can use the docker-based CLI to check if anything has changed by creating a delta-model that formalises the difference between the two versions of the model:

```
docker compose run --rm hackoladeStudioCLI compMod --model1=/home/hackolade/Documents/data/mongodb_atlas/docker_reveng_mongodb-atlas-mflix.hck.json --model2=/home/hackolade/Documents/data/mongodb_atlas/docker_reveng_mongodb-atlas-mflix_v2.hck.json --deltamodel=/home/hackolade/Documents/data/mongodb_atlas/mongo_deltamodel.hck.json;
```

### C. Generate documentation in pdf format
We can use the docker-based CLI to automatically generate documentation for our models.
#### i. For Neo4j model
```
docker compose run --rm hackoladeStudioCLI genDoc --model=/home/hackolade/Documents/data/neo4j/neo4j.hck.json --format=pdf --doc=/home/hackolade/Documents/data/neo4j/neo4jdoc.pdf;
```
#### ii. For old existing Mongo model
```
docker compose run --rm hackoladeStudioCLI genDoc --model=/home/hackolade/Documents/data/mongodb/mongo.hck.json --format=pdf --doc=/home/hackolade/Documents/data/mongodb/mongodoc.pdf;
```
#### iii. For Atlas Mongo model
```
docker compose run --rm hackoladeStudioCLI genDoc --model=/home/hackolade/Documents/data/mongodb_atlas/docker_reveng_mongodb-atlas-mflix.hck.json --format=pdf --doc=/home/hackolade/Documents/data/mongodb_atlas/mongo_atlas_doc.pdf;
```

### D. Generate documentation in html format

#### i. For Neo4j
```
docker compose run --rm hackoladeStudioCLI genDoc --model=/home/hackolade/Documents/data/neo4j/neo4j.hck.json --format=html --doc=/home/hackolade/Documents/data/neo4j/neo4jdoc.html;
```
#### ii. For old existing Mongo model
```
docker compose run --rm hackoladeStudioCLI genDoc --model=/home/hackolade/Documents/data/mongodb/mongo.hck.json --format=html --doc=/home/hackolade/Documents/data/mongodb/mongodoc.html;
```
#### iii. For Atlas Mongo model
```
docker compose run --rm hackoladeStudioCLI genDoc --model=/home/hackolade/Documents/data/mongodb_atlas/docker_reveng_mongodb-atlas-mflix.hck.json --format=html --doc=/home/hackolade/Documents/data/mongodb_atlas/mongo_atlas_doc.html;
```


## 4. Conclusion and wrap-up
The above example shows how to run different scenario commands from the Hackolade Command-Line-Interface installed in a Docker container. 

This can be tied into an entire DevOps pipeline using workflow automation like for example Github Actions: see for example https://docs.github.com/en/actions/creating-actions/about-custom-actions and https://docs.github.com/en/actions/creating-actions/creating-a-docker-container-action. This allows for automated and increased governance processes on top of our data models and schemas.