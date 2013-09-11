!SLIDE subsection
# Full-featured<br> Message Broker

!SLIDE smaller bullets incremental
# Reasons for Full-featured Broker

* Simple broker supports subset of STOMP<br>(`SUBSCRIBE`, `UNSUBSCRIBE`, `MESSAGE`)
* No acks, receipts, transactions
* Simple message sending loop
* Not suitable for clustering

!SLIDE smaller center
# Message Flow w/ Broker Relay
<br>
![Diagram](architecture-relay.png)

!SLIDE smaller
# Enable Broker Relay

    @@@ java

    @Configuration
    @EnableWebSocketMessageBroker
    public class MyConfig implements WebSocketMessageBrokerConfigurer {


      @Override
      public void configureMessageBroker(MessageBrokerConfigurer conf) {
        conf.enableStompBrokerRelay("/queue/", "/topic/");
        conf.setApplicationPrefixes("/app");
      }

      // ...

    }

!SLIDE smaller bullets incremental
# Using Full-featured Broker

* Check broker STOMP documentation page
* Understand destination semantics
* Any additional features
* Application is now backed by message broker


