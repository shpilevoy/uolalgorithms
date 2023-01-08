# Coursework Assignment: Sudoku assignment

Algorithms and Data Structure I

## Task 1: MakeVector

This function should take a four-element vector called row as an input parameter and return a vector of four elements
called puzzle: each element of puzzle should contain the four-element vector row. Complete this function.

```
function MakeVector(row)
    new Vector puzzle(4)
    
    # Just put row 4 times to the vector.
    # I think i don't need to copy input row since in pseudocode 
    # we pass value but not memory pointer (as Python/JavaScript do)
    for 1 <= i <= 4 do
        puzzle[i] <- row
    end for
    return puzzle
end function
```

## Task 2: PermuteVector

This function should take a four-element vector called row as an input parameter and return that vector but with
its values cyclically permuted by p elements to the left: p should be a number between 0 and 3 (inclusive). To be
able to get full marks you need to use the queue abstract data structure appropriately as outlined above.

```
function PermuteVector(row, p)
    if p = 0 then
        return row
    end if
    
    # Put values in permuted order to queue, as it is required in the task
    # We need to start iteration from one because it is pseudocode but not real code
    new Queue q
    for p + 1 <= i <= LENGTH[row] do
        ENQUEUE[row[i],q]
    end for
    for 0 + 1 <= i <= p do
        ENQUEUE[row[i],q]
    end for
    
    # Convert queue to vector and return vector, as it is required in the task
    new Vector result(4)
    for 1 <= i <= 4 do
        result[i] <- DEQUEUE[q]
    end for
    return result
end function
```

## Task 3: PermuteRows

This function should take a four-element vector called puzzle, which will be of the form of the output of MakeVector as an input parameter, as well as three integers x, y and z. The function will return puzzle but with elements
puzzle[1], puzzle[2] and puzzle[3] cyclically permuted by x, y and z elements respectively to the left: x, y and
z should all be numbers between 0 and 3 (inclusive). To be able to get full marks you should call the function
PermuteVector appropriately. HINT: You do not need to loop over integers x, y and z.

```
function PermuteRows(puzzle, x, y, z)
    puzzle[0] <- PermuteVector(puzzle[0], x)
    puzzle[1] <- PermuteVector(puzzle[1], y)
    puzzle[2] <- PermuteVector(puzzle[2], z)
    return puzzle
end function
```

P.S. I would use a loop if the arena size is big enough to avoid code repeating. But this one is small enough and the task says don't do it.

## Task 4: SearchStack

This function will take a stack and a value (called item) as input parameters, and return FALSE if item is not stored
in the stack, otherwise return the stack without the element storing item.

```
function SearchStack(stack, item)
    found_item <- FALSE
    new Stack result
    for 0 <= i < 4 do
        if EMPTY[stack] do
            return FALSE
        end if
        item_from_stack <- POP[stack]
        if item_from_stack = item do
            found_item <- TRUE
        else
            PUSH[item_from_stack,result]
        end if
    end for
    if found_item = TRUE do
        return result
    else
        return FALSE
    end if
end function
```

If pseudocode stack supports `select` operation and capability to remove item in the middle, it would significantly simplify the code. But it doesn't (based on the course books).

So, i need to read whole stack and copy it to a new one without the target element. We don't know the stack size here (`CheckColumn` and other functions put stack of size from 1 to 4), so need to check it is not empty everytime.

## Task 5: CheckColumn

This function will take the vector puzzle (as produced by MakeVector) as an input parameter and check that
column j contains all numbers from 1 to 4: if it does contain all numbers from 1 to 4, it should return TRUE,
otherwise it should return FALSE. The procedure you should use is the one outlined above. To get full marks you
need to call SearchStack(stack, item).

```
function CheckColumn(puzzle, j)
    new Stack stack
    for 1 <= i <= LENGTH[puzzle] do
        PUSH[puzzle[i][j],stack]
    end for
    for 1 <= k <= 4 do
        result <- SearchStack(stack, k)
        if result = FALSE do
            return FALSE
        end if
    end for
    if not EMPTY[stack] do
        return FALSE
    end if
    return TRUE
end function
```

The task requires to use a Stack, `SearchStack` expects a stack as input, so, first thing is transform the vector to a stack. After it, simply check `SearchStack` as the task requires.

At the end we check if stack is empty to check all items are used and just make code more defensive against possible bugs.

## Task 6: CheckGrids

This function will take the vector puzzle (as produced by MakeVector) as an input parameter and check that all
sub-grids contain all numbers from 1 to 4: if every sub-grid does contain all numbers from 1 to 4, it should return
TRUE, otherwise it should return FALSE. For each sub-grid you should create a stack with numbers from 1 to 4,
and then repeatedly search the stack to see if the values in the sub-grid are stored there. To get full marks you need
to call SearchStack(stack, item).

