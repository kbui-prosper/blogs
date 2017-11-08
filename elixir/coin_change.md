# Coin Change

This article solves the infamous coin change problem ([wiki](https://en.wikipedia.org/wiki/Change-making_problem)) in Elixir!

## When list is expected output

Please note that in elixir, `[7]` is the same as `'\a'`, so using a list to represent numbers can feel a little weird. Anyway, here is the solution that outputs a list of integers as the minimum coinset:

```elixir
defmodule Coin do
  def min_coinset(target, coins) do
    cond do
      target in coins -> [target]

      true ->
        coinsets = coins
        |> Enum.filter(fn coin -> coin < target end)
        |> Enum.map(fn coin ->
          Enum.concat [coin], min_coinset(target - coin, coins)
        end)
        |> Enum.min_by(&length/1)
    end
  end
end
```

## How it works

```elixir
iex(2)> Coin.min_coinset 2, [1, 5, 7, 10]
[1, 1]
iex(3)> Coin.min_coinset 5, [1, 5, 7, 10]
[5]
iex(4)> Coin.min_coinset 11, [1, 5, 7, 10]
[1, 10]
iex(5)> Coin.min_coinset 12, [1, 5, 7, 10]
[5, 7]
```
