!SLIDE subsection
# msgs.js


!SLIDE bullets incremental
# msgs.js

* Enterprise Integration Patterns in JavaScript
* provides core messaging patterns, and adapters
* deeply inspired by Spring Integration
* MIT licensed, part of cujoJS
* [http://github.com/cujojs/msgs](http://github.com/cujojs/msgs)


!SLIDE small
# Hello msgs.js

    @@@ javascript
    var bus = require('msgs').bus();
    
    bus.channel('lowercase');
    bus.channel('uppercase');
    
    bus.transform(
        function (message) { return message.toUpperCase(); },
        { input: 'lowercase', output: 'uppercase' }
    );
    bus.outboundAdapter(
        function (str) { console.log(str); },
        { input: 'uppercase' }
    );
    
    bus.send('lowercase', 'hello world');
    // 'HELLO WORLD'


!SLIDE
# WebSocket support

    @@@ javascript
    var bus = require('msgs').bus(),
        SockJS = require('sockjs');
    
      bus.channel('toServer');
    bus.channel('fromServer');
    
      bus.webSocketGateway(
        new SockJS('/msgs'),
        { output: 'fromServer', input: 'toServer' } 
    );


!SLIDE small
# STOMP support

    @@@ javascript
    var bus = require('msgs').bus(),
        SockJS = require('sockjs');
    
    bus.stompWebSocketBridge('broker', new SockJS('/broker'));
    
    // subscribe
    bus.on('broker!/topic/price.stock.*', function(quote) {
        portfolio.processQuote(quote);
    });
    
    // publish
    var execTrade = bus.inboundAdapter('broker!/app/trade');
    execTrade(trade);

