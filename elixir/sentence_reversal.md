# Sentence Reversal

This solution reverses the order of the words in a sentence without altering the actual words in the sentence.

```elixir
defmodule Sentence do
  def reversal string do
    string
    |> String.split(" ")
    |> Enum.reverse
    |> Enum.join(" ")
  end
end
```

How it works

```elixir
iex(2)> Sentence.reversal "what a weird day"
"day weird a what"
iex(3)> Sentence.reversal "i like sunshine and apple pies"
"pies apple and sunshine like i"
```

[Home][home]

[home]: ../README.md
