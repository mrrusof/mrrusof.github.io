---
layout: post
title: Duplicated Users
date: 2026-11-08
author: Ruslan Ledesma-Garza
summary: For one of the web applications we support, we know that some users have registered more than once.  Having duplicated users is a problem because it distorts usage metrics for the application and thus complicates business decisions.  So we need you to write a program that finds duplicated users.
---

{{page.summary}}

Because duplicated users rarely have the same values for all
attributes, we want to assign a degree of similarity to users that are
potential duplicates of each other.  So, we say that one user is a
potential duplicate of another if they have the same value for at
least one attribute.  We also say that one user has a low similarity
with another if they only share one attribute, a medium similarity if
they share two attributes and a high similarity if they share three or
more.  If an attribute is missing or empty for two given users, it is
considered a shared attribute.

**Input.**
The input is a CSV file of users as in the following example.  The
input comes into the program via stdin.  The rows are sorted by
user id in ascending order.

```asciidoc
id,first_name,last_name,email,zip_code,address
1,A,S,ana@example.com,,123 Maple St.
2,A,Sanchez,ana@example.com,48271,123 Maple St.
3,Alicia,S,alicia@example.com,48271,
4,Carlos,M,carlos@example.com,64753,654 Pamela Dr.
```

**Output.** For the example input, the output is the following CSV.
The program prints the CSV to stdout.  Each line relates two potential
duplicate users such that `id1` is canonical and indicates how similar
they are ("High", "Medium", "Low").  A user is canonical if it is the
smallest id for a given set of similar users. For example, user 1 is
canonical for the set of users `{1, 2}` that are similar over
`(first_name, email) = ('A', 'ana@example.com')`.  Users may appear at
most once in column `id2` and they are paired with the most similar
canonical user, breaking ties by choosing the smallest id.  Users
without potential duplicates do not appear in the output.  No user
appears in columns `id1` and `id2` of the same row.  The output rows
are sorted by column `id2`.

```asciidoc
id1,id2,similarity
1,2,High
1,3,Low
```

# Naive Approach

We approach the problem by indexing users by their attributes and then
we use the indices to find for each user another smaller user that is
most similar and smallest.

We now illustrate the approach on the example input CSV.

The indexing consists in mapping each value of each column to the set
of users that have that value.  We build the index $@I_k@$ for the
k-th column in the following way.  For each value $@v@$ in the k-th
column, assign $@v@$ to the empty set, $@I_k(v) := \lbrace\rbrace@$.
Then visit each cell in the k-th column from the first row to the
last.  When visiting a cell with value $@v@$, add the id $@i@$ of the
corresponding user to the set $@I_k(v)@$, $@I_k(v) := I_k(v) \cup
\lbrace i \rbrace@$.  For example, the indices for the example input
CSV are the following.

```asciidoc
first_name
A      -> {1, 2}
Alicia -> {3}
Carlos -> {4}

last_name
S       -> {1, 3}
Sanchez -> {2}
M       -> {4}

email
ana@example.com    -> {1, 2}
alicia@example.com -> {3}
carlos@example.com -> {4}

zip_code
<empty string> -> {1}
48271          -> {2, 3}
64753          -> {4}

address
'123 Maple St.'  -> {1, 2}
<empty string>   -> {3}
'654 Pamela Dr.' -> {4}
```

For each user, we find a smaller most similar and smallest user in the
following way.  For the k-th attribute of the user, convert its value
$@v@$ to the set of user ids $@I_k(v)@$ minus all the ids that are
greater or equal to the id of the user, so $@I_k(v) \cap \lbrace
j \mid j < i \rbrace@$.  Select the most frequent user id, breaking ties by
choosing the smallest id.  For example, the sets of user ids
corresponding to each attribute of each user of the input CSV are the
following.

```asciidoc
TABLE OF SETS OF IDS
---------------------------------------------------
id  first_name  last_name  email  zip_code  address
1   {}          {}         {}     {}        {}
2   {1}         {}         {1}    {}        {1}
3   {}          {1}        {}     {2}       {}
4   {}          {}         {}     {}        {}
```

Then the most frequent and smallest user id for each user and
their similarity is as follows.

```asciidoc
user  smaller_most_frequent_and_smallest_user   similarity
1     -                                         -
2     1                                         High
3     1                                         Low
4     -                                         -
```

Using the previous result we can now print the answer in the following
way.  First, discard those rows where there is no most frequent and
smallest user (for example, discard the first and last rows).  Second,
print the column of most frequent and smallest user as `id1` and the
first column as `id2`.  The result is as in the example output.

```asciidoc
id1,id2,similarity
1,2,High
1,3,Low
```

