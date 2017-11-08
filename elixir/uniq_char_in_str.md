# Unique Characters in String

This article shows one way to write a function that returns an array of unique characters in a string

## MapSet solution

```elixir
defmodule UniqChars do
  def uniq_chars str do
    uniq_ch_helper(str, MapSet.new, 0, String.length(str))
  end

  defp uniq_ch_helper(_, mapset, i, length) when i == length do
    MapSet.to_list(mapset)
  end

  defp uniq_ch_helper(str, mapset, i, length) do
    letter = String.at(str, i)
    uniq_ch_helper(str, MapSet.put(mapset, letter), i + 1, length)
  end
end
```