```
function CheckGrids(puzzle)
    for 0 <= i <= 1 do
        for 0 <= j <= 1 do
            # Create a stack with expected values
            new Stack stack
            for 1 <= n <= 4 do
                PUSH[n,stack]
            end for
            # Create a vector with current grid values
            new Vector grid(4)
            grid[0] <- puzzle[i * 2][j * 2]
            grid[1] <- puzzle[i * 2 + 1][j * 2]
            grid[2] <- puzzle[i * 2][j * 2 + 1]
            grid[3] <- puzzle[i * 2 + 1][j * 2 + 1]
            # Check all values in the grid
            for 1 <= n <= 4 do
                result <- SearchStack(stack, n)
                if result = FALSE do
                    return FALSE
                end if
            end for
        end for
    end for
    return TRUE    
end function
```

Here i used `i` and `j` loops to avoid code duplication for every grid. Probably, benefits not so big in the small arena, but for real sudoku it would mean to much lesser copied code.

## Task 7: Consider the following puzzle vector

>> Design and explain a concrete data structure that implements this puzzle vector. The data structure must only
consist of elements that can store an integer or a pointer to another element or null - elements can be indexed if they
are contiguous in memory as with an array. You can draw the data structure and explain how the allowed operations
of vectors are implemented on this concrete data structure - additional pointers can be created to traverse lists.
One approach could be to use arrays, or linked lists, or another approach completely.

In my implementation `puzzle` is a two-dimensional array of integers. It is very simple and provides fast constant access
to any element in the puzzle. In the more developed application I would use objects instead of integers, because
need to store some additional data for every cell (for example, if cell number is provided by computer or player put it)

It is possible to implement the same thing using a linked list but there is no reason to use it since
we know the final size of both dimensions and it is simpler to have fast select by index 
(array or dynamic array have constant complexity for `a[i]` operation but linked list requires iterate all
items between head/tail and target item)

## Task 8: MakeSolution

This function will take the four-element vector row as input, which is the same input for the function MakeVector. The function should return a solved Pseudoku puzzle such that all column and sub-grid Pseudoku conditions
are satisfied. The function will generate a vector using MakeVector(row), then try cyclic permutations on this
vector using PermuteRow(puzzle, x, y, z) until a set of permutations is found such that all Pseudoku conditions
are satisfied (checked using CheckGrids and ColCheck). To be able to get full marks you should call the
functions MakeVector, PermuteRow, CheckGrids and ColCheck.

```
function MakeSolution(row)
    base_puzzle <- MakeVector(row)
    for 0 <= x < 4 do
        for 0 <= y < 4 do
            for 0 <= z < 4 do
                puzzle <- PermuteRows(base_puzzle, x, y, z)
                result <- CheckGrids(puzzle)
                if result = TRUE do
                    result <- ColChecks(puzzle)
                    if result = TRUE do
                        return puzzle
                    end if
                end if
            end for
        end for
    end for
    return FALSE
end function
```

Just get all possible combinations of `x`, `y`, `z` and use `PermuteRows` with it. Use both checks `ColChecks` and `CheckGrids` to validate result.

## Task 9

>> Describe a method for setting values to be blank characters in the elements of the output of MakeSolution. 
You can describe the method in words, use pseudocode, or use a flowchart. The method should take
the number n as an input parameter and set n values to be blank characters. You do not need to go into great
detail as long as the method makes sense. Maximum word count for the whole task (excluding diagrams): 200 words.

As i explain in the Task 7, after some development i expect to change puzzle from two-dimensional array of integers to two-dimensional array of objects (or associative arrays).

Why just don't store unknown values as nulls / false?

Because we need to store some extra data, for example if the cell number is created by computer or added by player. It is good for user interface: usually, player can remove inserted values, but can't remove initial values.

## Task 10: Analysing the algorithm

>> In the next task, the goal is to analyse the algorithm in this assignment. The algorithm to generate Pseudoku
puzzles outlined here might not produce all possibly valid Pseudoku puzzles. Remember that, generally speaking,
the algorithm works by cyclically permuting several rows of a vector until the Pseudoku conditions are satisfied. In
the next task you should aim to identify all of the weaknesses you can think of in this algorithm.
Task 10: Describe and very briefly explain the limitations of the algorithm in this assignment. Maximum word
count for the whole task: 400 words (excluding figures).

#### 1. Missing cases

Current implementation doesn't change order in the input row. It can be fixed with a simple generator:

```
function MakeMoreSolutions(v)
    new Queue result
    
    if LENGTH[v] == 4:
        subresult <- MakeSolution(v)
        if subresult != FALSE do
            ENQUEUE[subresult,result]
        end if
        return result
    end if
    
    i <- LENGTH[v] + 1
    for 1 <= x <= 4 do
        v[i] <- x
        subresult <- MakeMoreSolutions(v)
        while not EMPTY[subresult] do:
            ENQUEUE[POP[subresult],result]
        end while
    end for
    return result

end function

new Vector input(4)
MakeMoreSolutions(input)
```

#### 2. Poor performance
This algorithm uses brute force approach that's always ineffective.

