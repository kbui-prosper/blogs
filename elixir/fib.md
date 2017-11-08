# Fibonacci Sequence in Elixir

This article shows a few ways to write a function that returns the nth number of the fibonacci sequence.

## Basic solution with exponential time complexity

```elixir
defmodule Fibonacci do
  def fib_rec(n) when n < 2, do: n

  def fib_rec n do
    fib_rec(n - 1) + fib_rec(n - 2)
  end
end
```

This first solution is pretty self explanatory. The first function captures the base cases, and the second function simply delivers the logic of a fibonacci sequence.

## Dynamic programming solution with linear time complexity and linear space complexity
