**Basic statistics**

**Number of nodes**

The number of nodes equals to the number of distinct subjects or objects.

    SELECT (COUNT (DISTINCT ?node) AS ?nNum)
    WHERE { ?node ?p ?obj . } UNION { ?obj ?p ?node . }

or in a more compact form using query path:

    SELECT (COUNT (DISTINCT ?node) AS ?nNum)
    WHERE { ?node ?p | ^?p ?obj . }

**Number of edges**

The number of edges equals to the number of triples.

    SELECT (COUNT (*) AS ?eNum)
    WHERE { ?s ?p ?o . }

**Graph density**

Graph density is defined as the ratio between the number of edges and the number of all possible edges (i.e. every node connects to all others).

    SELECT (?eNum/(?nNum *(?nNum - 1.0)) AS ?density)
    WHERE {
      { SELECT (COUNT (*) AS ?eNum) (COUNT (DISTINCT ?node) AS ?nNum)
        WHERE { ?node ?p | ^?p ?obj . }
      }
    }
