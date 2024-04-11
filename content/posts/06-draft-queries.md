---
title: Query basics
date: 2024-11-30
series:
  - Logseq queries for beginners
tags:
  - Logseq
TocOpen: false
draft: true
---
Think of all the data as living in one giant table. Each record in the table will have 3 things.
1. An entity identifier
2. An attribute 
3. A value belonging to that entity attribute combination

We call these tuples and they look like:
`[entity attribute value]`
This is most of what advanced queries are made up of. A series of tuples to define what data we are looking for.
In the query language when we don't know what something is we can replace that with a variable to be filled in, this is anything starting with `?` followed by letters, not sure about other symbols.

Generally we have no idea what identifier a block has in the big table. So we use a variable like `?b` or `?block`.

`:block/refs` is the name of a specific attribute available to the entity of type block.
The value for this attribute will be a list of one or more other entities in the big table.
These entities can be either blocks or pages themselves, with their own set of attributes.

So when you query for blocks which are tagged with `#note`, you are looking for tuples of `[?block :block/refs ?note]` (we don't know the entity identifier of note)
Then we have to specify what `?note` we want, else we get everything.
In this case we want the page with the name `note`.
The tuple for that would be `[?note :block/name "note"]`. In this case the attribute `:block/name` is always the lowercase name of a page.
So we want entities who's name is "note". As pages need unique names in Logseq, this is only 1 result.
Therefore with those two tuples we get all blocks that have a reference/link of either `[[note]]` or `#note`.