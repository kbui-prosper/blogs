# Ecto Quick Guide for Going from 1.2x to 1.3x

## Removal of the `model`

Phoenix 1.3x no longer has the `model` folder. Additionally, your `web.ex` file is now renamed to `#{your_app_name}_web.ex` file, and the model section has been removed. Here is a boilerplate `web.ex` file from 1.2:

```elixir
defmodule Discuss.Web do
  # ...

  def model do
    quote do
      use Ecto.Schema

      import Ecto
      import Ecto.Changeset
      import Ecto.Query
    end
  end

  def controller do
    # ...
  end

  def view do
    # ...
  end

  def router do
    # ...
  end

  def channel do
    # ...
  end

  # ...
end
```
