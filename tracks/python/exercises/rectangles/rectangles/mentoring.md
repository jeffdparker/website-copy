# Problem and Challenges

We are given a list of strings, and asked to count the number of rectangles.  
```
   +--+
  ++  |
+-++--+
|  |  |
+--+--+
```
The above diagram contains 6 rectangles.

Typical solutions to the Rectangles problem take the following steps.

## Locate possible corners

Corners are marked with a '+': we wish to have the (row, col) pairs for each
'+' in the input.

We need to traverse of the list of strings, recording the location of each '+'.
This is most naturaly done with enumerate(), so we have the index and the value.
```
    corners = []
    for row_index, row in enumerate(rows):
        for col_index, ch in enumerate(row):
            if ch == '+':
                corners.append((row_index, col_index))
```

We can find the corners using a List Comprehension:
```
    corners = [
        (row_index, col_index)
            for row_index, row in enumerate(rows)
                for col_index, cell in enumerate(row)
                    if cell == "+"
    ]
```

## Identifying possible rectangles

Given 'corners', a list of tuples, we wish to identify possible rectangles.

We can simplify our counting if we have cannonical representation: 
we can look for the Upper Left (UL) corner and a matching
Lower Right (LR) corner in the list.  

## Identify valid rectangles

We can winnow the pairs by assuring that UL is above and to the left of LR
```
    candidates = [(upper, lower)
                    for upper in corners
                        for lower in corners
                            if (upper[0] < lower[0]) and (upper[1] < lower[1])]
```
Since the LR will always follow the UL, we can keep track of the index of
'upper' and only walk the remainder of the list of corners looking for LR.
This reduces the amount of time finding the corners by a factor of two,
but this is not the bottleneck in the problem.

Look for a definition of a function to take a pair of tuples and check to
see if they mark a legal rectangle.  The test above can come before
calling the function, or within the function.

```
def valid_rectangle(rows: List[str], tup: tuple[tuple[int]]) -> bool:
    ...
```

### Check Four Corners

The next step checks to see that Upper Right and Lower Left corners exist.
```
    if rows[upper[0]][lower[1]] == '+' and rows[lower[0]][upper[1]] == '+':
        ...
```

### Validate Rows and Columns

If all four corners exist, we can check to see if the paths between the corners
are valid: '-' or '+' for a horizontal edge, and '|' or '+' for a vertical edge.

We check the upper edge below:
```
    row = rows[upper[0]]
    for ch in row[upper[1]:lower[1]]:
        if ch not in "-+":
            return False
```

It is useful to define a function to check rows.  This is a good place to
use all().
```
def check_row(row, left, right) -> bool:
    """Is this a valid row edge?"""
    return all(ch in "-+" for ch in row[left:right])


def check_col(rows, col, upper, lower) -> bool:
    """Is this a valid col edge?"""
    return all(rows[row][col] in "|+" for row in range(upper, lower))
```

We could write a function that takes an extra parameter defining the direction, 
and checks either row or column.

## Counting Valid Rectangles

Pairs that pass the tests above mark a rectangle that should be counted.

Below we take a list of candiate pairs, and winnow them to a list of 
legal rectangles.  We return the length of list.

```
    rectangles = [tup for tup in candidates if valid_rectangle(rows, tup)]

    return len(rectangles)
```

There is no point in building the list of legal rectangles: we can
generate the candidates using a Generator Comprehension, and count 
the valid rectangles using sum()
```
    return sum(1 for tup in candidates if valid_rectangle(rows, tup))
```
