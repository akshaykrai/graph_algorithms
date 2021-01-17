# Author Name Disambiguation

## Problem Statement

In scientific literature, author signatures are often ambiguous. 

The same person can sign his or her name differently in different publications. For example, David Rey Smith can sign his name as D. Smith, David Smith, D R Smith, etc. 
Similarly, two different individuals can share the same name, making it difficult to credit the publication to the right person.

This problem is called author name disambiguation, or AND for short, and has been an age-old problem in the industry for many years.

## Solution

A number of solutions have cropped up over the years, that leverage all kinds of metadata to disambiguate authors. 

Few novel approaches take into account the co-author network of an individual, with the assumption that same individuals have a common area of interest and are part of a common research peer-group throughout their career. The inherent graphical structure of relationships between authors makes solving this problem using graph algorthims particularly interesting.

One such algorithm defined is LUCID. More on this here: https://ieeexplore.ieee.org/document/8324326

## How it works

The python networkx and cdlib libraries are used as to provide the graph datastructure interface library and community-detection (SCAN) libraries respectively.

There are four parts to the LUCID algorithm itself:

1. First, the graph is initialized with the following rules:
- Each unique author name is created as it's own node on the graph.
- Each node also carries with it the list of publications that are associated with said author name.
- The edges between nodes represent occurences when the two authors of said relationship published a paper together.

2. The community-detection algorithm - SCAN (Structured Clustering Algorithm for Networks) is executed on the graph.
- What makes this algorithm different from regular community-detection algorithms is that apart from communities, it identifies hubs and outliers in the graph.
- Hubs are nodes in the graph that themselves do not belong to any community, but act as a bridge between different communities.
- In business lingo, hubs represent author names that most likely are two or more different individuals as they share completely different author networks.

3. Homonym resolver
- This stage tackels the problem of differentiating between authors that share the same name but have completely different peer-groups.
- At this stage, hubs are split into one or more nodes that are then merged into their own respective communities.
- The splitting is done based on the intersection of the hub node publications and the publications in the communities.
- New relationships are formed between the community nodes and the split node. The old relationships with the original node are discarded.

4. Synonym resolver
- This stage tackles the problem of the same author publishig with different synonyms of their names.
- Each pair of nodes in the community are evaluated based on their fuzzy matching score (Jaro-Winkler distance), their geodesic distance (The shortest path) between them, and lastly same basic rules such as the names must share the same initials.
- Should a pair of nodes satisfy all the above conditions, they are considered to be the same individial and hence merged into a single node containing the publications of both of the previous node.
- The splitting is carried out based on the intersection of publications

Once all 4 stages are done, we are left with a graph containing multiple communities. By assigning an index to each node based on the community it belongs to, and looking at the modified list of publications against each previously ambgiuous author names, one can conclude that authors sharing the same or similar names but having different cluster indexes are, in fact, different individuals.

