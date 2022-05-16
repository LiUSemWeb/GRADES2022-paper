# Material for research paper published in GRADES 2022 
In the paper '[Converting Property Graphs to RDF: A Preliminary Study of the Practical Impact of Different Mappings](https://doi.org/10.1145/3534540.3534695)' ([download PDF preprint](http://olafhartig.de/files/KhayatbashiEtAl_GRADES2022.pdf)), we present and compare the results of experiment on query performance over RDF/RDF-star graphs. RDF/RDF-star graphs are produced by applying different direct LPG mapping approaches. This repository provides material for this paper, including the code for converting Labeled Property Graphs (LPGs) to RDF and to RDF-star (using the mappings defined in the paper), test queries, and query plans used by the tested systems. 

## Data set
For the experiment, a real world LPG data set about Panama-Papers is used. Data is available in five separate CSV files, namely: panama_papers.nodes.address.csv, panama_papers.nodes.entity.csv,
panama_papers.nodes.intermediary.csv, panama_papers.nodes.officer.csv, and panama_papers.edges.csv. Panama-Papers data can be downloaded through this [link](https://offshoreleaks.icij.org/pages/database). After downloading the CSV files from the link above, copy them in the **input** directory.


## Mapping tools
The mapping scripts tools is in Python. For using the tool, you may need to create a Python virtual environment first. For creating a virtual environment, you need to run the below command in git bash:

```python -m venv .venv```

After creating the virtual environment, you need to activate it. For activating, you need to run the below command:

```source ./.venv/Scripts/Activate```

After the virtual environment is activated, you need to first **install all the packages**, which is available in **req.txt**. For installing the packages, you need to run the command below:

```pip install -r req.txt```

After installing the packages, you can **open jupyter lab** to run the mapping scrips. All you need is to run the below command in your git bash.

```jupyter lab```

Since the inputs of the mapping tool are CSVs, all you need is to copy the CSV files into the input directory as described above.


## Queries
There are three versions of the queries. You can find all the queries in the folder of **Queries**.

## Running the queries for experiment
As the goal of the experiment is to compare the three mappings in terms of execution time, you can find the materials for running the queries in 
the **Executing queries for experiment** directory. For running the experiment, you need to set up the port, user, and password for some of the systems. In below, 
you can find the details of the set up for each system.

## Neo4j
In the paper, we used the community edition 4.3.7 of Neo4j. For this purpose, we created an image of Neo4j inside the docker. You can start a Neo4j container by following the instructions below. 

Important: Before running the command below, copy all of the panama paper CSVs inside the import directory then run the command. For Neo4j configurations, we used the port **localhost:7687**. Moreover, we used the defualt user and password of the Neo4j, which is **neo4j** and **1234**.

```
docker run \
    --name panama-paper-evaluation \
    -p7474:7474 -p7687:7687 \
    -d \
    -v $HOME/neo4j/data:/data \
    -v $HOME/neo4j/logs:/logs \
    -v $HOME/neo4j/import:/var/lib/neo4j/import \
    -v $HOME/neo4j/plugins:/plugins \
    -e NEO4J_AUTH=neo4j/1234 \
    neo4j:4.3.7-community
```
The above command allows you to access neo4j through your browser at **http://localhost:7474**. Now, you need to import the data into a new database. You can do it with the command below:

```
docker exec panama-paper-evaluation neo4j-admin import --nodes=officer=/var/lib/neo4j/import/panama_papers.nodes.officer.csv --nodes=entity=/var/lib/neo4j/import/panama_papers.nodes.entity.csv --nodes=intermediary=/var/lib/neo4j/import/panama_papers.nodes.intermediary.csv --nodes=address=/var/lib/neo4j/import/panama_papers.nodes.address.csv --relationships=/var/lib/neo4j/import/panama_papers.edges.csv --delimiter="," --database=panama-paper
```

Since the community version does not support multiple databases at the same time, you need to change the Neo4j configuration to use the created database. It can be achieved by following the steps in below.

Firstly, install the vim:

```
apt-get update
apt-get -y install vim
```


After isntallation, you need to edit the file **NEO4J_HOME\conf\neo4j.conf**. For editting, you need to un-comment the line **dbms.default_database=neo4j**. Change the neo4j to the database that you want to use, which is **panama-paper** here.

After changing the configuration, you need to restart the container. You can do it by:

```
docker stop panama-paper-evaluation
docker start panama-paper-evaluation
```

Now, You can run the cypher shell in container and run queries with the command below:

```
docker exec -it panama-paper-evaluation cypher-shell -u neo4j -p 1234
```


## GraphDB
To set up each system, you can create an image of each system in docker. In bellow, you can find an explanation of how to create 
an instance of each system in the docker and how to load the data into each. We used the free version of GraphDB(v.9.10.0). You can pull the version that we have used in the experiment with the command below.

```docker pull shahrzadkhb/dockerhub:graphdbfree-9.10.0 ``` 

After pulling the image of GraphDB, you need to create a directory and copy all of the data files and run the command below.

```docker run --name panama-paper -d -p 7200:7200 -v path/to/data:/opt/graphdb/home/data shahrzadkhb/dockerhub:graphdbfree-9.10.0```

with the steps above, you could successfully create a free image of GraphDB inside the docker. Now, you need to create a database and 
import the data into it, which can be achieved by following the steps below.

First, You need to enter the container bash console: 

```docker exec -it panama-paper bash```
    
Then, you need to create a repository:

```   
cd opt/graphdb/dist/bin/.console
connect http://localhost:7200
create free 
```
Important: In order to do the experiment on GraphDB, you need to create three different databases in the system. You can create the databases with the names of: RDF_based_approach_1 for loading the RDF-based-approach-1 data, RDF_based_approach_2 for loading the RDF-based-approach-2 data, and RDF_star_based_approach for loading the RDF-star-based-approach data. Dont forget to give an id and a name when creating a repository.
 
Lastly, you need to open a repository and load the data into it.
  
```
open <repository id>
load /opt/graphdb/home/data/data-file.ttl   
```

## Stardog
We used Stardog(v.7.9.0) for the experiment. For creating Stardog inside the docker, you need to follow the steps which are explained below.

1. Firstly, you need to request a license key from the Stardog webpage. You can can do it trough the link https://www.stardog.com/download-free/.
2. After getting the licence key, you need to create a directory and name it to **stardog_home**. Then, copy the license key into the **stardog_home** directory. Now, by running the below command you can create an image of Stardog inside the docker.

```
docker run --name panama-paper-evaluation -it -v ../stardog_home/:/var/opt/stardog -p 5820:5820 stardog/stardog:7.9.0-java11-preview 
```

In the Stardog system, you also need to create three different databases like GraphDB. You also need to create the databases with the same names that you used in GraphDB. The port number, user name, and the password is **localhost:5820**, **admin**, **admin**, respectively. You can do it by running the python code, which is inside the **Load to Stardog** directory. 


After setting up each system and loading the data into each databases, you can run the experiment to mesure the average of query execution time. The output of each code file is a CSV containing the execution time, average, and stdv.    
