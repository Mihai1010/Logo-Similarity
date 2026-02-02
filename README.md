# Logo-Similarity

# Overview

This project is a logo similarity detection system that compares two logos and determine if they are similar or not.

Given a parquet file whcih contains a list of domains, the notebook does the following:

1. Normalize the domain names in order to be able to acces the pages.
2. Fetch logos from logo.dev first and if it fails, it uses Brandfetch.
3. Save the logos in a directory.

After extracting the logos, the notebook starts preprocessing the logos by converting them to grayscale and resizing them to 256x256 pixels, then uses imagehash.phash to generate a 256-bit hash.

The next phase is using NetworkX to create a graph from the hash values. Each domain with a valid logo hash is a node, and an edge is created between two nodes if the hamming distance between their hash values is less than or equal to 10. A lower hamming distance means the logos are more similar.

Then, it computes the connected components of the graph and outputs the domains in each component.


# Data Files

1 . Input
  
  1. logos.snappy.parquet
    
    A Parquet file where:
    
    1. The first column is assumed to contain domain names (e.g. example.com).
    
    2. The notebook currently uses df.iloc[:, 0] to read domains, so the first column must be domains.

2. Intermediate
  
  1. logos/ directory
    Contains downloaded logo images. Filenames are the normalized domain with dots replaced by underscores and an extension:
    Example: example_com.png or example_com.svg.
  
  2. data_with_logos.snappy.parquet
    
    A Parquet file created by the notebook with additional columns:
    
    1. logo_path
    2. logo_source

3. Output
  
  1. logo_clusters.json
    JSON file containing a list of clusters


# Limitations & Considerations

1. Runtime

Comparing all logo pairs is O(N^2) in the number of logos. With thousands of logos, this can be slow.

2. Network dependence

The logo fetching step depends on external services (logo.dev and Brandfetch).
Rate limiting and network errors are handled simply by skipping the logo.

3. Threshold choice

The distance threshold (≤ 10) is a heuristic and may need tuning for different datasets.