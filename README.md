# apppa

![appa meets aang](https://github.com/MrPowers/appa/blob/master/images/appa-meets-aang.jpeg)

Data lake metadata and transaction log store.

## Project purpose

### File metadata

Storing metadata about files allows you to intelligently skip data files when querying data to make computations run faster.  You can also run some queries on the metadata itself.

Your metadata needs to be specific to your data and query patterns.  I can't tell you what you should store in your metadata layer.  You need to look at how you interact with your data to determine the metadata fields that will allow for data skipping.

### Transaction log

The transaction log records files that are added and removed from the data lake.  The transaction log allows for powerful features like time travel, versioned data, and backwards compatible compaction.

## Comparison to Delta lake

This project is inpired by [Delta lake](https://delta.io/).  It has the following important differences:

* It's not intended to be compatible with Hive, so it can implement "disk partitioning" more efficiently
* Appa doesn't support streaming, so it's less complex
* Appa is intended to work with different types of technologies, not just Spark.  The Delta philosophy is "do all your data lake management processes with Spark".  The Appa philosophy is "use Spark, Dask, Pandas, or pure Python to manage the different operations".  Spark is a good technology for certain data lake operations, but not the best for operations like deleting files in S3.
* Small file compaction is a first class citizen in Appa

## High level overview

Suppose you have the following data files.

`file1.csv`

```
full_name,birth_year,country
Confucius,551,china
Deng Xiaoping,1904,china
Fan Bingbing,1982,china
```

`file2.csv`

```
full_name,birth_year,country
Mahatma Gandhi,1948,india
Amartya Sen,1933,india
Priyanka Chopra,1982,india
```

`file3.csv`

```
full_name,birth_year,country
Shaggy,1968,jamaica
Usain Bolt,1986,jamaica
Chetan Bhagat,1974,india
Diego Maradona,1960,argentina
```

Let's create a metadata store for these files:

```
file_name,countries,max_birth_year
file1.csv,[china],1982
file2.csv,[india],1982
file3.csv,[jamaica,india,argentina],1986
```

Here's how the metadata store allow us to run queries faster:

* For `where country = 'china'`, we can query file1 and skip file2 and file3
* For `where birth_year > 1985`, we can query file3
* For `where country in ('jamaica', 'india')'`, we can query file2 or file 3

Appa gives you the flexibility to choose which metadata you'll store.  Generating metadata takes computational resources so you don't want to store metadata needlessly.

## Generating metadata

TODO