This approach is naive because it takes $@O(n^2)@$, where $@n@$ is the
number of input rows, to find the most frequent and smallest id for
each user by inspecting the `TABLE OF SETS OF IDS`.  The worst case
happens when each column has the same value for every user: each set
in the `TABLE OF SETS OF IDS` contains $@i - 1@$ users with ids
smaller than the id of the current row, where $@i@$ is the 1-based index of
the current row, so processing one user takes $@O(n)@$ time for a
fixed number of columns (meaning the number of columns remains
constant across input CSV files).  The building of the indices is not
problematic because it takes $@O(n)@$ for a fixed number of columns.


# Solution Approach

There is a way to achieve linear time $@O(n)@$ for a fixed number of
columns.  The key to doing that is indexing combinations of column
values instead of individual column values.  This indexing consists in
building an index for each of the $@2^k - 1@$ nonempty subsets of the
$@k@$ attribute columns.  Then when we look up the most frequent and
smallest id for a given row, we consider the indices for subsets going
from larger to smaller subsets until we find a match.  Because the
number of subsets is fixed when the number of columns $@k@$ is fixed,
the building of indices and the finding of the smallest canonical user
for each row each take linear time $@O(n)@$ on the number of rows
$@n@$.

For each combination of columns and values, the corresponding index
stores the smallest user id having those values. Therefore, if
multiple users share the same values for a given subset of columns, we
only need to remember the smallest id. Any other user with those
values cannot be a better candidate for that subset because it has the
same similarity and a greater id.

We now illustrate the solution approach on the example input CSV.

The indexing step produces one index for each subset of the input
columns, except the `id` column, in the following way.  For each row
from top to bottom of the input CSV, and for each subset of columns
$@S@$, map the values $@v_1, ..., v_j@$ of the subset of columns to
the user id $@i@$ of the row, $@I_S(v_1, ..., v_j) = i@$ when
$@I_S(v_1, ..., v_j)@$ is unset.  For example, some of the indices we
build for the example input CSV are the following.

```asciidoc
SUBSET {first_name, last_name, email, zip_code, address}:
  (A, S, ana@example.com, '', '123 Maple St.')             -> 1
  (A, Sanchez, ana@example.com, 48271, '123 Maple St.')    -> 2
  (Alicia, S, alicia@example.com, 48271, '')               -> 3
  (Carlos, M, carlos@example.com, 64753, '654 Pamela Dr.') -> 4

...

SUBSET {first_name, email, address}:
  (A, ana@example.com, '123 Maple St.')          -> 1
  (Alicia, alicia@example.com, '')               -> 3
  (Carlos, carlos@example.com, '654 Pamela Dr.') -> 4

...

SUBSET {first_name, email}:
  (A, ana@example.com)         -> 1
  (Alicia, alicia@example.com) -> 3
  (Carlos, carlos@example.com) -> 4

...

SUBSET {email, address}:
  (ana@example.com, '123 Maple St.')     -> 1
  (alicia@example.com, '')               -> 3
  (carlos@example.com, '654 Pamela Dr.') -> 4

...

SUBSET {last_name}:
  (S)       -> 1
  (Sanchez) -> 2
  (M)       -> 4

...

SUBSET {zip_code}:
  ('')    -> 1
  (48271) -> 2
  (64753) -> 4

...
```

With the indices for all subsets of columns, we now find the canonical
id that is smallest for each of the rows of the input CSV, if it
exists, in the following way.  Take one input row and lookup the row's
values in the corresponding indices, from the biggest subset
cardinality to smallest, until we find an id that is not the id of the
given row.  For example, when we consider the second input row with id
2, we start by applying the index for the biggest subset `{first_name,
last_name, email, zip_code, address}` and we get id 2 but that is the
id of the row, so we continue.  Then we consider the indices for all
subsets of cardinality 4 and none returns a match for the row.  Then
we consider the indices for subsets of cardinality 3 and find that the
index for subset `{first_name, email, address}` returns id 1 because
of the mapping `(A, ana@example.com, '123 Maple St.') -> 1`.  Because
no other index of cardinality 3 returns an id, we select id 1 as the
canonical id for this row (user id 2).

When multiple indices for the same subset cardinality return an id, we
break ties by selecting the smallest id.  For example, when we
consider row 3 (user id 3), we don't find a match until we consider
the indices of cardinality 1.  We find two matches, index for
`{last_name}` returns id 1 and index for `{zip_code}` returns id 2.
We select the smallest id of the two and assign to row 3 (user id 3)
the id 1 as its canonical user.

Because we examine subsets from largest to smallest, the first
cardinality for which we find a matching user gives us the greatest
possible similarity. If multiple indices of that cardinality return
users, selecting the smallest id ensures that we also satisfy the
tie-breaking rule.

Finally, the result is the same as in the example output CSV.


# Implementation

We implement the solution approach in Python as follows.  A critical
implementation detail is that we hash the input cells so that we can
expect constant time hash lookups.  This hashing is intended to
highlight to you, the reader, the importance of addressing the
limitations of the chosen programming language and it is not
guaranteed to give constant time hash lookups or avoid collisions in
its current form.

