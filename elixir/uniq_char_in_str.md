# Unique Characters in String

This article shows one way to write a function that returns a list of unique characters in a string

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

The first function `uniq_chars` is just the entry point, all the logic lies with `uniq_ch_helper`. There are 2 functions `uniq_ch_helper` to match:
* One is the final case when the index is the same as the length, in which case a list is returned
* The second is the normal case, where the current letter is put into the `mapset`, and the index `i` is incremented
