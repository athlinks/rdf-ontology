# rdf-ontology

#### This is currently in development, expect changes

# [Athlinks RDF API v0.1 (beta)](#athlinks-rdf-api-v0.1-(beta))

RDF is a standard model for data interchange on the Web. RDF has features that facilitate data merging even if the underlying schemas differ, and it specifically supports the evolution of schemas over time without requiring all the data consumers to be changed. 
Our ontology is based off of schema.org and Open Graph(ogp.me). Search engines use schema.org heavly, and sites like Facebook and Pinterest use Open Graph. 
It is our belief that this improves the portability of data for us our partners. 

[Athlinks RDF API v0.1 (beta)](#athlinks-rdf-api-v0.1-(beta))
    [More information about RDF](#more-information-about-rdf)
    [The Athlinks Ontology](#the-athlinks-ontology)
	[The Athlinks RDF Endpoint](#the-athlinks-rdf-endpoint)
		[Turtle Formats currently supported](#turtle-formats-currently-supported)
	[Authentication](#authentication)

## [More information about RDF](#more-information-about-rdf)

https://en.wikipedia.org/wiki/Resource_Description_Framework - High level description of RDF and its formats
http://blog.swirrl.com/articles/introduction-to-rdf/ - Concise description of RDF
http://docs.rdf4j.org/rdf-tutorial/ - Getting started with RDF
http://schema.org - An example ontology
http://openid.net/connect/ - Overview of authentication methodology

## [The Athlinks Ontology](#the-athlinks-ontology)
https://github.com/athlinks/rdf-ontology
This describes the types and predicates that Athlinks expects.

## [The Athlinks RDF Endpoint](#the-athlinks-rdf-endpoint)
You can post RDF data in a varity of Turtle based formats to: 
http://api.athlinks.com/rdf

#### [Turtle Formats currently supported](#turtle-formats-currently-supported)

 - text/turtle (https://www.w3.org/TR/turtle/)
 - application/n-triples (https://www.w3.org/TR/n-triples/)
 - application/trig (https://www.w3.org/TR/trig/)

Primer: https://www.w3.org/2007/02/turtle/primer/

We also plan on supporting formats such as RDF/XML (https://www.w3.org/TR/rdf-syntax-grammar/) and JSON-LD (https://www.w3.org/TR/json-ld/). 
 
Example data upload
```bash
#!/bin/bash
ACCESSTOKEN=..YOUR_ACCESS_TOKEN.. #See Authentication section below.
curl -s 'http://host:port/rdf' \
	--data-binary @file.ttl \
	-H "Content-Type: application/x-turtle" \
 	-H "Accept: application/json" \
 	-H "Authorization: Bearer $ACCESSTOKEN"

#### Response
# {
#   "ok": true,
#   "status": 200,
#   "triples": NumProcessed
# }
####
```

## [Authentication](#authentication)
Its likely you will want to use the Client Credentials Grant (https://tools.ietf.org/html/rfc6749#section-4.4) to get your Access Token if your using this from a backed service.

Example Client Credentials Flow to get an AccessToken.
```bash
#!/bin/bash
CLIENTID=..YOUR_CLIENT_ID..
CLIENTSECRET=..YOUR_CLIENT_SECRET..
TOKENRESPONSE=$(curl -s \
     -X POST 'https://accounts-dev.athlinks.com/auth/realms/athlinks/protocol/openid-connect/token' \
	 -H 'Content-Type: application/x-www-form-urlencoded' \
	 -d "client_id=$CLIENTID" \
	 -d "client_secret=$CLIENTSECRET" \
	 -d 'grant_type=client_credentials' \
	 -d 'scope=api-write')
# Use this access token in your Authorization header
ACCESSTOKEN=$(echo $TOKENRESPONSE | jq -r '.access_token')	 
ACCESSTOKENEXPIRESIN=$(echo $TOKENRESPONSE | jq -r '.expires_in')
# You will need to use the refresh token to request new access tokens according to the token expiration
REFRESHTOKEN=$(echo $TOKENRESPONSE | jq -r '.refresh_token')
REFRESHTOKENEXPIRESIN=$(echo $TOKENRESPONSE | jq -r '.refresh_expires_in')
echo TOKENRESPONSE was:
echo $TOKENRESPONSE | jq -r '.'	 
```
## [To Turtle](#example-of-event-data-in-turtle)
Here is an example event from Run SignUp converted to using the [Athlinks Ontology](https://github.com/athlinks/rdf-ontology/blob/master/athlinks-schema.ttl) and represented in Turtle.

JSON representation of a RunSignUp Event
```json
"race": {
                "race_id": 1649,
                "name": "10k Turkey Trek & 2k Turkey Toddle",
                "last_date": "11/19/2016",
                "last_end_date": "11/19/2016",
                "next_date": "11/18/2017",
                "next_end_date": "11/18/2017",
                "is_draft_race": "F",
                "is_registration_open": "T",
                "created": "5/21/2012 21:13",
                "last_modified": "1/9/2017 17:10",
                "description": "This Arvada event supports ...",
                "url": "https://runsignup.com/Race/CO/Arvada/SixPack10kand5k",
                "external_race_url": "http://10kturkeytrek.com/index.html",
                "external_results_url": null,
                "fb_page_id": null,
                "fb_event_id": null,
                "address": {
                    "street": "Ralston Creek Trail @ Long Lake Ranch Park",
                    "city": "Arvada",
                    "state": "CO",
                    "zipcode": "80007",
                    "country_code": "US"
                },
                "timezone": "America/Denver",
                "logo_url": "https://d368g9lw5ileu7.cloudfront.net/races/race1649-logo.bym87Y.png",
                "real_time_notifications_enabled": "T",
                "waiver": "I know that running a road race is ....",
                "events": [
                    {
                        "event_id": 139165,
                        "race_event_days_id": 58262,
                        "name": "10k Turkey Trek",
                        "details": null,
                        "start_time": "11/18/2017 09:00",
                        "end_time": null,
                        "registration_opens": "12/1/2016 02:00",
                        "event_type": "running_race",
                        "distance": "10K",
                        "volunteer": "F",
                        "require_dob": "T",
                        "require_phone": "T",
                        "registration_periods": [
                            {
                                "registration_opens": "12/1/2016 02:00",
                                "registration_closes": "6/1/2017 01:59",
                                "race_fee": "$35.00",
                                "processing_fee": "$3.80"
                            },
                            {
                                "registration_opens": "6/1/2017 02:00",
                                "registration_closes": "11/1/2017 01:59",
                                "race_fee": "$40.00",
                                "processing_fee": "$3.80"
                            },
                            {
                                "registration_opens": "11/1/2017 02:00",
                                "registration_closes": "11/17/2017 18:00",
                                "race_fee": "$45.00",
                                "processing_fee": "$3.80"
                            }
                        ]
                    },
                    {
                        "event_id": 139166,
                        "race_event_days_id": 58262,
                        "name": "2k Turkey Toddle (un-timed)",
                        "details": null,
                        "start_time": "11/18/2017 09:10",
                        "end_time": null,
                        "registration_opens": "12/1/2016 02:00",
                        "event_type": "running_race",
                        "distance": "2K",
                        "volunteer": "F",
                        "require_dob": "T",
                        "require_phone": "T",
                        "registration_periods": [
                            {
                                "registration_opens": "12/1/2016 02:00",
                                "registration_closes": "6/1/2017 01:59",
                                "race_fee": "$20.00",
                                "processing_fee": "$3.05"
                            },
                            {
                                "registration_opens": "6/1/2017 02:00",
                                "registration_closes": "11/1/2017 01:59",
                                "race_fee": "$25.00",
                                "processing_fee": "$3.05"
                            },
                            {
                                "registration_opens": "11/1/2017 02:00",
                                "registration_closes": "11/17/2017 18:00",
                                "race_fee": "$25.00",
                                "processing_fee": "$3.05"
                            }
                        ],
                        "giveaway": "Children's t-shirt option"
                    }
                ],
                "headings": [
                    {
                        "heading_text": "Driving Directions to the Park",
                        "content": "17850 W 64th Avenue, Arvada, Colorado is the approximate address..."
                    },
                    {
                        "heading_text": "More Information",
                        "content": "<p><a href=\"http://3wraces.com/10k-turkey-trek.html\" target=\"_blank\">10kTurkeyTrek.com</a></p>"
                    }
                ]
            }
```

Converts to:

```ttl
@prefix athlinks: <https://athlinks.com/2017/01/rdf-schema#>
@prefix rsu: <https://runsignup.com/API/>
<rsu:race/1649> a <athlinks:AthleticSeries> ;
	<schema:name> "10k Turkey Trek & 2k Turkey Toddle" ;
	<schema:description> "This Arvada event supports ..." ;
	<schema:url> "https://runsignup.com/Race/CO/Arvada/SixPack10kand5k" ;
	<schema:url> "http://10kturkeytrek.com/index.html" ;
	<schema:location> [
		a <schema:PostalAddress> ;
		<schema:streetAddress> "Ralston Creek Trail @ Long Lake Ranch Park" ;
		<schema:addressCountry> "US" ;
		<schema:addressLocality> "Arvada" ;
		<schema:addressRegion> "CO" ;
		<schema:postalCode> "80007" .
	] ;
	<schema:logo> "https://d368g9lw5ileu7.cloudfront.net/races/race1649-logo.bym87Y.png" ;
	<schema:subEvent> <rsu:raceEventDays/58262> .

<rsu:raceEventDays/58262> a <athlinks:AthleticEvent> ;	
		<schema:subEvent> <rsu:event/139165> ;
		<schema:subEvent> <rsu:event/139166> .

<rsu:#ProcessingFee1> a <schema:PaymentChargeSpecification> ;
			<schema:price> "3.80" ;
			<schema:priceCurrency> "USD" .

<rsu:#ProcessingFee2> a <schema:PaymentChargeSpecification> ;
			<schema:price> "3.05" ;
			<schema:priceCurrency> "USD" .
	 
<rsu:event/139165> a <athlinks:AthleticRace>;
	<schema:name> "10k Turkey Trek" ;
	<athlinks:raceType> "running race" ;
	<athlinks:distance> [
		a <schema:QuantitativeValue> ;
		<schema:value> "10" ;
		<schema:unitCode> "KEL" .
	] ;
	<schema:startDate> "2017-11-18T09:00:00-07:00"^^xsd:DateTime ;
	<schema:offers> [
		a <schema:Offer> ;
		<schema:price> "35.00" ;
		<schema:priceCurrency> "USD" ;
		<schema:priceValidFrom> "2016-12-1T02:00:00-07:00"^^:xsd:DateTime ;
		<schema:priceValidUntil> "2017-6-1T01:59:59-07:00"^^xsd:DateTime ;
		<schema:priceSpecification> <rsu:ProcessingFee1>
	] ;
	<schema:offers> [
		a <schema:Offer> ;
		<schema:price> "40.00" ;
		<schema:priceCurrency> "USD" ;
		<schema:priceValidFrom> "2017-6-1T02:00:00-07:00"^^:xsd:DateTime ;
		<schema:priceValidUntil> "2017-11-1T01:59:59-07:00"^^xsd:DateTime ;
		<schema:priceSpecification> <rsu:ProcessingFee1>
	] ;
	<schema:offers> [
		a <schema:Offer> ;
		<schema:price> "45.00" ;
		<schema:priceCurrency> "USD" ;
		<schema:priceValidFrom> "2017-11-1T02:00:00-07:00"^^:xsd:DateTime ;
		<schema:priceValidUntil> "2017-11-17T18:00:00-07:00"^^xsd:DateTime ;
		<schema:priceSpecification> <rsu:ProcessingFee1>
	] ;
<rsu:event/139166> a <athlinks:AthleticRace>;
	<schema:name> "2k Turkey Toddle (un-timed)" ;
	<athlinks:raceType> "running race" ;
	<athlinks:distance> [
		a <schema:QuantitativeValue> ;
		<schema:value> "2" ;
		<schema:unitCode> "KEL" .
	] ;
	<schema:startDate> "2017-11-18T09:10:00-07:00"^^xsd:DateTime ;
	<schema:offers> [
		a <schema:Offer> ;
		<schema:price> "20.00" ;
		<schema:priceCurrency> "USD" ;
		<schema:priceValidFrom> "2016-12-1T02:00:00-07:00"^^:xsd:DateTime ;
		<schema:priceValidUntil> "2017-6-1T01:59:59-07:00"^^xsd:DateTime ;
		<schema:priceSpecification> <rsu:ProcessingFee2>
	] ;

	<schema:offers> [
		a <schema:Offer> ;
		<schema:price> "25.00" ;
		<schema:priceCurrency> "USD" ;
		<schema:priceValidFrom> "2017-6-1T02:00:00-07:00"^^:xsd:DateTime ;
		<schema:priceValidUntil> "2017-11-1T01:59:59-07:00"^^xsd:DateTime ;
		<schema:priceSpecification> <rsu:ProcessingFee2>
	] ;

	<schema:offers> [
		a <schema:Offer> ;
		<schema:price> "25.00" ;
		<schema:priceCurrency> "USD" ;
		<schema:priceValidFrom> "2017-11-1T02:00:00-07:00"^^:xsd:DateTime ;
		<schema:priceValidUntil> "2017-11-17T01:59:59-07:00"^^xsd:DateTime ;
		<schema:priceSpecification> <rsu:ProcessingFee2>
	] ;
```