Additionally, current implementation requires N^2 iterations
in the worst case to find valid puzzle because it uses `PermuteRow` function N times for N rows.

```
function MakeSolution(row)
    base_puzzle <- MakeVector(row)
    for 1 <= x < 4 do
        for 1 <= y < 4 and y != x do
            for 1 <= z < 4 and z != x and z != y do
                puzzle <- PermuteRows(base_puzzle, x, y, z)
                result <- CheckGrids(puzzle)
                if result = TRUE do
                    result <- ColChecks(puzzle)
                    if result = TRUE do
                        return puzzle
                    end if
                end if
            end for
        end for
    end for
    return FALSE
end function
```

It makes no sense to try `PermuteRows` where any argument is zero - it will fail `CheckColumn` check because the last line always has zero permutation.
Also it makes no any sense to call `PermuteRows` where any pair of arguments are equal - the same reason.

## Overwork: Python implementation

Not the part of the task but can be considered as a proof of working algorithm.

```python

from typing import Any


def make_vector(row: list[int]) -> list[list[int]]:
    puzzle = list()
    for i in range(0, 4):
        puzzle.append(row.copy())
    return puzzle


def permute_vector(row: list[Any], p: int) -> list[Any]:
    assert 0 <= p < len(row)
    if p == 0:
        return row
    q = list()
    for i in range(p, len(row)):
        q.append(row[i])
    for i in range(0, p):
        q.append(row[i])
    return q


def permute_rows(puzzle: list[list[int]], x: int, y: int, z: int) -> list[list[int]]:
    for i in (x, y, z):
        assert 0 <= i <= 3
    new_puzzle = puzzle.copy()
    new_puzzle[0] = permute_vector(puzzle[0], x)
    new_puzzle[1] = permute_vector(puzzle[1], y)
    new_puzzle[2] = permute_vector(puzzle[2], z)
    return new_puzzle


def search_stack(stack, item) -> bool or list[int]:
    for i in range(0, len(stack)):
        if stack[i] == item:
            del stack[i]
            return stack
    return False


def check_column(puzzle: list[list[int]], j) -> bool:
    assert 1 <= j <= 4, j
    stack = list()
    for i in range(0, len(puzzle)):
        stack.append(puzzle[i][j - 1])
    for k in range(1, 5):
        result = search_stack(stack, k)
        if result is False:
            return False
    if len(stack) > 0:
        return False
    return True


def col_checks(puzzle: list[list[int]]) -> bool:
    for j in range(1, 5):
        if check_column(puzzle, j) is False:
            return False
    return True


def check_grids(puzzle: list[list[int]]) -> bool:
    for i in range(0, 2):
        for j in range(0, 2):
            stack = list()
            for n in range(1, 5):
                stack.append(n)
            for n in (
                    puzzle[i*2][j*2],
                    puzzle[i*2 + 1][j*2],
                    puzzle[i*2][j*2 + 1],
                    puzzle[i*2 + 1][j*2 + 1],
            ):
                result = search_stack(stack, n)
                if result is False:
                    return False
    return True


def make_solution(row: list[int]):
    base_puzzle = make_vector(row)
    for x in range(0, 4):
        for y in range(0, 4):
            for z in range(0, 4):
                print(f'Create puzzle {x=} {y=} {z=}')
                puzzle = permute_rows(base_puzzle, x, y, z)
                print(f'Created puzzle {x=} {y=} {z=}: {puzzle}')
                result = check_grids(puzzle)
                if result is False:
                    print(f'Bad puzzle (grid) {x=} {y=} {z=}: {puzzle}')
                    continue
                result = col_checks(puzzle)
                if result is False:
                    print(f'Bad puzzle (column) {x=} {y=} {z=}: {puzzle}')
                    continue
                print(f'Great puzzle {x=} {y=} {z=}: {puzzle}')
                return puzzle
    print('Good puzzle is not found')


assert check_column([[1,2,3,4], [2,3,4,1], [3,4,1,2], [4,1,2,3]], 1) is True
assert col_checks([[1,2,3,4], [2,3,4,1], [3,4,1,2], [4,1,2,3]]) is True
assert check_grids([[1,2,3,4], [3,4,1,2], [1,2,3,4], [3,4,1,2]]) is True
assert check_grids([[4,3,2,1], [2,1,3,4], [3,4,1,2], [1,2,4,3]]) is True


import itertools
for row in itertools.permutations([1, 2, 3, 4]):
    result = make_solution(list(row))
    if result:
        break
    print('Fail', row)
```

```
...
Create puzzle x=1 y=3 z=2
Created puzzle x=1 y=3 z=2: [[4, 1, 3, 2], [3, 2, 4, 1], [1, 3, 2, 4], [2, 4, 1, 3]]
Great puzzle x=1 y=3 z=2: [[4, 1, 3, 2], [3, 2, 4, 1], [1, 3, 2, 4], [2, 4, 1, 3]]
4 1 3 2
3 2 4 1
1 3 2 4
2 4 1 3
```
