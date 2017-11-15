# Balanced Parentheses Check

This article writes a function checks if a string has balanced parentheses. Only 3 types of parentheses are considered for this example: round `()`, square `[]`, and curly `{}`.

## Solution

```elixir
defmodule Parentheses do
  def balance_check string do
    parentheses = %{
      ")" => "(",
      "]" => "[",
      "}" => "{"
    }

    closers = Map.keys(parentheses)
    openers = Map.values(parentheses)

    string
    |> String.graphemes
    |> Enum.filter(& &1 in closers or &1 in openers)
    |> Enum.reduce([], fn (x, acc) ->
      cond do
        acc == false -> false
        acc == [] -> [x]
        x in openers -> [x | acc]
        x in closers && parentheses[x] == List.first(acc) ->
          List.delete_at(acc, 0)
        true -> false
      end
    end)
    |> truthy_check()
  end

  defp truthy_check anything do
    !!anything
  end
end
```

#### How it works

[Home][home]

[home]: ../README.md