{% highlight python %}
########################################
# Read input

def read_input_csv():
    import sys
    import csv

    reader = csv.reader(sys.stdin)

    _header = next(reader)

    rows = []
    for row in reader:
        rows.append(row)

    return rows


########################################
# Preprocessing input

def hash_string(s):
    # Hashing as 32-bit integer ensures that integer comparisons
    # happen in constant time as opposed to linear time for bignums.
    return abs(hash(s.strip())) & 0xFFFFFFFF 


def hash_rows(rows):
    hashed_rows = []

    for row in rows:
        hashed_row = [int(row[0])]

        for cell in row[1:]:
            hashed_row.append(hash_string(cell))

        hashed_rows.append(hashed_row)

    return hashed_rows


########################################
# Indexing

def count_cols(rows):
    first = rows[0]
    return len(first)


def subsets_count_for_cardinality(cardinality):
    return 2 ** cardinality


def get_bits(n):
    ones_count = 0
    bits = []

    while n > 0:
        if n & 1 == 1:
            ones_count += 1
            bits.append(1)
        else:
            bits.append(0)
            
        n = n >> 1

    return [bits, ones_count]


class SubsetIndex:
    def __init__(self, index_id):
        bits, ones_count = get_bits(index_id)

        self.key_mask = bits
        self.key_cardinality = ones_count
        self.data = {}


    def key_for_row(self, row):
        key = []
    
        for i, bit in enumerate(self.key_mask):
            if bit == 1:
                key.append(row[1 + i]) # skip the id, first column
    
        return tuple(key)

    
    def put(self, row, v):
        key = self.key_for_row(row)
        self.data[key] = v


    def get(self, row):
        key = self.key_for_row(row)
        return self.data.get(key)


def init_indices(col_count):
    all_indices = []
    indices_by_cardinality = {}
    subsets_count = subsets_count_for_cardinality(col_count - 1)

    for i in range(1, subsets_count):
        index = SubsetIndex(i)

        indices_by_cardinality.setdefault(index.key_cardinality, [])
        indices_by_cardinality[index.key_cardinality].append(index)

        all_indices.append(index)

    return [all_indices, indices_by_cardinality]


def index(rows):
    col_count = count_cols(rows)
    all_indices, indices_by_cardinality = init_indices(col_count)

    for row in rows:
        for index in all_indices:
            if index.get(row) is None:
                row_id = row[0]
                index.put(row, row_id)

    return indices_by_cardinality


########################################
# Smallest canonical users/rows lookup

def find_most_frequent_and_smallest_row(row, indices_by_cardinality):
    row_id = row[0]
    col_count = len(row)

    for cardinality in range(col_count - 1, 0, -1):
        most_frequent_and_smallest_row = None

        for index in indices_by_cardinality[cardinality]:
            similar_id = index.get(row)

            if similar_id is None or similar_id == row_id:
                continue

            if most_frequent_and_smallest_row is None or similar_id < most_frequent_and_smallest_row:
                most_frequent_and_smallest_row = similar_id

        if not most_frequent_and_smallest_row is None:
            return [most_frequent_and_smallest_row, cardinality]

    return [None, 0]


def find_smallest_canonical_rows(rows, indices_by_cardinality):
    result = []

    for row in rows:
        row_id = row[0]
        smallest_canonical_row, similarity = find_most_frequent_and_smallest_row(row, indices_by_cardinality)

        result.append([row_id, smallest_canonical_row, similarity])
    
    return result


########################################
# Pretty printing

def pretty_similarity_value(similarity):
    if similarity < 2:
        return 'Low'
    elif similarity < 3:
        return 'Medium'
    else:
        return 'High'


def pretty_print(result_rows):
    print("id1,id2,similarity")

    for row in result_rows:
        id1 = row[1]
        id2 = row[0]
        similarity = pretty_similarity_value(row[2])

        if id1 is None:
            continue
        
        print(f"{id1},{id2},{similarity}")


def main():
    rows = read_input_csv()

    if len(rows) == 0:
        return

    hashed_rows = hash_rows(rows)
    indices_by_cardinality = index(hashed_rows)
    rows_with_smallest_canonical_row = find_smallest_canonical_rows(hashed_rows, indices_by_cardinality)
    pretty_print(rows_with_smallest_canonical_row)


main()
{% endhighlight %}

# A Final Note

ChatGPT (OpenAI) served as a technical editor for this article,
providing proofreading, technical review, and suggestions for
improving the explanations. The algorithm and implementation are my
own...

...that said, always challenge your technical editors XD.

<img style="width: 100%" src="/assets/2026-11-08-always-challenge-your-technical-editors.png"/>


{% include share.html %}


{% include comments.html %}
