# Fibonacci Sequence in Elixir

This article shows a few ways to write a function that returns the nth number of the fibonacci sequence.

## Basic recursion solution with exponential time complexity

```elixir
defmodule Fibonacci do
  def fib(n) when n < 2, do: n

  def fib(n) do
    fib(n - 1) + fib(n - 2)
  end
end
```

This first solution is pretty self explanatory. The first function captures the base cases, and the second function simply delivers the logic of a fibonacci sequence.

## Efficient recursion solution with linear time complexity

```elixir
defmodule Fibonacci do
  def fib(0), do: 0

  def fib(n), do: fib_helper(0, 1, 1, n)

  defp fib_helper(_prev, curr, curr_i, target_i) when curr_i == target_i do
    curr
  end

  defp fib_helper(prev, curr, curr_i, target_i) do
    fib_helper(curr, curr + prev, curr_i + 1, target_i)
  end
end
```

Don't let this solution intimidate you. There are 3 functions to be aware of:
1. `def fib(0), do: 0` catches the base case of 0
2. `def fib(n), do: fib_helper(0, 1, 1, n)` triggers the helper
3. The last function has four arguments. The first argument is the previous number in the fibonacci sequence. The second is the current number is the fibonacci sequence. The third is the current index, and the last is the target index. When the current index is the same as the target index, the current number in the fibonacci sequence is returned. Otherwise, it triggers `fib_helper` again but one step forward in the fibonacci sequence.

[Home][home]

[home]: ../README.md
