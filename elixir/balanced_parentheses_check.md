# Balanced Parentheses Check

This article writes a function checks if a string has balanced parentheses. Only 3 types of parentheses are considered for this example: round `()`, square `[]`, and curly `{}`.

## Solution
```elixir
defmodule Parentheses do
  def balance_check string do
    string
    |> String.graphemes
    |> Enum.filter(& &1 in ["(", "[", "{", "}", "]", ")"] )
    |> balance_check_step([])
  end

  defp balance_check_step [], [] do
    true
  end

  defp balance_check_step paren_list, [] do
    [current_paren | paren_list] = paren_list
    balance_check_step paren_list, [current_paren]
  end

  defp balance_check_step [], stack do
    false
  end

  defp balance_check_step paren_list, stack do
    parentheses = %{
      ")" => "(",
      "]" => "[",
      "}" => "{"
    }

    closers = Map.keys(parentheses)
    openers = Map.values(parentheses)
    [current_paren | paren_list] = paren_list
    [top_item | stack_remainder] = stack

    cond do
      current_paren in openers ->
        balance_check_step paren_list, [current_paren | stack]
      current_paren in closers && parentheses[current_paren] == top_item ->
        balance_check_step paren_list, stack_remainder
      true -> false
    end
  end
end
```

#### How it works
```elixir
iex(2)> Parentheses.balance_check "Khai"
true
iex(3)> Parentheses.balance_check "{Khai"
false
iex(4)> Parentheses.balance_check "{Khai}"
true
iex(5)> Parentheses.balance_check "{Khai]}"
false
iex(6)> Parentheses.balance_check "{Khai[]}"
true
iex(7)> Parentheses.balance_check "{Kha)i[]}"
false
iex(8)> Parentheses.balance_check "({Kha)i[]}"
false
iex(9)> Parentheses.balance_check "(Kha)i[]}"
false
iex(10)> Parentheses.balance_check "{(Kha)i[]}"
true
```

[Home][home]

[home]: ../README.md
