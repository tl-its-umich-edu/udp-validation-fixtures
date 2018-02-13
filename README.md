# udp-validation-fixtures
This repo hold the JSON fixtures used in testing UDP, the test cases are described in the [Sheets](https://docs.google.com/spreadsheets/d/1XGj76VRC1t2NH3LeA60U2rHqogqx_CIy1KAL48Cv5NQ/edit#gid=0). 

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

            

