# Welcome to Flow/Lead

## Sending Leads

sending leads is possible in three ways:

1. Tracking system link to our offer - the simplest way
2. Integrate our JS SDK in your HTML page
3. Send us HTTP requests via our REST API interface

## 1. Using a tracking system link (in development)

## 2. Using our JS SDK (in development)

## 3. Using our REST API

Lead information should be sent using HTTP POST to the following endpoint:
```
http://track-flow.io/ws/1/flow/track/a27bd7afbde4e77717a5156d69a14405
```

The payload should contain the following:
```
{
  "ws_ver": "1.0",
  "event_type_id": 1,
  "lead_aff_id": "{AFFILIATE ID}",
  "data": "eyJlbWFpbCI6InRlc3QxQG1haWxpbmF0b3IuY29tIiwiZmlyc3RuYW1lIjoiSm9obiIsImxhc3RuYW1lIjoiU21pdGgiLCAicGhvbmUxIjogIisxMjM0NTY3OCIsICJwYXNzd29yZCI6ICJQYXNkMTMzWCIsICJjbGllbnRfaXAiOiAiMTk1LjI1NC4yMTkuNjAiLCAiY2xpZW50X29yaWdpbl9kb21haW4iOiAibXlmdW5uZWxkb21haW4uY29tIn0="
}
```

The "data" parameter is a JSON string encoded in Base64.  
The following code in PHP demonstrates how to encode an array with lead information:
```
$data = base64_encode(json_encode(array(
	"email" => "test1@mailinator.com",
	"firstname" => "John",
	"lastname" => "Smith",
	"phone1" => "+12345678",
	"password" => "Pasd133X",
	"client_ip" => "195.254.219.60",
	"client_origin_domain" => "myfunneldomain.com"
), JSON_UNESCAPED_UNICODE));
```

All fields in the $data array are required. "client_ip" is the IP of the user and "client_origin_domain" is the domain name the user came from.

### Sending Test Leads

All test lead emails should be sent with the postfix "@mailinator.com" so we can tell they're not real leads.

### Full Example

```
<?php

	// your affiliate id
        $lead_aff_id = "aa1";

	// lead information
        $data = array(
	    "email" => "test1@mailinator.com",
	    "firstname" => "John",
	    "lastname" => "Smith",
	    "phone1" => "+12345678",
	    "password" => "Pasd133X",
	    "client_ip" => "195.254.219.60",
	    "client_origin_domain" => "myfunneldomain.com"
        );



        ////////////////////////////

        $o = array(
            "ws_ver" => "1.0",
            "event_type_id" => 1,
            "lead_aff_id" => $lead_aff_id,
            "data" => base64_encode(json_encode($data, JSON_UNESCAPED_UNICODE))
        );

        $postdata = http_build_query($o);
    
        $opts = array("http" => 
            array (
                "method" => "POST",
                "header" => "Content-Type: application/x-www-form-urlencoded",
                "content" => $postdata
            )
        );
    
        $context  = stream_context_create($opts);
    
        $url = "http://track-flow.io/ws/1/flow/track/a27bd7afbde4e77717a5156d69a14405";
        $result = file_get_contents($url, false, $context);        

        echo $result;
	
```

### Pulling information with REST API

In order to pull information we must whitelist your server IP and give you an affiliate token. An affiliate token is required only when pulling information but not when pushing leads.

**Aggregated data endpoint:**
```
http://track-flow.io/ws/1/affiliate/aggregated/d1b06d78c0e5abe74e81f635b0c0ceff/{AFFILIATE ID}/{AFFILIATE TOKEN}/{START DATE in UTC}/{END DATE in UTC}
```
For example:
```
http://track-flow.io/ws/1/affiliate/aggregated/d1b06d78c0e5abe74e81f635b0c0ceff/aa1/token123/2019-01-01/2019-01-02
```

Aggregated data output is in this format: [["{DATETIME in UTC}","{COUNTRY CODE}","{LEAD PARAMS ID}","{COUNT LEADS}","{COUNT FTDs}"], ... ]
For example: [["2019-04-11 00:00:00","NO","4696eda8653935b8928528397e61fe54","1","0"], ... ]

**Deposits endpoint:**
```
http://track-flow.io/ws/1/affiliate/deposits/d1b06d78c0e5abe74e81f635b0c0ceff/{AFFILIATE ID}/{AFFILIATE TOKEN}/{DATE in UTC}
```
For example:
```
http://track-flow.io/ws/1/affiliate/deposits/d1b06d78c0e5abe74e81f635b0c0ceff/aa1/token123/2019-01-01
```

Deposits data output is int this format: [["{DATETIME in UTC}","{COUNTRY CODE}","{EMAIL}","{LEAD PARAMS ID}","{DEPOSIT DETAILS}"]]
For Example: [["2019-04-11 06:45:02","NO","test1@mailinator.com","99914b932bd37a50b983c5e7c90ae93b","{}"], ...]

**Specific lead information endpoint (by email):**
```
http://track-flow.io/ws/1/affiliate/email/d1b06d78c0e5abe74e81f635b0c0ceff/{AFFILIATE ID}/{AFFILIATE TOKEN}/{EMAIL}
```
For example:
```
http://track-flow.io/ws/1/affiliate/email/d1b06d78c0e5abe74e81f635b0c0ceff/aa1/token123/test1@mailinator.com
```
This endpoint returns JSON with details about the LEAD and DEPOSIT events. It can also be used to check whether this lead was sent in the past.
