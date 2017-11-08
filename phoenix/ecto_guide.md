# Ecto Quick Guide for Going from 1.2x to 1.3x

## Removal of the `model`

Phoenix 1.3x no longer has the `model` folder. Additionally, your `web.ex` file is now renamed to `#{your_app_name}_web.ex` file, and the model section has been removed. Here is a boilerplate `web.ex` file from 1.2:

```elixir
defmodule Discuss.Web do
  # ...

  def model do # this function no longer exists in 1.3
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

The `model` function is completely removed in Phoenix 1.3 and above. However, Phoenix still ships with Ecto. So what this means is that we have to use Ecto manually in our business logic. Here are the 4 key Ecto items:

* `import Ecto` - Not sure what this does.
* `use Ecto.Schema` - runs the code inside `__using__` function from `Ecto.Schema` module. Add this line where you're writing your schema.
* `import Ecto.Changeset` - Add this line where you're writing your changeset function.
* `import Ecto.Query` - Add this line where you're writing your database queries.

That's just the overview for now.

[Home][home]

[home]: ../README.md
