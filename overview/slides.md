!SLIDE subsection

# Building WebSocket Browser Applications with Spring
<br><br>
## [Rossen Stoyanchev](https://twitter.com/rstoya05)
## [Scott Andrews](https://twitter.com/scothis)

!SLIDE
# This presentation on Github
## [https://github.com/rstoyanchev](https://github.com/rstoyanchev)
## [s2gx2013-websocket-browser-apps-with-spring](https://github.com/rstoyanchev/s2gx2013-websocket-browser-apps-with-spring)

!SLIDE center small
# What are we going to discuss?

!SLIDE center small
# WebSocket Web Apps with Spring

!SLIDE small bullets incremental
# The Role of WebSocket

* An exciting new capability
* Especially on the heels of Ajax/Comet history
* Once you get your feet wet though
* You realize how much remains to be worked out

!SLIDE smaller
# WebSocket "Hello World!"

    @@@ java


    public class MyHandler extends TextWebSocketHandlerAdapter {

      @Override
      public void handleTextMessage(WebSocketSession sess, TextMessage msg) {
        sess.sendMessage(new TestMessage("Hello World!"));
      }

    }

!SLIDE small center
# Beyond "Hello World!"

!SLIDE small bullets incremental
# What's In a Message?

* A message is a blank page
* We can write anything
* That's good, although-
* ...you'll end up with custom format

!SLIDE smaller
# Something like this...

    @@@ java

    public void handleMessage(String text) {

      String[] strings = text.split("::::");
      clientId = strings[1];

      if (text.startsWith("add-client::::")) {
          type = MessageType.ADD_CLIENT;
      }
      else if (text.startsWith("remove-client::::")) {
          // ...
      }
      else if (text.startsWith("send-message::::")) {
          // ...
          message = strings[2];
      }
      else {
          throw IllegalStateException("Unknown type");
      }
    }

!SLIDE smaller
# Or this...

    @@@ java

    public void handleMessage(String text) {

        String[] tkns = text.split(":");
        AuctionMessage msg = new AuctionMessage(tkns[0], tkns[1], tkns[2]);

        String type = msg.getType();

        if (type.equals(AuctionMessage.LOGOUT_REQUEST)) {
          // ...
        } 
        else if (type.equals(AuctionMessage.AUCTION_LIST_REQUEST)) {
          // ...
        } 
        else if (type.equals(AuctionMessage.LOGIN_REQUEST)) {
          // ...
        } 
        else if (type.equals(AuctionMessage.BID_REQUEST)) {
          // ...
        }
    }

!SLIDE small bullets incremental
# How to Handle a Message?

* What's the equivalent of `@RequestMapping`?
* Hard to provide **useful** annotations
* Not without assuming what's in the message
* Compare to HTTP where we have-
* ... URL, HTTP method, headers ...

!SLIDE small bullets incremental
# No easy way to avoid

* One class to handle all messages
* One long if-else condition
* Compare to Spring MVC web app
* Many annotated controllers

!SLIDE small bullets incremental
# How to Broadcast To
# Subset of Users?

* Pass WebSocket session references around
* Keep track of who's interested in what
* A simple loop only scales so far

!SLIDE smaller
# Something like this...

    @@@ java

    public class Auction {

      private List<WebSocketSession> sessions = new ArrayList<>();


      synchronized void addArc(Session sess) {
          this.sessions.add(sess);
      }

      public synchronized void removeArc(Session sess) {
          this.sessions.remove(sess);
      }

      private void sendPriceUpdate() {
        for (WebSocketSession sess : getSessions()) {
          sess.sendMessage(new TextMessage(".."));
        }
      }
    }

!SLIDE small bullets incremental
# More Questions

* Did the message get there?
* _WebSocket protocol provides no guarantees_
* How to avoid blocking?
* _Need event-driven, reactive model_

!SLIDE small bullets incremental
# Messaging architecture

* WebSocket implies a messaging architecture
* Very different from HTTP/REST
* Closer to traditional messaging (AMQP, JMS)
* But still a web application

!SLIDE smaller bullets incremental
# Message Brokers

* We could use a message broker
* RabbitMQ, ActiveMQ have WebSocket support
* Messaging is hard
* Message brokers excel at what they do
* But quite a switch for web devs

!SLIDE center
## Is it Web App or Messaging App?
<br>
![Young/Old Face Drawing](young-old.jpg)

!SLIDE smaller bullets incremental
# The Best of Both Worlds

* An event-driven, messaging architecture
* Incorporate message broker
* Yet make it easy to do application things
* Web app not quite like traditional messaging app




