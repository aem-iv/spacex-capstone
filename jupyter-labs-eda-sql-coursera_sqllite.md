<p style="text-align:center">
    <a href="https://skills.network" target="_blank">
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/assets/logos/SN_web_lightmode.png" width="200" alt="Skills Network Logo">
    </a>
</p>

<h1 align=center><font size = 5>Assignment: SQL Notebook for Peer Assignment</font></h1>

Estimated time needed: **60** minutes.

## Introduction
Using this Python notebook you will:

1.  Understand the Spacex DataSet
2.  Load the dataset  into the corresponding table in a Db2 database
3.  Execute SQL queries to answer assignment questions 


## Overview of the DataSet

SpaceX has gained worldwide attention for a series of historic milestones. 

It is the only private company ever to return a spacecraft from low-earth orbit, which it first accomplished in December 2010.
SpaceX advertises Falcon 9 rocket launches on its website with a cost of 62 million dollars wheras other providers cost upward of 165 million dollars each, much of the savings is because Space X can reuse the first stage. 


Therefore if we can determine if the first stage will land, we can determine the cost of a launch. 

This information can be used if an alternate company wants to bid against SpaceX for a rocket launch.

This dataset includes a record for each payload carried during a SpaceX mission into outer space.


### Download the datasets

This assignment requires you to load the spacex dataset.

In many cases the dataset to be analyzed is available as a .CSV (comma separated values) file, perhaps on the internet. Click on the link below to download and save the dataset (.CSV file):

 <a href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_2/data/Spacex.csv" target="_blank">Spacex DataSet</a>




