# Coursework Assignment: Sudoku assignment

Algorithms and Data Structure I

## Task 1: MakeVector

![image](https://user-images.githubusercontent.com/6631578/211193838-c2961337-3f92-475d-9d90-ef9db7164262.png)


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

![image](https://user-images.githubusercontent.com/6631578/211193862-9c273130-27b6-4475-9931-76bfda867272.png)

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

![image](https://user-images.githubusercontent.com/6631578/211193874-c0221303-82a3-4835-a9d4-1a2b75a69996.png)


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

![image](https://user-images.githubusercontent.com/6631578/211193897-43c0df1e-0cc0-42e6-94e3-5cd0d9220dc4.png)


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

![image](https://user-images.githubusercontent.com/6631578/211193902-a60aa267-dbfa-4eae-9e86-b5a2aef654bd.png)

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

![image](https://user-images.githubusercontent.com/6631578/211193912-20e10fa0-d431-483f-8649-263f5bced499.png)

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

![image](https://user-images.githubusercontent.com/6631578/211193928-3e7ce466-95a9-4cfe-9850-6927fc62e1a3.png)

In my implementation `puzzle` is a two-dimensional array of integers. It is very simple and provides fast constant access
to any element in the puzzle. In the more developed application I would use objects instead of integers, because
need to store some additional data for every cell (for example, if cell number is provided by computer or player put it)

It is possible to implement the same thing using a linked list but there is no reason to use it since
we know the final size of both dimensions and it is simpler to have fast select by index 
(array or dynamic array have constant complexity for `a[i]` operation but linked list requires iterate all
items between head/tail and target item)

## Task 8: MakeSolution

![image](https://user-images.githubusercontent.com/6631578/211193953-0ac9a408-6d57-442c-95d4-ba514ec4ea61.png)

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

![image](https://user-images.githubusercontent.com/6631578/211193977-94c3a581-0300-4df1-90b2-b939de0a90f9.png)

As i explain in the Task 7, after some development i expect to change puzzle from two-dimensional array of integers to two-dimensional array of objects (or associative arrays).

Why just don't store unknown values as nulls / false?

Because we need to store some extra data, for example if the cell number is created by computer or added by player. It is good for user interface: usually, player can remove inserted values, but can't remove initial values.

## Task 10: Analysing the algorithm

![image](https://user-images.githubusercontent.com/6631578/211193988-13f8f140-f4c5-4f68-8974-cc292887b7ec.png)

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
