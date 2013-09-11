!SLIDE subsection
# User Information

!SLIDE smaller bullets incremental
# Authentication

* STOMP `CONNECT` frame has authentication headers
* Over WebSocket we can use HTTP
* Protect application as usual, e.g. Spring Security
* Messages will have "user" header added

!SLIDE smaller
# Access to User Information


    @@@ java

    @Controller
    public class GreetingController {


      @MessageMapping("/greeting")
      @SendTo("/topic/greetings")
      public String greet(String greeting, Principal user) {
          return "[" + user + "] says: " + greeting;
      }

    }

!SLIDE smaller bullets incremental
# Sending Messages to a User

* Report an error
* Send results asynchronously
* Many other use cases

!SLIDE smaller
# Report Error
## via `@SendToUser`

    @@@ java

    @Controller
    public class GreetingController {

      // ...

      @MessageExceptionHandler
      @SendToUser("/queue/errors")
      public String handleException(IllegalStateException ex) {
        return ex.getMessage();
      }

    }

!SLIDE smaller
# Send Results Asynchronously
## via 'convertAndSendToUser`
    @@@ java
    @Service
    public class TradeService {

      @Autowired
      private SimpMessagingTemplate template;

      public void executeTrade(Trade trade) {

        String user = trade.getUser();
        String dest = "/queue/position-updates";
        TradeResult result = ...

        this.template.convertAndSendToUser(user, dest, result);
      }

    }

!SLIDE smaller bullets incremental
# How "SendToUser" Works
## _(client side)_
<br><br>
* Client subscribes to queue with unique name suffix
* STOMP `CONNECTED` frame provides the suffix
* along with current user name

!SLIDE smaller bullets incremental
# Subscribe using Queue Suffix

    @@@ javascript

    var socket = new SockJS('/spring-websocket-portfolio/portfolio');
    var client = Stomp.over(socket);

    client.connect('', '', function(frame) {

      var user = frame.headers['user-name'];
      var suffix = frame.headers['queue-suffix'];

      client.subscribe("/queue/trade-result" + suffix, function(msg) {
        // ...
      });
      client.subscribe("/queue/errors" + suffix, function(msg) {
        // ...
      });

    }

!SLIDE smaller bullets incremental
# How "SendToUser" Works
## _(server side)_
<br><br>
* When you use `@SendToUser("/queue/a")`
* Resulting destination is `"/user/{user}/queue/a"`
* This is resolved to `"/queue/a/"` + `unique queue-suffix`
* and then sent to broker channel


!SLIDE small center
# Message Flow
<br>
![Diagram](architecture-full.png)

!SLIDE smaller bullets incremental
# Managing Inactive Queues
## _(full-featured brokers)_
<br><br>
* Check broker documentation
* For example RabbitMQ creates auto-delete queue<br>with destinations like `"/exchange/amq.direct/a"`
* ActiveMQ has [config options](http://activemq.apache.org/delete-inactive-destinations.html) to purge inactive destinations


