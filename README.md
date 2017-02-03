# rdf-ontology

#### This is currently in development, expect changes

# [Athlinks RDF API v0.1 (beta)](#athlinks-rdf-api-v0.1-(beta))

RDF is a standard model for data interchange on the Web. RDF has features that facilitate data merging even if the underlying schemas differ, and it specifically supports the evolution of schemas over time without requiring all the data consumers to be changed.
Our ontology is based off of schema.org and Open Graph(ogp.me). Search engines use schema.org heavily, and sites like Facebook and Pinterest use Open Graph.
It is our belief that this improves the portability of data for us and our partners.

- [Athlinks RDF API v0.1 (beta)](#athlinks-rdf-api-v0.1-(beta))
    - [More information about RDF](#more-information-about-rdf)
    - [The Athlinks Ontology](#the-athlinks-ontology)
    - [The Athlinks RDF Endpoint](#the-athlinks-rdf-endpoint)
        - [Turtle Formats currently supported](#turtle-formats-currently-supported)
    - [Authentication](#authentication)
    - [Turtle](#turtle)
    - [Result Submission](#result-submission)
    - [Units of Measurement](#units-of-measurement)
    - [Validate your RDF documents](#validate-your-rdf-documents)

## [More information about RDF](#more-information-about-rdf)

- https://en.wikipedia.org/wiki/Resource_Description_Framework - High level description of RDF and its formats
- http://blog.swirrl.com/articles/introduction-to-rdf/ - Concise description of RDF
- http://docs.rdf4j.org/rdf-tutorial/ - Getting started with RDF
- http://schema.org - An example ontology
- http://openid.net/connect/ - Overview of authentication methodology

## [The Athlinks Ontology](#the-athlinks-ontology)
https://github.com/athlinks/rdf-ontology/blob/master/athlinks-schema.ttl

This describes the types and predicates that Athlinks expects.

## [The Athlinks RDF Endpoint](#the-athlinks-rdf-endpoint)
You can post RDF data in a varity of Turtle based formats to:
http://adp.athlinks.com/rdf

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

## [Turtle](#turtle)

Here is an example event from RunSignUp converted to using the [Athlinks Ontology](https://github.com/athlinks/rdf-ontology/blob/master/athlinks-schema.ttl) and represented in Turtle.

[JSON representation of a RunSignUp Event](https://runsignup.com/Rest/race/1649/?format=json&future_events_only=T&race_headings=F&race_links=T&include_waiver=T&include_participant_caps=T&include_age_based_pricing=F&include_giveaway_details=F&include_questions=F&include_addons=T&include_membership_settings=T&include_corral_settings=F&include_donation_settings=T)

Converts to:

```ttl
@prefix athlinks: <http://adp.athlinks.com/2017/01/rdf-schema#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix rsu: <https://runsignup.com/Rest/> .
@prefix athlinks: <http://adp.athlinks.com/2017/01/rdf-schema#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix owl: <http://www.w3.org/2002/07/owl#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix schema: <http://schema.org/> .

rsu:race\/1649 a athlinks:AthleticSeries ;
	rdfs:label "10k Turkey Trek & 2k Turkey Toddle" ;
	rdfs:comment "This Arvada event supports ..." ;
	schema:mainEntityOfPage "https://runsignup.com/Race/CO/Arvada/SixPack10kand5k" ;
	schema:mainEntityOfPage "http://10kturkeytrek.com/index.html" ;
	schema:location [
		a schema:PostalAddress ;
		schema:streetAddress "Ralston Creek Trail @ Long Lake Ranch Park" ;
		schema:addressCountry "US" ;
		schema:addressLocality "Arvada" ;
		schema:addressRegion "CO" ;
		schema:postalCode "80007" ;
	] ;
	schema:logo "https://d368g9lw5ileu7.cloudfront.net/races/race1649-logo.bym87Y.png" ;
	schema:subEvent rsu:raceEventDays\/58262 .

rsu:raceEventDays\/58262 a athlinks:AthleticEvent ;
		schema:subEvent rsu:event\/139165 ;
		schema:subEvent rsu:event\/139166 .

rsu:\#ProcessingFee1 a schema:PaymentChargeSpecification ;
			schema:price "3.80" ;
			schema:priceCurrency "USD" .

rsu:\#ProcessingFee2 a schema:PaymentChargeSpecification ;
			schema:price "3.05" ;
			schema:priceCurrency "USD" .

rsu:event\/139165 a athlinks:AthleticRace;
	rdfs:label "10k Turkey Trek" ;
	athlinks:raceType "running race" ;
	athlinks:distance [
		a schema:QuantitativeValue ;
		schema:value "10" ;
		schema:unitCode "KEL" ;
	] ;
	schema:startDate "2017-11-18T09:00:00-07:00"^^xsd:dateTime ;
	schema:offers [
		a schema:Offer ;
		schema:price "35.00" ;
		schema:priceCurrency "USD" ;
		schema:priceValidFrom "2016-12-1T02:00:00-07:00"^^xsd:dateTime ;
		schema:priceValidUntil "2017-6-1T01:59:59-07:00"^^xsd:dateTime ;
		schema:priceSpecification rsu:ProcessingFee1 ;
	] , [
		a schema:Offer ;
		schema:price "40.00" ;
		schema:priceCurrency "USD" ;
		schema:priceValidFrom "2017-6-1T02:00:00-07:00"^^xsd:dateTime ;
		schema:priceValidUntil "2017-11-1T01:59:59-07:00"^^xsd:dateTime ;
		schema:priceSpecification rsu:ProcessingFee1 ;
	] , [
		a schema:Offer ;
		schema:price "45.00" ;
		schema:priceCurrency "USD" ;
		schema:priceValidFrom "2017-11-1T02:00:00-07:00"^^xsd:dateTime ;
		schema:priceValidUntil "2017-11-17T18:00:00-07:00"^^xsd:dateTime ;
		schema:priceSpecification rsu:ProcessingFee1 ;
	] .

rsu:event\/139166 a athlinks:AthleticRace;
	rdfs:label "2k Turkey Toddle (un-timed)" ;
	athlinks:raceType "running race" ;
	athlinks:distance [
		a schema:QuantitativeValue ;
		schema:value "2" ;
		schema:unitCode "KEL" ;
	] ;
	schema:startDate "2017-11-18T09:10:00-07:00"^^xsd:dateTime ;
	schema:offers [
		a schema:Offer ;
		schema:price "20.00" ;
		schema:priceCurrency "USD" ;
		schema:priceValidFrom "2016-12-1T02:00:00-07:00"^^xsd:dateTime ;
		schema:priceValidUntil "2017-6-1T01:59:59-07:00"^^xsd:dateTime ;
		schema:priceSpecification rsu:ProcessingFee2 ;
	] , [
		a schema:Offer ;
		schema:price "25.00" ;
		schema:priceCurrency "USD" ;
		schema:priceValidFrom "2017-6-1T02:00:00-07:00"^^xsd:dateTime ;
		schema:priceValidUntil "2017-11-1T01:59:59-07:00"^^xsd:dateTime ;
		schema:priceSpecification rsu:ProcessingFee2 ;
	] , [
		a schema:Offer ;
		schema:price "25.00" ;
		schema:priceCurrency "USD" ;
		schema:priceValidFrom "2017-11-1T02:00:00-07:00"^^xsd:dateTime ;
		schema:priceValidUntil "2017-11-17T01:59:59-07:00"^^xsd:dateTime ;
		schema:priceSpecification rsu:ProcessingFee2 ;
	] .
```

#### [Result Submission](#result-submission)

Once an event has been submitted such as in the example above, results for the event can be submitted. Note that the results must refer to the event in the results RDF doc.

[Example Results RDF Document](https://github.com/athlinks/rdf-ontology/blob/master/examples/results.example.ttl)

#### [Units of Measurement](#units-of-measurement)

[See GoodRelations UN/CEFACT Common Codes](http://wiki.goodrelations-vocabulary.org/Documentation/UN/CEFACT_Common_Codes)

#### [Validate your RDF documents](#validate-your-rdf-documents)

[Easy RDF Validation Service](http://www.easyrdf.org/converter)
