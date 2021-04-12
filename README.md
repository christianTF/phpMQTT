**2020 Update**
I am restarting the project so watch this space


Blue Rhinos Consulting
Andrew Milsted | andrew@bluerhinos.co.uk | http://www.bluerhinos.co.uk | @bluerhinos

A simple php class to connect/publish/subscribe to an MQTT broker

Documentation: Coming Soon
Source: http://github.com/bluerhinos/phpMQTT

To install via Composer
-----------------------
`composer require bluerhinos/phpmqtt=@dev`

Usage
-----
## Init and connect
```php
require_once "phpMQTT.php";
$client_id = uniqid(gethostname()."_someIdentier");
$brokerhost = "localhost";
$brokerport = 1883;
$user = "mqttuser";
$pass = "mqttpass";
$clean_session = true;

$lwt = array(
  "topic" => "mytopic/connected", 
  "content" => "false", 
  "qos" => 0, 
  "retain" => true 
];

$mqtt = new Bluerhinos\phpMQTT($brokerhost,  $brokerport, $client_id);
$mqtt_connected = $mqtt->connect(
  $clean_session, 
  $lwt, 
  $user,
  $pass
);

if( !$mqtt_connected ) {
  echo "Not connected\n";
  exit(1);
}

// Do subscriptions
$topics["subscribe1/#"] = array('qos' => 0, 'function' => 'callback_subscribe1');
$topics["subscribe2/#"] = array('qos' => 0, 'function' => 'callback_subscribe2');

// Publish to the LWT topic, that we are online
$mqtt->publish( 
  $lwt['topic'],  // Topic
  "true",         // Value to say we are online
  0,              // QoS
  true            // Send retained
);

while( 1 ) {
  if( $mqtt_connected ) {
    // proc will check for new messages.
    // If proc is called if connect has failed, it will fall into a retry loop (10 seconds) and won't come back
    // Therefore, if you want to be non-blocked, only call proc with the "if connected" part
    $mqtt->proc();
  }
  else {
    // Do error handling
    // You might want to put the connecting process into a sub and retry to connect yourself every x seconds
}

$mqtt->close();

function callback_subscribe1( $topic, $message ) {
  echo "callback_subscribe1 Received $topic --> $message\n";
  // Do your stuff with that topic and message
  return;
}

function callback_subscribe2( $topic, $message ) {
  echo "callback_subscribe2 Received $topic --> $message\n";
  // Do your stuff with that topic and message
  return;
}
```
