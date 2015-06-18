
*Basic inference*

**Inverse properties**

        SELECT *
        WHERE { {?s ?p ?o . ?o ?p ?s } OPTIONAL { ?a ?b ?c FILTER ( ?a = ?o && ?c = ?s ) } }

*Basic graph statistics*

**Number of nodes**

The number of nodes equals to the sum of distinct subjects or objects.

    SELECT (COUNT (DISTINCT ?node) AS ?nNum)
    WHERE { { ?node ?p ?obj } UNION { ?obj ?p ?node } }

or in a more compact form using query path:

    SELECT (COUNT (DISTINCT ?node) AS ?nNum)
    WHERE { ?node ?p | ^?p ?obj }

**Number of directed edges**

The number of directed edges equals to the number of triples.

    SELECT (COUNT (*) AS ?eNum)
    WHERE { ?s ?p ?o }

**Number of undirected edges**

Number of single edges (only one triple between two nodes)

        SELECT (COUNT (*) AS ?eNum)
        WHERE { ?s ?p ?o 
                MINUS { ?o ?y ?s } }

**Directed Graph density**

Graph density is defined as the ratio between the number of edges and the number of all possible edges (i.e. every node connects to all others via outgoing and incoming links).

    SELECT (?eNum/(?nNum *(?nNum - 1.0)) AS ?density)
    WHERE {
      { SELECT (COUNT (*)/2 AS ?eNum) (COUNT (DISTINCT ?node) AS ?nNum)
        WHERE { { ?node ?p ?obj } UNION { ?obj ?p ?node } }
      }
    }

**Undirected graph density**




**Shortest path between two nodes**

