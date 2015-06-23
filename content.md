##Basic inference

###Inverse properties

    SELECT ?p ?q
    WHERE {
      { ?s ?p ?o . ?o ?p ?s }
      OPTIONAL 
      { ?a ?q ?c FILTER ( ?a = ?o && ?c = ?s ) }
    }

##Basic graph statistics

Notations:

    N: set of nodes
    E: set of edges
    <N, E>: a graph consists of nodes in N and edges in E
    ?nNum: number of nodes in SPARQL queries, equals to |N|
    ?eNum: number of edges in SPARQL queries, equals to |E|

###Number of nodes

The number of nodes equals to the sum of distinct subjects or objects.

    SELECT ( COUNT ( DISTINCT ?node ) AS ?nNum )
    WHERE { 
      { ?node ?p ?obj }
      UNION
      { ?obj ?p ?node }
    }

or in a more compact form using query path:

    SELECT ( COUNT ( DISTINCT ?node ) AS ?nNum )
    WHERE { ?node ?p | ^?p ?obj }

###Number of directed edges

The number of directed edges equals to the number of triples.

    SELECT ( COUNT ( * ) AS ?eNum )
    WHERE { ?s ?p ?o }

###Number of undirected edges

Number of undirected single edges (only one triple between two nodes)

    SELECT ( COUNT ( * ) AS ?seNum )
    WHERE {
      ?s ?p ?o 
        FILTER NOT EXISTS { ?o ?q ?s } 
    }

or

    SELECT ( COUNT ( * ) AS ?seNum )
    WHERE {
      ?s ?p ?o 
        MINUS { ?o ?q ?s } 
    }

Number of undirected multiple edges (more than one triples between two nodes)

    SELECT ( COUNT ( * ) / 2 AS ?meNum )
    WHERE {
      SELECT DISTINCT ?s ?o
      WHERE {
        ?s ?p ?o 
          FILTER EXISTS { ?o ?q ?s }
      }
    }

Number of undirected edges

    SELECT ( COUNT ( * ) / 2 AS ?eNum )
    WHERE {
      SELECT DISTINCT ?s ?o
      WHERE {
        { ?s ?p ?o }
        UNION
    	{ ?o ?q ?s }
      }
    }

###Directed Graph density

Graph density is defined as the ratio between the number of edges and the number of all possible edges (i.e. every node connects to all others via outgoing and incoming links). In a directed graph, the number of possilbe edges is `|N|*(|N|-1)`, and the number of acutual edges equals to the number of triples.

    SELECT ( ?eNum / ( ?nNum * ( ?nNum - 1.0 ) ) AS ?density )
    WHERE {
      { SELECT ( COUNT ( * ) / 2 AS ?eNum ) ( COUNT ( DISTINCT ?node ) AS ?nNum )
        WHERE {
          { ?node ?p ?obj }
          UNION
          { ?obj ?p ?node }
        }
      }
    }

###Undirected graph density
In a undirected graph a linked \<subject, object\> pair counts as one edge. The number of possilbe edges is `|N|*(|N|-1)/2`, and the number of acutual edges equals to the number of distinct \<subject, object\> pairs devided by 2.

    SELECT ( ?eNum / ( ?nNum * ( ?nNum - 1.0 ) / 2 ) AS ?density )
    WHERE {
      SELECT ( COUNT ( * ) / 2 AS ?eNum ) ( COUNT ( DISTINCT ?s ) AS ?nNum )
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

