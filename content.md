##Basic inference

###Inverse properties

    SELECT ?p ?q
    WHERE {
      { ?s ?p ?o . ?o ?p ?s }
      OPTIONAL 
      { ?a ?q ?c FILTER (?a = ?o && ?c = ?s) }
    }

##Basic graph statistics

Notations:

    N: set of nodes
    E: set of edges
    G=(V, E): a graph consists of nodes in N and edges in E
    ?vNum: number of nodes in SPARQL queries, equals to |V|
    ?eNum: number of edges in SPARQL queries, equals to |E|

###Number of nodes

The number of nodes equals to the sum of distinct subjects and objects.

    SELECT (COUNT (DISTINCT ?node) AS ?vNum)
    WHERE { 
      { ?node ?p ?obj }
      UNION
      { ?obj ?p ?node }
    }

or in a more compact form using property path:

    SELECT (COUNT (DISTINCT ?node) AS ?vNum)
    WHERE { ?node ?p | ^?p ?obj }

###Number of directed edges

The number of directed edges equals to the number of triples.

    SELECT (COUNT (*) AS ?eNum)
    WHERE { ?s ?p ?o }

###Number of undirected edges

Number of triples between two nodes

    SELECT ?s ?o (COUNT (*) AS ?tNum)
    WHERE {
        { ?s ?p ?o }
        UNION
        { ?o ?q ?s }
    }
    GROUP BY ?s ?o

Number of single edges (edges consisting of one triple)

    SELECT ?s ?o (COUNT (*) AS ?tNum)
    WHERE {
        { ?s ?p ?o }
        UNION
        { ?o ?q ?s }
    }
    GROUP BY ?s ?o
    HAVING (COUNT (*) = 1)

Number of multi-edges (edges consisting of more than on triples)

    SELECT ?s ?o (COUNT (*) AS ?tNum)
    WHERE {
        { ?s ?p ?o }
        UNION
        { ?o ?q ?s }
    }
    GROUP BY ?s ?o
    HAVING (COUNT (*) > 1)

Number of undirected edges is the number of linked distinct `(subject, object)` pairs

    SELECT (COUNT (*) / 2 AS ?eNum)
    WHERE {
      SELECT DISTINCT ?s ?o
      WHERE {
        { ?s ?p ?o }
        UNION
        { ?o ?q ?s }
      }
    }

###Directed Graph density

Graph density is defined as the ratio between the number of edges and the number of all possible edges (i.e. every node connects to all others via outgoing and incoming links). In a directed graph, the number of possilbe edges is `|V|*(|V|-1)`, and the number of acutual edges equals to the number of triples.

    SELECT (?eNum / (?vNum * (?vNum - 1.0)) AS ?density)
    WHERE {
      { SELECT (COUNT (*) / 2 AS ?eNum) (COUNT (DISTINCT ?node) AS ?vNum)
        WHERE {
          { ?node ?p ?obj }
          UNION
          { ?obj ?p ?node }
        }
      }
    }

###Undirected graph density
In a undirected graph a linked `(subject, object)` pair counts as one edge. The number of possible edges is `|V|*(|V|-1)/2`, and the number of actual edges equals to the number of distinct `(subject, object)` pairs divided by 2.

    SELECT (?eNum / (?vNum * (?vNum - 1.0) / 2) AS ?density)
    WHERE {
      SELECT (COUNT (*) / 2 AS ?eNum) (COUNT (DISTINCT ?s) AS ?vNum)
      WHERE {
        SELECT DISTINCT ?s ?o
        WHERE {
          { ?s ?p ?o }
          UNION
          { ?o ?q ?s }
        }
      }
    }


**Shortest path between two nodes**
