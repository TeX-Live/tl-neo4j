TeX Live and Neo4j
==================

This project provides scripts to convert a full history of TeX Live Databases
into a graph database, in particular Neo4j.

The entities that are created are

Nodes
-----

There are several types of nodes (labels):

  - `File`: only attribute is `name`
  - `TLPDB`: represents a TeX Live Database on a certain time/svn revision.
     The attributes are: 

     - `date` (a Neo4j `Date` object) gives the date the tlpdb was created
     - `current` (Bool) whether it is the latest version of the tlpdb
     - `revision` (Integer) the revision of the tlpdb

then there are nodes with each of them having two labels attached, one either
begin `TLPOBJ` or `TLPSRC`, the other is one of `Scheme`, `Collection`,
`Package`, `TLCore`, `ConTeXt`. These separation reflects the package structure
of TeX Live.

  - `TLPOBJ` has attributes:

    - `name` (String) the name of the package
    - `revision` (Integer) the revision of the package

  - `TLPSRC` has only one attribute, `name` (as above)

Relations
---------

There are five types of relations:

  - `contains`: expresses which `TLPOBJ` are contained in a particular `TLPDB`,
     thus `(t:TLPDB) -[:contains]-> (p:TLPOBJ)`

  - `depends`: expresses the dependency between `TLPOBJ` (tlpdb `depend`),
     thus `(p:TLPOBJ) -[:depends]-> (q:TLPOBJ)`

  - `includes`: expresses which `File`s are included in which `TLPOBJ`,
     thus `(p:TLPOBJ) -[:includes]-> (f:File)`

  - `instanceOf`: expresses which `TLPOBJ` is an instance of a `TLPSRC`,
     thus `(p:TLPOBJ) -[:instanceOf]-> (s:TLPSRC)`

  - `next`: relation between `TLPDB` giving their order (implicit available
     also via the date of course)

 
Access to the resulting graph
-----------------------------

Most of the TeX Live Databases from 2018 have been included into a graph and
it is available at http://texlive.info:7474/browser/ (user/pass: neo4j) in
read-only mode.


Code
----

The main scripts are `tl-dump-neo4j` which creates csv files. These files can 
either be used to create the initial version of a database using the Neo4j
import tool, or further processes by `create-cypher-statements` to convert
into a set of cypher code that updates a already present graph with new information
from a tlpdb. See `do-all` on how to convert a large set of tlpdbs into a graph.

Documentation
-------------

By now there is hardly any documentation besides what is written above.

The directory `slides` contains a presentation held at the Japan Neo4j user Group
Meeting in Tokyo in September 2018 (in Japanese).

License and copyright
---------------------

All scripts and material is (c) 2018  Norbert Preining, licensed under the GPLv3 
or any later version. Some of the images used in the slides are excluded from this.