```python
!pip install sqlalchemy==1.3.9

```

    Collecting sqlalchemy==1.3.9
      Downloading SQLAlchemy-1.3.9.tar.gz (6.0 MB)
    [2K     [90m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━[0m [32m6.0/6.0 MB[0m [31m120.8 MB/s[0m eta [36m0:00:00[0m
      Preparing metadata (setup.py) ... [?2done
    [?25hBuilding wheels for collected packages: sqlalchemy
      Building wheel for sqlalchemy (setup.py) ...done
    [?25h  Created wheel for sqlalchemy: filename=SQLAlchemy-1.3.9-cp312-cp312-linux_x86_64.whl size=1160111 sha256=01447fe7bcfd4da5133cee4b9fec480b8be7d297c6286f8f6de107c695604004
      Stored in directory: /home/jupyterlab/.cache/pip/wheels/b3/1c/42/0e26b8d512adc6bce10ff71a05229366b4ccec641cd3b42111
    Successfully built sqlalchemy
    Installing collected packages: sqlalchemy
      Attempting uninstall: sqlalchemy
        Found existing installation: SQLAlchemy 2.0.37
        Uninstalling SQLAlchemy-2.0.37:
          Successfully uninstalled SQLAlchemy-2.0.37
    [31mERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
    jupyterhub 5.2.1 requires SQLAlchemy>=1.4.1, but you have sqlalchemy 1.3.9 which is incompatible.[0m[31m
    [0mSuccessfully installed sqlalchemy-1.3.9


### Connect to the database

Let us first load the SQL extension and establish a connection with the database



```python
!pip install ipython-sql
!pip install ipython-sql prettytable
```

    Collecting ipython-sql
      Downloading ipython_sql-0.5.0-py3-none-any.whl.metadata (17 kB)
    Collecting prettytable (from ipython-sql)
      Downloading prettytable-3.17.0-py3-none-any.whl.metadata (34 kB)
    Requirement already satisfied: ipython in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (8.31.0)
    Collecting sqlalchemy>=2.0 (from ipython-sql)
      Downloading sqlalchemy-2.0.47-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl.metadata (9.5 kB)
    Collecting sqlparse (from ipython-sql)
      Downloading sqlparse-0.5.5-py3-none-any.whl.metadata (4.7 kB)
    Requirement already satisfied: six in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (1.17.0)
    Requirement already satisfied: ipython-genutils in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (0.2.0)
    Requirement already satisfied: greenlet>=1 in /opt/conda/lib/python3.12/site-packages (from sqlalchemy>=2.0->ipython-sql) (3.1.1)
    Requirement already satisfied: typing-extensions>=4.6.0 in /opt/conda/lib/python3.12/site-packages (from sqlalchemy>=2.0->ipython-sql) (4.12.2)
    Requirement already satisfied: decorator in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (5.1.1)
    Requirement already satisfied: jedi>=0.16 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (0.19.2)
    Requirement already satisfied: matplotlib-inline in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (0.1.7)
    Requirement already satisfied: pexpect>4.3 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (4.9.0)
    Requirement already satisfied: prompt_toolkit<3.1.0,>=3.0.41 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (3.0.50)
    Requirement already satisfied: pygments>=2.4.0 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (2.19.1)
    Requirement already satisfied: stack_data in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (0.6.3)
    Requirement already satisfied: traitlets>=5.13.0 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (5.14.3)
    Requirement already satisfied: wcwidth in /opt/conda/lib/python3.12/site-packages (from prettytable->ipython-sql) (0.2.13)
    Requirement already satisfied: parso<0.9.0,>=0.8.4 in /opt/conda/lib/python3.12/site-packages (from jedi>=0.16->ipython->ipython-sql) (0.8.4)
    Requirement already satisfied: ptyprocess>=0.5 in /opt/conda/lib/python3.12/site-packages (from pexpect>4.3->ipython->ipython-sql) (0.7.0)
    Requirement already satisfied: executing>=1.2.0 in /opt/conda/lib/python3.12/site-packages (from stack_data->ipython->ipython-sql) (2.1.0)
    Requirement already satisfied: asttokens>=2.1.0 in /opt/conda/lib/python3.12/site-packages (from stack_data->ipython->ipython-sql) (3.0.0)
    Requirement already satisfied: pure_eval in /opt/conda/lib/python3.12/site-packages (from stack_data->ipython->ipython-sql) (0.2.3)
    Downloading ipython_sql-0.5.0-py3-none-any.whl (20 kB)
    Downloading sqlalchemy-2.0.47-cp312-cp312-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl (3.3 MB)
    [2K   [90m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━[0m [32m3.3/3.3 MB[0m [31m80.8 MB/s[0m eta [36m0:00:00[0m
    [?25hDownloading prettytable-3.17.0-py3-none-any.whl (34 kB)
    Downloading sqlparse-0.5.5-py3-none-any.whl (46 kB)
    Installing collected packages: sqlparse, sqlalchemy, prettytable, ipython-sql
      Attempting uninstall: sqlalchemy
        Found existing installation: SQLAlchemy 1.3.9
        Uninstalling SQLAlchemy-1.3.9:
          Successfully uninstalled SQLAlchemy-1.3.9
    Successfully installed ipython-sql-0.5.0 prettytable-3.17.0 sqlalchemy-2.0.47 sqlparse-0.5.5
    Requirement already satisfied: ipython-sql in /opt/conda/lib/python3.12/site-packages (0.5.0)
    Requirement already satisfied: prettytable in /opt/conda/lib/python3.12/site-packages (3.17.0)
    Requirement already satisfied: ipython in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (8.31.0)
    Requirement already satisfied: sqlalchemy>=2.0 in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (2.0.47)
    Requirement already satisfied: sqlparse in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (0.5.5)
    Requirement already satisfied: six in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (1.17.0)
    Requirement already satisfied: ipython-genutils in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (0.2.0)
    Requirement already satisfied: wcwidth in /opt/conda/lib/python3.12/site-packages (from prettytable) (0.2.13)
    Requirement already satisfied: greenlet>=1 in /opt/conda/lib/python3.12/site-packages (from sqlalchemy>=2.0->ipython-sql) (3.1.1)
    Requirement already satisfied: typing-extensions>=4.6.0 in /opt/conda/lib/python3.12/site-packages (from sqlalchemy>=2.0->ipython-sql) (4.12.2)
    Requirement already satisfied: decorator in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (5.1.1)
    Requirement already satisfied: jedi>=0.16 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (0.19.2)
    Requirement already satisfied: matplotlib-inline in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (0.1.7)
    Requirement already satisfied: pexpect>4.3 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (4.9.0)
    Requirement already satisfied: prompt_toolkit<3.1.0,>=3.0.41 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (3.0.50)
    Requirement already satisfied: pygments>=2.4.0 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (2.19.1)
    Requirement already satisfied: stack_data in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (0.6.3)
    Requirement already satisfied: traitlets>=5.13.0 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (5.14.3)
    Requirement already satisfied: parso<0.9.0,>=0.8.4 in /opt/conda/lib/python3.12/site-packages (from jedi>=0.16->ipython->ipython-sql) (0.8.4)
    Requirement already satisfied: ptyprocess>=0.5 in /opt/conda/lib/python3.12/site-packages (from pexpect>4.3->ipython->ipython-sql) (0.7.0)
    Requirement already satisfied: executing>=1.2.0 in /opt/conda/lib/python3.12/site-packages (from stack_data->ipython->ipython-sql) (2.1.0)
    Requirement already satisfied: asttokens>=2.1.0 in /opt/conda/lib/python3.12/site-packages (from stack_data->ipython->ipython-sql) (3.0.0)
    Requirement already satisfied: pure_eval in /opt/conda/lib/python3.12/site-packages (from stack_data->ipython->ipython-sql) (0.2.3)



```python
%load_ext sql
```

    The sql extension is already loaded. To reload it, use:
      %reload_ext sql



```python
import csv, sqlite3
import prettytable
prettytable.DEFAULT = 'DEFAULT'

con = sqlite3.connect("my_data1.db")
cur = con.cursor()
```


```python
!pip install -q pandas
```


```python
%sql sqlite:///my_data1.db
```


```python
import pandas as pd
df = pd.read_csv("Spacex.csv")
df.to_sql("SPACEXTBL", con, if_exists='replace', index=False,method="multi")
```




    101



**Note:This below code is added to remove blank rows from table**



```python
#DROP THE TABLE IF EXISTS

%sql DROP TABLE IF EXISTS SPACEXTABLE;
```

     * sqlite:///my_data1.db
    Done.





    []




```python
%sql create table SPACEXTABLE as select * from SPACEXTBL where Date is not null
```

     * sqlite:///my_data1.db
    Done.





    []



## Tasks

Now write and execute SQL queries to solve the assignment tasks.

**Note: If the column names are in mixed case enclose it in double quotes
   For Example "Landing_Outcome"**

### Task 1




##### Display the names of the unique launch sites  in the space mission



```python
%sql SELECT * from SPACEXTABLE
```

     * sqlite:///my_data1.db
    Done.





<table>
    <thead>
        <tr>
            <th>Date</th>
            <th>Time (UTC)</th>
            <th>Booster_Version</th>
            <th>Launch_Site</th>
            <th>Payload</th>
            <th>PAYLOAD_MASS__KG_</th>
            <th>Orbit</th>
            <th>Customer</th>
            <th>Mission_Outcome</th>
            <th>Landing_Outcome</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>2010-06-04</td>
            <td>18:45:00</td>
            <td>F9 v1.0  B0003</td>
            <td>CCAFS LC-40</td>
            <td>Dragon Spacecraft Qualification Unit</td>
            <td>0</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Failure (parachute)</td>
        </tr>
        <tr>
            <td>2010-12-08</td>
            <td>15:43:00</td>
            <td>F9 v1.0  B0004</td>
            <td>CCAFS LC-40</td>
            <td>Dragon demo flight C1, two CubeSats, barrel of Brouere cheese</td>
            <td>0</td>
            <td>LEO (ISS)</td>
            <td>NASA (COTS) NRO</td>
            <td>Success</td>
            <td>Failure (parachute)</td>
        </tr>
        <tr>
            <td>2012-05-22</td>
            <td>7:44:00</td>
            <td>F9 v1.0  B0005</td>
            <td>CCAFS LC-40</td>
            <td>Dragon demo flight C2</td>
            <td>525</td>
            <td>LEO (ISS)</td>
            <td>NASA (COTS)</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2012-10-08</td>
            <td>0:35:00</td>
            <td>F9 v1.0  B0006</td>
            <td>CCAFS LC-40</td>
            <td>SpaceX CRS-1</td>
            <td>500</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2013-03-01</td>
            <td>15:10:00</td>
            <td>F9 v1.0  B0007</td>
            <td>CCAFS LC-40</td>
            <td>SpaceX CRS-2</td>
            <td>677</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2013-09-29</td>
            <td>16:00:00</td>
            <td>F9 v1.1  B1003</td>
            <td>VAFB SLC-4E</td>
            <td>CASSIOPE</td>
            <td>500</td>
            <td>Polar LEO</td>
            <td>MDA</td>
            <td>Success</td>
            <td>Uncontrolled (ocean)</td>
        </tr>
        <tr>
            <td>2013-12-03</td>
            <td>22:41:00</td>
            <td>F9 v1.1</td>
            <td>CCAFS LC-40</td>
            <td>SES-8</td>
            <td>3170</td>
            <td>GTO</td>
            <td>SES</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2014-01-06</td>
            <td>22:06:00</td>
            <td>F9 v1.1</td>
            <td>CCAFS LC-40</td>
            <td>Thaicom 6</td>
            <td>3325</td>
            <td>GTO</td>
            <td>Thaicom</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2014-04-18</td>
            <td>19:25:00</td>
            <td>F9 v1.1</td>
            <td>CCAFS LC-40</td>
            <td>SpaceX CRS-3</td>
            <td>2296</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Controlled (ocean)</td>
        </tr>
        <tr>
            <td>2014-07-14</td>
            <td>15:15:00</td>
            <td>F9 v1.1</td>
            <td>CCAFS LC-40</td>
            <td>OG2 Mission 1  6 Orbcomm-OG2 satellites</td>
            <td>1316</td>
            <td>LEO</td>
            <td>Orbcomm</td>
            <td>Success</td>
            <td>Controlled (ocean)</td>
        </tr>
        <tr>
            <td>2014-08-05</td>
            <td>8:00:00</td>
            <td>F9 v1.1</td>
            <td>CCAFS LC-40</td>
            <td>AsiaSat 8</td>
            <td>4535</td>
            <td>GTO</td>
            <td>AsiaSat</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2014-09-07</td>
            <td>5:00:00</td>
            <td>F9 v1.1 B1011</td>
            <td>CCAFS LC-40</td>
            <td>AsiaSat 6</td>
            <td>4428</td>
            <td>GTO</td>
            <td>AsiaSat</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2014-09-21</td>
            <td>5:52:00</td>
            <td>F9 v1.1 B1010</td>
            <td>CCAFS LC-40</td>
            <td>SpaceX CRS-4</td>
            <td>2216</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Uncontrolled (ocean)</td>
        </tr>
        <tr>
            <td>2015-01-10</td>
            <td>9:47:00</td>
            <td>F9 v1.1 B1012</td>
            <td>CCAFS LC-40</td>
            <td>SpaceX CRS-5</td>
            <td>2395</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Failure (drone ship)</td>
        </tr>
        <tr>
            <td>2015-02-11</td>
            <td>23:03:00</td>
            <td>F9 v1.1 B1013</td>
            <td>CCAFS LC-40</td>
            <td>DSCOVR</td>
            <td>570</td>
            <td>HEO</td>
            <td>U.S. Air Force NASA NOAA</td>
            <td>Success</td>
            <td>Controlled (ocean)</td>
        </tr>
        <tr>
            <td>2015-03-02</td>
            <td>3:50:00</td>
            <td>F9 v1.1 B1014</td>
            <td>CCAFS LC-40</td>
            <td>ABS-3A Eutelsat 115 West B</td>
            <td>4159</td>
            <td>GTO</td>
            <td>ABS Eutelsat</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2015-04-14</td>
            <td>20:10:00</td>
            <td>F9 v1.1 B1015</td>
            <td>CCAFS LC-40</td>
            <td>SpaceX CRS-6</td>
            <td>1898</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Failure (drone ship)</td>
        </tr>
        <tr>
            <td>2015-04-27</td>
            <td>23:03:00</td>
            <td>F9 v1.1 B1016</td>
            <td>CCAFS LC-40</td>
            <td>Turkmen 52 / MonacoSAT</td>
            <td>4707</td>
            <td>GTO</td>
            <td>Turkmenistan National Space Agency</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2015-06-28</td>
            <td>14:21:00</td>
            <td>F9 v1.1 B1018</td>
            <td>CCAFS LC-40</td>
            <td>SpaceX CRS-7</td>
            <td>1952</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Failure (in flight)</td>
            <td>Precluded (drone ship)</td>
        </tr>
        <tr>
            <td>2015-12-22</td>
            <td>1:29:00</td>
            <td>F9 FT B1019</td>
            <td>CCAFS LC-40</td>
            <td>OG2 Mission 2  11 Orbcomm-OG2 satellites</td>
            <td>2034</td>
            <td>LEO</td>
            <td>Orbcomm</td>
            <td>Success</td>
            <td>Success (ground pad)</td>
        </tr>
        <tr>
            <td>2016-01-17</td>
            <td>18:42:00</td>
            <td>F9 v1.1 B1017</td>
            <td>VAFB SLC-4E</td>
            <td>Jason-3</td>
            <td>553</td>
            <td>LEO</td>
            <td>NASA (LSP) NOAA CNES</td>
            <td>Success</td>
            <td>Failure (drone ship)</td>
        </tr>
        <tr>
            <td>2016-03-04</td>
            <td>23:35:00</td>
            <td>F9 FT B1020</td>
            <td>CCAFS LC-40</td>
            <td>SES-9</td>
            <td>5271</td>
            <td>GTO</td>
            <td>SES</td>
            <td>Success</td>
            <td>Failure (drone ship)</td>
        </tr>
        <tr>
            <td>2016-04-08</td>
            <td>20:43:00</td>
            <td>F9 FT B1021.1</td>
            <td>CCAFS LC-40</td>
            <td>SpaceX CRS-8</td>
            <td>3136</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2016-05-06</td>
            <td>5:21:00</td>
            <td>F9 FT B1022</td>
            <td>CCAFS LC-40</td>
            <td>JCSAT-14</td>
            <td>4696</td>
            <td>GTO</td>
            <td>SKY Perfect JSAT Group</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2016-05-27</td>
            <td>21:39:00</td>
            <td>F9 FT B1023.1</td>
            <td>CCAFS LC-40</td>
            <td>Thaicom 8</td>
            <td>3100</td>
            <td>GTO</td>
            <td>Thaicom</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2016-06-15</td>
            <td>14:29:00</td>
            <td>F9 FT B1024</td>
            <td>CCAFS LC-40</td>
            <td>ABS-2A Eutelsat 117 West B</td>
            <td>3600</td>
            <td>GTO</td>
            <td>ABS Eutelsat</td>
            <td>Success</td>
            <td>Failure (drone ship)</td>
        </tr>
        <tr>
            <td>2016-07-18</td>
            <td>4:45:00</td>
            <td>F9 FT B1025.1</td>
            <td>CCAFS LC-40</td>
            <td>SpaceX CRS-9</td>
            <td>2257</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Success (ground pad)</td>
        </tr>
        <tr>
            <td>2016-08-14</td>
            <td>5:26:00</td>
            <td>F9 FT B1026</td>
            <td>CCAFS LC-40</td>
            <td>JCSAT-16</td>
            <td>4600</td>
            <td>GTO</td>
            <td>SKY Perfect JSAT Group</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2017-01-14</td>
            <td>17:54:00</td>
            <td>F9 FT B1029.1</td>
            <td>VAFB SLC-4E</td>
            <td>Iridium NEXT 1</td>
            <td>9600</td>
            <td>Polar LEO</td>
            <td>Iridium Communications</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2017-02-19</td>
            <td>14:39:00</td>
            <td>F9 FT B1031.1</td>
            <td>KSC LC-39A</td>
            <td>SpaceX CRS-10</td>
            <td>2490</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Success (ground pad)</td>
        </tr>
        <tr>
            <td>2017-03-16</td>
            <td>6:00:00</td>
            <td>F9 FT B1030</td>
            <td>KSC LC-39A</td>
            <td>EchoStar 23</td>
            <td>5600</td>
            <td>GTO</td>
            <td>EchoStar</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2017-03-30</td>
            <td>22:27:00</td>
            <td>F9 FT  B1021.2</td>
            <td>KSC LC-39A</td>
            <td>SES-10</td>
            <td>5300</td>
            <td>GTO</td>
            <td>SES</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2017-05-01</td>
            <td>11:15:00</td>
            <td>F9 FT B1032.1</td>
            <td>KSC LC-39A</td>
            <td>NROL-76</td>
            <td>5300</td>
            <td>LEO</td>
            <td>NRO</td>
            <td>Success</td>
            <td>Success (ground pad)</td>
        </tr>
        <tr>
            <td>2017-05-15</td>
            <td>23:21:00</td>
            <td>F9 FT B1034</td>
            <td>KSC LC-39A</td>
            <td>Inmarsat-5 F4</td>
            <td>6070</td>
            <td>GTO</td>
            <td>Inmarsat</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2017-06-03</td>
            <td>21:07:00</td>
            <td>F9 FT B1035.1</td>
            <td>KSC LC-39A</td>
            <td>SpaceX CRS-11</td>
            <td>2708</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Success (ground pad)</td>
        </tr>
        <tr>
            <td>2017-06-23</td>
            <td>19:10:00</td>
            <td>F9 FT  B1029.2</td>
            <td>KSC LC-39A</td>
            <td>BulgariaSat-1</td>
            <td>3669</td>
            <td>GTO</td>
            <td>Bulsatcom</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2017-06-25</td>
            <td>20:25:00</td>
            <td>F9 FT B1036.1</td>
            <td>VAFB SLC-4E</td>
            <td>Iridium NEXT 2</td>
            <td>9600</td>
            <td>LEO</td>
            <td>Iridium Communications</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2017-07-05</td>
            <td>23:38:00</td>
            <td>F9 FT B1037</td>
            <td>KSC LC-39A</td>
            <td>Intelsat 35e</td>
            <td>6761</td>
            <td>GTO</td>
            <td>Intelsat</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2017-08-14</td>
            <td>16:31:00</td>
            <td>F9 B4 B1039.1</td>
            <td>KSC LC-39A</td>
            <td>SpaceX CRS-12</td>
            <td>3310</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Success (ground pad)</td>
        </tr>
        <tr>
            <td>2017-08-24</td>
            <td>18:51:00</td>
            <td>F9 FT B1038.1</td>
            <td>VAFB SLC-4E</td>
            <td>Formosat-5</td>
            <td>475</td>
            <td>SSO</td>
            <td>NSPO</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2017-09-07</td>
            <td>14:00:00</td>
            <td>F9 B4 B1040.1</td>
            <td>KSC LC-39A</td>
            <td>Boeing X-37B OTV-5</td>
            <td>4990</td>
            <td>LEO</td>
            <td>U.S. Air Force</td>
            <td>Success</td>
            <td>Success (ground pad)</td>
        </tr>
        <tr>
            <td>2017-10-09</td>
            <td>12:37:00</td>
            <td>F9 B4 B1041.1</td>
            <td>VAFB SLC-4E</td>
            <td>Iridium NEXT 3</td>
            <td>9600</td>
            <td>Polar LEO</td>
            <td>Iridium Communications</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2017-10-11</td>
            <td>22:53:00</td>
            <td>F9 FT  B1031.2</td>
            <td>KSC LC-39A</td>
            <td>SES-11 / EchoStar 105</td>
            <td>5200</td>
            <td>GTO</td>
            <td>SES EchoStar</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2017-10-30</td>
            <td>19:34:00</td>
            <td>F9 B4 B1042.1</td>
            <td>KSC LC-39A</td>
            <td>Koreasat 5A</td>
            <td>3500</td>
            <td>GTO</td>
            <td>KT Corporation</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2017-12-15</td>
            <td>15:36:00</td>
            <td>F9 FT  B1035.2</td>
            <td>CCAFS SLC-40</td>
            <td>SpaceX CRS-13</td>
            <td>2205</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Success (ground pad)</td>
        </tr>
        <tr>
            <td>2017-12-23</td>
            <td>1:27:00</td>
            <td>F9 FT  B1036.2</td>
            <td>VAFB SLC-4E</td>
            <td>Iridium NEXT 4</td>
            <td>9600</td>
            <td>Polar LEO</td>
            <td>Iridium Communications</td>
            <td>Success</td>
            <td>Controlled (ocean)</td>
        </tr>
        <tr>
            <td>2018-01-08</td>
            <td>1:00:00</td>
            <td>F9 B4 B1043.1</td>
            <td>CCAFS SLC-40</td>
            <td>Zuma</td>
            <td>5000</td>
            <td>LEO</td>
            <td>Northrop Grumman</td>
            <td>Success (payload status unclear)</td>
            <td>Success (ground pad)</td>
        </tr>
        <tr>
            <td>2018-01-31</td>
            <td>21:25:00</td>
            <td>F9 FT  B1032.2</td>
            <td>CCAFS SLC-40</td>
            <td>GovSat-1 / SES-16</td>
            <td>4230</td>
            <td>GTO</td>
            <td>SES</td>
            <td>Success</td>
            <td>Controlled (ocean)</td>
        </tr>
        <tr>
            <td>2018-02-22</td>
            <td>14:17:00</td>
            <td>F9 FT  B1038.2</td>
            <td>VAFB SLC-4E</td>
            <td>Paz  Tintin A &amp; B</td>
            <td>2150</td>
            <td>SSO</td>
            <td>Hisdesat exactEarth SpaceX</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2018-03-06</td>
            <td>5:33:00</td>
            <td>F9 B4 B1044</td>
            <td>CCAFS SLC-40</td>
            <td>Hispasat 30W-6  PODSat</td>
            <td>6092</td>
            <td>GTO</td>
            <td>Hispasat  NovaWurks</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2018-03-30</td>
            <td>14:14:00</td>
            <td>F9 B4  B1041.2</td>
            <td>VAFB SLC-4E</td>
            <td>Iridium NEXT 5</td>
            <td>9600</td>
            <td>Polar LEO</td>
            <td>Iridium Communications</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2018-04-02</td>
            <td>20:30:00</td>
            <td>F9 B4  B1039.2</td>
            <td>CCAFS SLC-40</td>
            <td>SpaceX CRS-14</td>
            <td>2647</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2018-04-18</td>
            <td>22:51:00</td>
            <td>F9 B4 B1045.1</td>
            <td>CCAFS SLC-40</td>
            <td>Transiting Exoplanet Survey Satellite (TESS)</td>
            <td>362</td>
            <td>HEO</td>
            <td>NASA (LSP)</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2018-05-11</td>
            <td>20:14:00</td>
            <td>F9 B5  B1046.1</td>
            <td>KSC LC-39A</td>
            <td>Bangabandhu-1</td>
            <td>3600</td>
            <td>GTO</td>
            <td>Thales-Alenia/BTRC</td>
            <td>Success</td>
            <td>Success (drone ship)</td>
        </tr>
        <tr>
            <td>2018-05-22</td>
            <td>19:47:58</td>
            <td>F9 B4  B1043.2</td>
            <td>VAFB SLC-4E</td>
            <td>Iridium NEXT 6   GRACE-FO 1, 2</td>
            <td>6460</td>
            <td>Polar LEO</td>
            <td>Iridium Communications GFZ ‚ NASA</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2018-06-04</td>
            <td>4:45:00</td>
            <td>F9 B4  B1040.2</td>
            <td>CCAFS SLC-40</td>
            <td>SES-12</td>
            <td>5384</td>
            <td>GTO</td>
            <td>SES</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2018-06-29</td>
            <td>9:42:00</td>
            <td>F9 B4 B1045.2</td>
            <td>CCAFS SLC-40</td>
            <td>SpaceX CRS-15</td>
            <td>2697</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2018-07-22</td>
            <td>5:50:00</td>
            <td>F9 B5B1047.1</td>
            <td>CCAFS SLC-40</td>
            <td>Telstar 19V</td>
            <td>7075</td>
            <td>GTO</td>
            <td>Telesat</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2018-07-25</td>
            <td>11:39:00</td>
            <td>F9 B5B1048.1</td>
            <td>VAFB SLC-4E</td>
            <td>Iridium NEXT-7</td>
            <td>9600</td>
            <td>Polar LEO</td>
            <td>Iridium Communications</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2018-08-07</td>
            <td>5:18:00</td>
            <td>F9 B5 B1046.2</td>
            <td>CCAFS SLC-40</td>
            <td>Merah Putih </td>
            <td>5800</td>
            <td>GTO</td>
            <td>Telkom Indonesia</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2018-09-10</td>
            <td>4:45:00</td>
            <td>F9 B5B1049.1</td>
            <td>CCAFS SLC-40</td>
            <td>Telstar 18V / Apstar-5C</td>
            <td>7060</td>
            <td>GTO</td>
            <td>Telesat</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2018-10-08</td>
            <td>2:22:00</td>
            <td>F9 B5 B1048.2</td>
            <td>VAFB SLC-4E</td>
            <td>SAOCOM 1A</td>
            <td>3000</td>
            <td>SSO</td>
            <td>CONAE</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2018-11-15</td>
            <td>20:46:00</td>
            <td>F9 B5 B1047.2</td>
            <td>KSC LC-39A</td>
            <td>Es hail 2</td>
            <td>5300</td>
            <td>GTO</td>
            <td>Es hailSat</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2018-12-03</td>
            <td>18:34:05</td>
            <td>F9 B5 B1046.3</td>
            <td>VAFB SLC-4E</td>
            <td>SSO-A </td>
            <td>4000</td>
            <td>SSO</td>
            <td>Spaceflight Industries</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2018-12-05</td>
            <td>18:16:00</td>
            <td>F9 B5B1050</td>
            <td>CCAFS SLC-40</td>
            <td>SpaceX CRS-16</td>
            <td>2500</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Failure</td>
        </tr>
        <tr>
            <td>2018-12-23</td>
            <td>13:51:00</td>
            <td>F9 B5B1054</td>
            <td>CCAFS SLC-40</td>
            <td>GPS III-01 </td>
            <td>4400</td>
            <td>MEO</td>
            <td>USAF</td>
            <td>Success </td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2019-01-11</td>
            <td>15:31:00</td>
            <td>F9 B5 B1049.2</td>
            <td>VAFB SLC-4E</td>
            <td>Iridium NEXT-8</td>
            <td>9600</td>
            <td>Polar LEO</td>
            <td>Iridium Communications</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2019-02-22</td>
            <td>1:45:00</td>
            <td>F9 B5 B1048.3</td>
            <td>CCAFS SLC-40</td>
            <td>Nusantara Satu, Beresheet Moon lander, S5</td>
            <td>4850</td>
            <td>GTO</td>
            <td>PSN, SpaceIL / IAI</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2019-03-02</td>
            <td>7:49:00</td>
            <td>F9 B5B1051.1</td>
            <td>KSC LC-39A</td>
            <td>Crew Dragon Demo-1, SpaceX CRS-17 </td>
            <td>12055</td>
            <td>LEO (ISS)</td>
            <td>NASA (CCD) </td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2019-05-04</td>
            <td>6:48:00</td>
            <td>F9 B5B1056.1 </td>
            <td>CCAFS SLC-40</td>
            <td>SpaceX CRS-17, Starlink v0.9</td>
            <td>2495</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2019-05-24</td>
            <td>2:30:00</td>
            <td>F9 B5 B1049.3</td>
            <td>CCAFS SLC-40</td>
            <td>Starlink v0.9, RADARSAT Constellation</td>
            <td>13620</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2019-06-12</td>
            <td>14:17:00</td>
            <td>F9 B5 B1051.2 </td>
            <td>VAFB SLC-4E</td>
            <td>RADARSAT Constellation, SpaceX CRS-18 </td>
            <td>4200</td>
            <td>SSO</td>
            <td>Canadian Space Agency (CSA)</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2019-07-25</td>
            <td>22:01:00</td>
            <td>F9 B5 B1056.2 </td>
            <td>CCAFS SLC-40</td>
            <td>SpaceX CRS-18, AMOS-17 </td>
            <td>2268</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2019-08-06</td>
            <td>23:23:00</td>
            <td>F9 B5 B1047.3 </td>
            <td>CCAFS SLC-40</td>
            <td>AMOS-17, Starlink 1 v1.0 </td>
            <td>6500</td>
            <td>GTO</td>
            <td>Spacecom</td>
            <td>Success</td>
            <td>No attempt </td>
        </tr>
        <tr>
            <td>2019-11-11</td>
            <td>14:56:00</td>
            <td>F9 B5 B1048.4</td>
            <td>CCAFS SLC-40</td>
            <td>Starlink 1 v1.0, SpaceX CRS-19 </td>
            <td>15600</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2019-12-05</td>
            <td>17:29:00</td>
            <td>F9 B5B1059.1</td>
            <td>CCAFS SLC-40</td>
            <td>SpaceX CRS-19, JCSat-18 / Kacific 1 </td>
            <td>2617</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS), Kacific 1</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2019-12-17</td>
            <td>0:10:00</td>
            <td>F9 B5 B1056.3 </td>
            <td>CCAFS SLC-40</td>
            <td>JCSat-18 / Kacific 1, Starlink 2 v1.0 </td>
            <td>6956</td>
            <td>GTO</td>
            <td>Sky Perfect JSAT, Kacific 1</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-01-07</td>
            <td>2:33:00</td>
            <td>F9 B5 B1049.4</td>
            <td>CCAFS SLC-40</td>
            <td>Starlink 2 v1.0, Crew Dragon in-flight abort test </td>
            <td>15600</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-01-19</td>
            <td>15:30:00</td>
            <td>F9 B5 B1046.4</td>
            <td>KSC LC-39A</td>
            <td>Crew Dragon in-flight abort test, Starlink 3 v1.0 </td>
            <td>12050</td>
            <td>Sub-orbital</td>
            <td>NASA (CTS)</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2020-01-29</td>
            <td>14:07:00</td>
            <td>F9 B5 B1051.3</td>
            <td>CCAFS SLC-40</td>
            <td>Starlink 3 v1.0, Starlink 4 v1.0 </td>
            <td>15600</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-02-17</td>
            <td>15:05:00</td>
            <td>F9 B5 B1056.4</td>
            <td>CCAFS SLC-40</td>
            <td>Starlink 4 v1.0, SpaceX CRS-20</td>
            <td>15600</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Failure</td>
        </tr>
        <tr>
            <td>2020-03-07</td>
            <td>4:50:00</td>
            <td>F9 B5 B1059.2</td>
            <td>CCAFS SLC-40</td>
            <td>SpaceX CRS-20, Starlink 5 v1.0 </td>
            <td>1977</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-03-18</td>
            <td>12:16:00</td>
            <td>F9 B5 B1048.5</td>
            <td>KSC LC-39A</td>
            <td>Starlink 5 v1.0, Starlink 6 v1.0 </td>
            <td>15600</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Failure</td>
        </tr>
        <tr>
            <td>2020-04-22</td>
            <td>19:30:00</td>
            <td>F9 B5 B1051.4</td>
            <td>KSC LC-39A</td>
            <td>Starlink 6 v1.0, Crew Dragon Demo-2 </td>
            <td>15600</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-05-30</td>
            <td>19:22:00</td>
            <td>F9 B5B1058.1 </td>
            <td>KSC LC-39A</td>
            <td>Crew Dragon Demo-2, Starlink 7 v1.0 </td>
            <td>12530</td>
            <td>LEO (ISS)</td>
            <td>NASA (CCDev)</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-06-04</td>
            <td>1:25:00</td>
            <td>F9 B5 B1049.5</td>
            <td>CCAFS SLC-40</td>
            <td>Starlink 7 v1.0, Starlink 8 v1.0</td>
            <td>15600</td>
            <td>LEO</td>
            <td>SpaceX, Planet Labs</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-06-13</td>
            <td>9:21:00</td>
            <td>F9 B5 B1059.3</td>
            <td>CCAFS SLC-40</td>
            <td>Starlink 8 v1.0, SkySats-16, -17, -18, GPS III-03 </td>
            <td>15410</td>
            <td>LEO</td>
            <td>SpaceX, Planet Labs</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-06-30</td>
            <td>20:10:46</td>
            <td>F9 B5B1060.1</td>
            <td>CCAFS SLC-40</td>
            <td>GPS III-03, ANASIS-II</td>
            <td>4311</td>
            <td>MEO</td>
            <td>U.S. Space Force</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-07-20</td>
            <td>21:30:00</td>
            <td>F9 B5 B1058.2 </td>
            <td>CCAFS SLC-40</td>
            <td>ANASIS-II, Starlink 9 v1.0</td>
            <td>5500</td>
            <td>GTO</td>
            <td>Republic of Korea Army, Spaceflight Industries (BlackSky)</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-08-07</td>
            <td>5:12:00</td>
            <td>F9 B5 B1051.5</td>
            <td>KSC LC-39A</td>
            <td>Starlink 9 v1.0, SXRS-1, Starlink 10 v1.0 </td>
            <td>14932</td>
            <td>LEO</td>
            <td>SpaceX, Spaceflight Industries (BlackSky), Planet Labs</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-08-18</td>
            <td>14:31:00</td>
            <td>F9 B5 B1049.6</td>
            <td>CCAFS SLC-40</td>
            <td>Starlink 10 v1.0, SkySat-19, -20, -21, SAOCOM 1B </td>
            <td>15440</td>
            <td>LEO</td>
            <td>SpaceX, Planet Labs, PlanetIQ</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-08-30</td>
            <td>23:18:00</td>
            <td>F9 B5 B1059.4</td>
            <td>CCAFS SLC-40</td>
            <td>SAOCOM 1B, GNOMES 1, Tyvak-0172</td>
            <td>3130</td>
            <td>SSO</td>
            <td>CONAE, PlanetIQ, SpaceX</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-09-03</td>
            <td>12:46:14</td>
            <td>F9 B5 B1060.2 </td>
            <td>KSC LC-39A</td>
            <td>Starlink 11 v1.0, Starlink 12 v1.0 </td>
            <td>15600</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-10-06</td>
            <td>11:29:34</td>
            <td>F9 B5 B1058.3 </td>
            <td>KSC LC-39A</td>
            <td>Starlink 12 v1.0, Starlink 13 v1.0 </td>
            <td>15600</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-10-18</td>
            <td>12:25:57</td>
            <td>F9 B5 B1051.6</td>
            <td>KSC LC-39A</td>
            <td>Starlink 13 v1.0, Starlink 14 v1.0 </td>
            <td>15600</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-10-24</td>
            <td>15:31:34</td>
            <td>F9 B5 B1060.3</td>
            <td>CCAFS SLC-40</td>
            <td>Starlink 14 v1.0, GPS III-04  </td>
            <td>15600</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-11-05</td>
            <td>23:24:23</td>
            <td>F9 B5B1062.1</td>
            <td>CCAFS SLC-40</td>
            <td>GPS III-04 , Crew-1</td>
            <td>4311</td>
            <td>MEO</td>
            <td>USSF</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-11-16</td>
            <td>0:27:00</td>
            <td>F9 B5B1061.1 </td>
            <td>KSC LC-39A</td>
            <td>Crew-1, Sentinel-6 Michael Freilich </td>
            <td>12500</td>
            <td>LEO (ISS)</td>
            <td>NASA (CCP)</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-11-21</td>
            <td>17:17:08</td>
            <td>F9 B5B1063.1</td>
            <td>VAFB SLC-4E</td>
            <td>Sentinel-6 Michael Freilich, Starlink 15 v1.0 </td>
            <td>1192</td>
            <td>LEO</td>
            <td>NASA / NOAA / ESA / EUMETSAT</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-11-25</td>
            <td>2:13:00</td>
            <td>F9 B5 B1049.7 </td>
            <td>CCAFS SLC-40</td>
            <td>Starlink 15 v1.0, SpaceX CRS-21</td>
            <td>15600</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
        <tr>
            <td>2020-12-06</td>
            <td>16:17:08</td>
            <td>F9 B5 B1058.4 </td>
            <td>KSC LC-39A</td>
            <td>SpaceX CRS-21</td>
            <td>2972</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>Success</td>
        </tr>
    </tbody>
</table>




```python
%sql PRAGMA table_info(SPACEXTABLE)
```

     * sqlite:///my_data1.db
    Done.





<table>
    <thead>
        <tr>
            <th>cid</th>
            <th>name</th>
            <th>type</th>
            <th>notnull</th>
            <th>dflt_value</th>
            <th>pk</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>Date</td>
            <td>TEXT</td>
            <td>0</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Time (UTC)</td>
            <td>TEXT</td>
            <td>0</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Booster_Version</td>
            <td>TEXT</td>
            <td>0</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>3</td>
            <td>Launch_Site</td>
            <td>TEXT</td>
            <td>0</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>4</td>
            <td>Payload</td>
            <td>TEXT</td>
            <td>0</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>5</td>
            <td>PAYLOAD_MASS__KG_</td>
            <td>INT</td>
            <td>0</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>6</td>
            <td>Orbit</td>
            <td>TEXT</td>
            <td>0</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>7</td>
            <td>Customer</td>
            <td>TEXT</td>
            <td>0</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>8</td>
            <td>Mission_Outcome</td>
            <td>TEXT</td>
            <td>0</td>
            <td>None</td>
            <td>0</td>
        </tr>
        <tr>
            <td>9</td>
            <td>Landing_Outcome</td>
            <td>TEXT</td>
            <td>0</td>
            <td>None</td>
            <td>0</td>
        </tr>
    </tbody>
</table>




```sql
%%sql SELECT DISTINCT Launch_Site
FROM SPACEXTABLE;
```

     * sqlite:///my_data1.db
    Done.





<table>
    <thead>
        <tr>
            <th>Launch_Site</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>CCAFS LC-40</td>
        </tr>
        <tr>
            <td>VAFB SLC-4E</td>
        </tr>
        <tr>
            <td>KSC LC-39A</td>
        </tr>
        <tr>
            <td>CCAFS SLC-40</td>
        </tr>
    </tbody>
</table>




### Task 2


#####  Display 5 records where launch sites begin with the string 'CCA' 



```sql
%%sql SELECT * FROM SPACEXTABLE WHERE Launch_Site 
    LIKE '%CCA%' LIMIT 5;
```

     * sqlite:///my_data1.db
    Done.





<table>
    <thead>
        <tr>
            <th>Date</th>
            <th>Time (UTC)</th>
            <th>Booster_Version</th>
            <th>Launch_Site</th>
            <th>Payload</th>
            <th>PAYLOAD_MASS__KG_</th>
            <th>Orbit</th>
            <th>Customer</th>
            <th>Mission_Outcome</th>
            <th>Landing_Outcome</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>2010-06-04</td>
            <td>18:45:00</td>
            <td>F9 v1.0  B0003</td>
            <td>CCAFS LC-40</td>
            <td>Dragon Spacecraft Qualification Unit</td>
            <td>0</td>
            <td>LEO</td>
            <td>SpaceX</td>
            <td>Success</td>
            <td>Failure (parachute)</td>
        </tr>
        <tr>
            <td>2010-12-08</td>
            <td>15:43:00</td>
            <td>F9 v1.0  B0004</td>
            <td>CCAFS LC-40</td>
            <td>Dragon demo flight C1, two CubeSats, barrel of Brouere cheese</td>
            <td>0</td>
            <td>LEO (ISS)</td>
            <td>NASA (COTS) NRO</td>
            <td>Success</td>
            <td>Failure (parachute)</td>
        </tr>
        <tr>
            <td>2012-05-22</td>
            <td>7:44:00</td>
            <td>F9 v1.0  B0005</td>
            <td>CCAFS LC-40</td>
            <td>Dragon demo flight C2</td>
            <td>525</td>
            <td>LEO (ISS)</td>
            <td>NASA (COTS)</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2012-10-08</td>
            <td>0:35:00</td>
            <td>F9 v1.0  B0006</td>
            <td>CCAFS LC-40</td>
            <td>SpaceX CRS-1</td>
            <td>500</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
        <tr>
            <td>2013-03-01</td>
            <td>15:10:00</td>
            <td>F9 v1.0  B0007</td>
            <td>CCAFS LC-40</td>
            <td>SpaceX CRS-2</td>
            <td>677</td>
            <td>LEO (ISS)</td>
            <td>NASA (CRS)</td>
            <td>Success</td>
            <td>No attempt</td>
        </tr>
    </tbody>
</table>



### Task 3




##### Display the total payload mass carried by boosters launched by NASA (CRS)



```python
SELECT SUM(column_name) FROM table_name WHERE condition;
```


```sql
%%sql SELECT SUM(PAYLOAD_MASS__KG_) 
    FROM SPACEXTABLE 
    WHERE Customer = 'NASA (CRS)';
```

     * sqlite:///my_data1.db
    Done.





<table>
    <thead>
        <tr>
            <th>SUM(PAYLOAD_MASS__KG_)</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>45596</td>
        </tr>
    </tbody>
</table>



### Task 4




##### Display average payload mass carried by booster version F9 v1.1



```sql
%%sql SELECT AVG(Payload_Mass__kg_) AS Avg_Payload
FROM SPACEXTABLE
WHERE Booster_Version = 'F9 v1.1';
```

     * sqlite:///my_data1.db
    Done.





<table>
    <thead>
        <tr>
            <th>Avg_Payload</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>2928.4</td>
        </tr>
    </tbody>
</table>



### Task 5

##### List the date when the first succesful landing outcome in ground pad was acheived.


_Hint:Use min function_ 



```sql
%%sql
SELECT MIN(Date) AS First_GroundPad_Success
FROM SPACEXTABLE
WHERE Landing_Outcome = 'Success (ground pad)';
```

     * sqlite:///my_data1.db
    Done.





<table>
    <thead>
        <tr>
            <th>First_GroundPad_Success</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>2015-12-22</td>
        </tr>
    </tbody>
</table>



### Task 6

##### List the names of the boosters which have success in drone ship and have payload mass greater than 4000 but less than 6000



```sql
%%sql
SELECT Booster_Version
FROM SPACEXTABLE
WHERE Landing_Outcome = 'Success (drone ship)'
AND Payload_Mass__kg_ > 4000
AND Payload_Mass__kg_ < 6000;
```

     * sqlite:///my_data1.db
    Done.





<table>
    <thead>
        <tr>
            <th>Booster_Version</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>F9 FT B1022</td>
        </tr>
        <tr>
            <td>F9 FT B1026</td>
        </tr>
        <tr>
            <td>F9 FT  B1021.2</td>
        </tr>
        <tr>
            <td>F9 FT  B1031.2</td>
        </tr>
    </tbody>
</table>



### Task 7




##### List the total number of successful and failure mission outcomes



```sql
%%sql
SELECT Landing_Outcome, COUNT(*) AS Total
FROM SPACEXTABLE
GROUP BY Landing_Outcome;
```

     * sqlite:///my_data1.db
    Done.





<table>
    <thead>
        <tr>
            <th>Landing_Outcome</th>
            <th>Total</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Controlled (ocean)</td>
            <td>5</td>
        </tr>
        <tr>
            <td>Failure</td>
            <td>3</td>
        </tr>
        <tr>
            <td>Failure (drone ship)</td>
            <td>5</td>
        </tr>
        <tr>
            <td>Failure (parachute)</td>
            <td>2</td>
        </tr>
        <tr>
            <td>No attempt</td>
            <td>21</td>
        </tr>
        <tr>
            <td>No attempt </td>
            <td>1</td>
        </tr>
        <tr>
            <td>Precluded (drone ship)</td>
            <td>1</td>
        </tr>
        <tr>
            <td>Success</td>
            <td>38</td>
        </tr>
        <tr>
            <td>Success (drone ship)</td>
            <td>14</td>
        </tr>
        <tr>
            <td>Success (ground pad)</td>
            <td>9</td>
        </tr>
        <tr>
            <td>Uncontrolled (ocean)</td>
            <td>2</td>
        </tr>
    </tbody>
</table>



### Task 8



##### List all the booster_versions that have carried the maximum payload mass, using a subquery with a suitable aggregate function.



```sql
%%sql
SELECT Booster_Version
FROM SPACEXTABLE
WHERE Payload_Mass__kg_ = (
    SELECT MAX(Payload_Mass__kg_)
    FROM SPACEXTABLE
);
```

     * sqlite:///my_data1.db
    Done.





<table>
    <thead>
        <tr>
            <th>Booster_Version</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>F9 B5 B1048.4</td>
        </tr>
        <tr>
            <td>F9 B5 B1049.4</td>
        </tr>
        <tr>
            <td>F9 B5 B1051.3</td>
        </tr>
        <tr>
            <td>F9 B5 B1056.4</td>
        </tr>
        <tr>
            <td>F9 B5 B1048.5</td>
        </tr>
        <tr>
            <td>F9 B5 B1051.4</td>
        </tr>
        <tr>
            <td>F9 B5 B1049.5</td>
        </tr>
        <tr>
            <td>F9 B5 B1060.2 </td>
        </tr>
        <tr>
            <td>F9 B5 B1058.3 </td>
        </tr>
        <tr>
            <td>F9 B5 B1051.6</td>
        </tr>
        <tr>
            <td>F9 B5 B1060.3</td>
        </tr>
        <tr>
            <td>F9 B5 B1049.7 </td>
        </tr>
    </tbody>
</table>



### Task 9


##### List the records which will display the month names, failure landing_outcomes in drone ship ,booster versions, launch_site for the months in year 2015.

**Note: SQLLite does not support monthnames. So you need to use  substr(Date, 6,2) as month to get the months and substr(Date,0,5)='2015' for year.**



```sql
%%sql
SELECT 
    substr(Date, 6, 2) AS Month,
    Landing_Outcome,
    Booster_Version,
    Launch_Site
FROM SPACEXTABLE
WHERE substr(Date,1,4) = '2015'
AND Landing_Outcome LIKE 'Failure%';
```

     * sqlite:///my_data1.db
    Done.





<table>
    <thead>
        <tr>
            <th>Month</th>
            <th>Landing_Outcome</th>
            <th>Booster_Version</th>
            <th>Launch_Site</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>01</td>
            <td>Failure (drone ship)</td>
            <td>F9 v1.1 B1012</td>
            <td>CCAFS LC-40</td>
        </tr>
        <tr>
            <td>04</td>
            <td>Failure (drone ship)</td>
            <td>F9 v1.1 B1015</td>
            <td>CCAFS LC-40</td>
        </tr>
    </tbody>
</table>



### Task 10




##### Rank the count of landing outcomes (such as Failure (drone ship) or Success (ground pad)) between the date 2010-06-04 and 2017-03-20, in descending order.



```sql
%%sql
SELECT Landing_Outcome, COUNT(*) AS Outcome_Count
FROM SPACEXTABLE
WHERE Date BETWEEN '2010-06-04' AND '2017-03-20'
GROUP BY Landing_Outcome
ORDER BY Outcome_Count DESC;
```

     * sqlite:///my_data1.db
    Done.





<table>
    <thead>
        <tr>
            <th>Landing_Outcome</th>
            <th>Outcome_Count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>No attempt</td>
            <td>10</td>
        </tr>
        <tr>
            <td>Success (drone ship)</td>
            <td>5</td>
        </tr>
        <tr>
            <td>Failure (drone ship)</td>
            <td>5</td>
        </tr>
        <tr>
            <td>Success (ground pad)</td>
            <td>3</td>
        </tr>
        <tr>
            <td>Controlled (ocean)</td>
            <td>3</td>
        </tr>
        <tr>
            <td>Uncontrolled (ocean)</td>
            <td>2</td>
        </tr>
        <tr>
            <td>Failure (parachute)</td>
            <td>2</td>
        </tr>
        <tr>
            <td>Precluded (drone ship)</td>
            <td>1</td>
        </tr>
    </tbody>
</table>



### Reference Links

* <a href ="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Labs_Coursera_V5/labs/Lab%20-%20String%20Patterns%20-%20Sorting%20-%20Grouping/instructional-labs.md.html?origin=www.coursera.org">Hands-on Lab : String Patterns, Sorting and Grouping</a>  

*  <a  href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Labs_Coursera_V5/labs/Lab%20-%20Built-in%20functions%20/Hands-on_Lab__Built-in_Functions.md.html?origin=www.coursera.org">Hands-on Lab: Built-in functions</a>

*  <a  href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Labs_Coursera_V5/labs/Lab%20-%20Sub-queries%20and%20Nested%20SELECTs%20/instructional-labs.md.html?origin=www.coursera.org">Hands-on Lab : Sub-queries and Nested SELECT Statements</a>

*   <a href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Module%205/DB0201EN-Week3-1-3-SQLmagic.ipynb">Hands-on Tutorial: Accessing Databases with SQL magic</a>

*  <a href= "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Module%205/DB0201EN-Week3-1-4-Analyzing.ipynb">Hands-on Lab: Analyzing a real World Data Set</a>




## Author(s)

<h4> Lakshmi Holla </h4>


## Other Contributors

<h4> Rav Ahuja </h4>


<!--
## Change log
| Date | Version | Changed by | Change Description |
|------|--------|--------|---------|
| 2024-07-10 | 1.1 |Anita Verma | Changed Version|
| 2021-07-09 | 0.2 |Lakshmi Holla | Changes made in magic sql|
| 2021-05-20 | 0.1 |Lakshmi Holla | Created Initial Version |
-->


## <h3 align="center"> © IBM Corporation 2021. All rights reserved. <h3/>

