# Coin Change

This article solves the infamous coin change problem ([wiki](https://en.wikipedia.org/wiki/Change-making_problem)) in Elixir!

## The solution

```elixir
defmodule Coin do
  def min_coinset(target, coins) do
    cond do
      target in coins -> [target]
      true ->
        coins
        |> Enum.filter(fn coin -> coin < target end)
        |> Enum.map(fn coin -> [coin] ++ min_coinset(target - coin, coins) end)
        |> Enum.min_by(fn coin -> &length/1 end)
    end
  end
end
```

## How it works
