# Using Action Cable with React + Redux set up

## The basics

Action Cable is the default solution for WebSockets in Rails 5. In order to get started, all you have to do is to create a file in `app/channels`:

```ruby
# app/channels/chat_room_channel.rb
class ChatRoomChannel < ApplicationCable::Channel
  def subscribed
    stream_from "chat_room"
  end
end
```

This method `subscribed` is invoked when a client subscribes to a channel. The string `"chat_room"` is a unique identifier, used to distinguish between different WebSocket channels. A client subscribes by invoking the following javascript code on the front end:

```javascript
window.App.cable.subscriptions.create({
  channel: 'ChatRoomChannel'
}
```

```ruby
# app/channels/chat_room_channel.rb
  def subscribed
    stream_from "chat_room_#{params[:chat_room_id]}"
  end
```
