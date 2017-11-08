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
  def fib do

  end
```
