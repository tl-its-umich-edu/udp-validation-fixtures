# udp-validation-fixtures

### Lecture Capture Gold Tire testing:
This repo hold the JSON fixtures used in testing UDP, the test cases are described in the [Sheets](https://docs.google.com/spreadsheets/d/1XGj76VRC1t2NH3LeA60U2rHqogqx_CIy1KAL48Cv5NQ/edit#gid=0). Here is the work flow 
```
if(gold-tier LC):
   store in raw;

   if(enrichable)
      write to BQ;
   else
      write to quarantine;
```

1. Fixture `event_with_out_envelope.json`, Test Case: E-09, Send a Caliper event that contains 
    `@context = http://purl.imsglobal.org/ctx/caliper/v1p1, id, type (valid string from a fixed vocabulary), actor (id and type attributes), action (valid string from a fixed vocabulary), object, eventTime (ISO 8601 format) without an envelope`
    1. This test case failed as Endpoint sent 200 Ok with a response that is routing to stream. Below is the response. This event was not in BigQuery, raw bucket but i found it in quarantine bucket with empty file having nothing in it
        {"urn:uuid:b26ea330-c6a2-4766-8509-5dbfba14b203": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "35630329643383"}}
        
2. Fixture `event_with_only_envelope_no_payload.json`, Test case: E-06
    1. This case is sending a caliper event with envelope with out a payload, with proper OAuth token, pass the test return {HTTP code: Response} => {200 : {}}. 
3. Fixture `event_with_only_envelope_no_payload.json`, Test case: E-08
    1. This case is sending a caliper event with envelope with out a payload, with wrong OAuth token, pass the test return {HTTP code: Response} => {400 : bearer token missing or not recognized}
4. Fixture `event_with_only_envelope_no_payload.json`, Test case: E-07
    1. This case is sending a caliper event with envelope with out a payload, with missing OAuth token header, pass the test return {HTTP code: Response} => {400 : bearer token missing or not recognized}
5. Fixture `E_returning_routed_to_gold_tire_pub_sub_topic.json` , Test case: E-17
    1. Event sent successfully with proper routing information. {HTTP code: Response} => {200 : {"urn:uuid:c982635a-cae4-420e-a6f5-7cd1c0d8de39": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "340963522}
6. Fixture `E_returning_routed_to_gold_tire_pub_sub_topic.json` , Test case: E-19
       1. Event sent successfully with proper routing information and found in the raw bucket. {HTTP code: Response} => {200 : {"urn:uuid:c982635a-cae4-420e-a6f5-7cd1c0d8de39": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "340963522}
           this use case events are not written to BigQuery. But this call set the stage for next step for event processing. 
           

            

