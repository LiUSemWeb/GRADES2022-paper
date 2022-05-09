# Converting-Property-Graphs-to-RDF-and-RDF-star
In the paper 'Converting Property Graphs to RDF: A Preliminary Study of the Practical Impact of Different Mappings', we present and compare the results of experiment on query performance over RDF/RDF-star graphs. RDF/RDF-star graphs are produced by applying different direct LPG mapping approaches. In this repository, you can find the code for converting property graph to RDF and RDF-star, queries, query plans. 

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
