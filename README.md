# Welcome to Flow/Lead

## Sending Leads - Affiliates

sending leads as affiliates is possible in three ways:

1. Tracking system link to an offer - the simplest way
2. Integrate our JS SDK in your HTML page
3. Send us HTTP requests via our REST API interface

## Using our REST API

Lead information should be sent using HTTP POST to the following endpoint:

http://track-flow.io/ws/1/flow/track/a27bd7afbde4e77717a5156d69a14405

The payload should contain the following:
```
{
  "ws_ver": "1.0",
  "event_type_id": 1,
  "lead_aff_id": "{YOUR AFFILIATE ID}",
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

All fields inside "data" are required. "client_ip" is the IP of the user and "client_origin_domain" is the domain name the user came from.

### Sending Test Leads

All test leads should be sent with the postfix "@mailinator.com" so we can tell they're not real leads.
