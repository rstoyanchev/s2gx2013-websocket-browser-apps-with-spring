!SLIDE subsection
# User Information

!SLIDE smaller bullets incremental
# Authentication

* STOMP `CONNECT` frame has authentication headers
* However over WebSocket we can use HTTP
* Protect application as usual, e.g. Spring Security
* Messages enriched with "user" header

!SLIDE small
# Access to Current User


    @@@ java

    @Controller
    public class GreetingController {


      @MessageMapping("/greeting")
      @SendTo("/topic/greetings")
      public String greet(String greeting, Principal user) {
          return user + " says: " + greeting;
      }

    }

!SLIDE smaller bullets incremental
# How to Send Message to User?

* Report an error
* Send results asynchronously
* Many other use cases

!SLIDE smaller
# Handle Error and Send Message
    @@@ java

    @Controller
    public class GreetingController {


      @MessageMapping("/greeting")
      public void greet(String greeting) {
        throw new IllegalStateException("error");
      }

      @MessageExceptionHandler
      @SendToUser("/queue/errors")
      public String handleException(IllegalStateException ex) {
        return ex.getMessage();
      }

    }

!SLIDE smaller
# Send Results Asynchronously
    @@@ java

    @Service
    public class TradeService {

      @Autowired
      private SimpMessagingTemplate template;


      public void executeTrade(Trade trade) {

        // ...

        String user = ...
        String position = ...
        String destination = "/queue/position-updates";
        this.template.convertAndSendToUser(user, destination, position);
      }

    }

!SLIDE smaller bullets incremental
# How Does Send To User Work?

* Client must subscribe to unique queue
* Using unique suffix
* Provided in STOMP `CONNECTED` frame
* Along with current user name

!SLIDE smaller bullets incremental
# Subscribing To Unique Queue

    @@@ javascript

    var socket = new SockJS('/spring-websocket-portfolio/portfolio');
    var client = Stomp.over(socket);

    client.connect('guest', 'guest', function(frame) {

      var user = frame.headers['user-name'];
      var suffix = frame.headers['queue-suffix'];

      client.subscribe("/queue/position-updates" + suffix, function(msg) {
      });

      stompClient.subscribe("/queue/errors" + suffix, function(msg) {
      });

    }

!SLIDE smaller bullets incremental
# Resolving User Destinations

* When you use one of these:
* `@SendToUser("/queue/foo")`
* `template.convertAndSend(user,"/queue/foo",foo);`
* You get message with destination header:<br><br>`"/{user}/queue/foo"`





