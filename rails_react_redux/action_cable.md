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

This method `subscribed` is invoked when a client subscribes to a WebSocket channel. The string `"chat_room"` is a unique identifier, used to distinguish between different WebSocket channels. A client subscribes by invoking the following javascript code on the front end:

```javascript
// Some where in your front end javascript
window.App.cable.subscriptions.create({
  channel: 'ChatRoomChannel'
});
```

When this code is ran, the backend will invoke `subscribed` and the client will continuously listen in on the `"chat_room"` channel from `ChatRoomChannel`.

But what if we want different chatrooms? What if we want over 9000 chatrooms? In the backend, we can modify our code to take in `params` like so:

```ruby
# app/channels/chat_room_channel.rb
  def subscribed
    stream_from "chat_room_#{params[:chat_room_id]}"
  end
```

And in the frontend, we can modify our code like so:

```javascript
// Some where in your front end javascript
window.App.cable.subscriptions.create({
  channel: 'ChatRoomChannel',
  chat_room_id: 9000 // This sends over `9000` as `params[:chat_room_id]``
});
```

Of course you would want to replace `9000` with a variable. Or even, better, let's throw this entire thing inside a method so we can reuse:

```javascript
// Some where in your front end javascript
const subscribeToChatRoom = (chatRoomId) => (
  window.App.cable.subscriptions.create({
    channel: 'ChatRoomChannel',
    chat_room_id: chatRoomId
  })
)
```

Now we can reuse the `subscribeToChatRoom` as many times as we want (say, over 9000 times?), each time we only need to pass in the `chatRoomId`.

Okay so now we are subscribed to the ChatRoom, can we make it do stuff? Yes we can. Just rewrite your front end like so:

```javascript
// Some where in your front end javascript
const subscribeToChatRoom = (chatRoomId) => (
  window.App.cable.subscriptions.create({
    channel: 'ChatRoomChannel',
    chat_room_id: chatRoomId
  },
  {
    connected: () => {
      // do something here
    },
    disconnected: () => {
      // do something here
    },
    received: (data) => {
      // do something here
    }
  })
)
```

`connected` is invoked when a connection is established. `disconnected` is invoked when a connection is disconnected. `received` is invoked when data is received. You can manipulate the data received (normally JSON) however you want (log it, dispatch it, go crazy).

Here is the last piece of a puzzle: how does the server send information over? In your backend, simply invoke this code to send data over:

```ruby
  ActionCable.server.broadcast(
    "chat_room_9000",
    "Data goes here"
  )
```

`broadcast` method is invoked with 2 arguments, the first one is the channel identifier (`"chat_room_9000"`), the second one is data (`"Data goes here"`). In real examples, you would want to replace `"Data goes here"` with real data in JSON format. You can run this code anywhere you want, in your `controller`, in your `channel`, even in your `model`.

And that's just the overview. I'm kinda tight on time right now, so here's a link for further reading: [Real-Time Rails: Implementing WebSockets in Rails 5 with Action Cable](https://blog.heroku.com/real_time_rails_implementing_websockets_in_rails_5_with_action_cable)
