# udp-validation-fixtures
This repo hold the JSON fixtures used in testing UDP, the test cases are described in the Sheets. 

1. Send a Caliper event that contains @context = http://purl.imsglobal.org/ctx/caliper/v1p1, id, type (valid string from a fixed vocabulary), actor (id and type attributes), action (valid string from a fixed vocabulary), object, eventTime (ISO 8601 format) without an envelope
    1. This test case failed as Endpoint sent 200 Ok with a response that is routing to stream. Below is the response. This event was not in BigQuery, raw bucket but i found it in quarantine bucket with empty file having nothing in it
        {"urn:uuid:b26ea330-c6a2-4766-8509-5dbfba14b203": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "35630329643383"}}

