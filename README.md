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

1. **E-09**, Fixture `E-09_event_with_out_envelope.json`,  Send a Caliper event that contains 
    `@context = http://purl.imsglobal.org/ctx/caliper/v1p1, id, type (valid string from a fixed vocabulary), actor (id and type attributes), action (valid string from a fixed vocabulary), object, eventTime (ISO 8601 format) without an envelope`
    1. This test case failed as Endpoint sent 200 Ok with a response that is routing to stream. Below is the response. This event was not in BigQuery, raw bucket but i found it in quarantine bucket with empty file having nothing in it
        {"urn:uuid:b26ea330-c6a2-4766-8509-5dbfba14b203": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "35630329643383"}}
        
2. **E-06**, Fixture `E-06_event_with_only_envelope_no_payload.json`, 
    1. This case is sending a caliper event with envelope with out a payload, with proper OAuth token, pass the test return {HTTP code: Response} => {200 : {}}. 
3. **E-08**, Fixture `E-08_event_with_only_envelope_no_payload.json`, 
    1. This case is sending a caliper event with envelope with out a payload, with wrong OAuth token, pass the test return {HTTP code: Response} => {400 : bearer token missing or not recognized}
4. **E-07**, Fixture `E-07_event_with_only_envelope_no_payload.json`, 
    1. This case is sending a caliper event with envelope with out a payload, with missing OAuth token header, pass the test return {HTTP code: Response} => {400 : bearer token missing or not recognized}
5. **E-17**, Fixture `E-17_gte_routed_to_pub_sub_topic.json` , 
    1. Event sent successfully with proper routing information. {HTTP code: Response} => {200 : {"urn:uuid:c982635a-cae4-420e-a6f5-7cd1c0d8de39": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "340963522}
6. **E-16**, Fixture `E-16_gte_routed_to_pub_sub_topic.json` ,  this test case is same as E-17 the only difference is the E-16 checks for the presence of a response. 
7. **E-19**, Fixture `E-17_gte_routed_to_pub_sub_topic.json` , 
       1. Event sent successfully with proper routing information and found in the raw bucket. {HTTP code: Response} => {200 : {"urn:uuid:c982635a-cae4-420e-a6f5-7cd1c0d8de39": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "340963522}
           this use case events are not written to BigQuery. But this call set the stage for next step for event processing. 
8. **E-15**, Fixture `E-15_multiple_events_in_one.json`,  Multiple events wrapped in one envelope and in BigQuery these events appeared as seperate table entry
    1. {HTTP code: Response} => {200: {"urn:uuid:1bc6ab92-fc6c-42f0-b47c-57a6580952f5": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "36761342368097"}, 
                                       "urn:uuid:9a5595aa-0803-49c6-b251-7d90994030c2": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "36761783696015"}, 
                                       "urn:uuid:220ec031-4500-4f73-96e4-c1bc4df30d83": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "36763424717427"}, 
                                       "urn:uuid:94e3b5e0-98c8-4057-855b-91f9a26697c8": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "36762709009312"}, 
                                       "urn:uuid:386c77b6-17cf-49b0-a6cf-61269261ac4f": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "36761196357185"}}}
9. **E-25**, Fixtures `E-25_event_in_big_query_timed_1.json` , `event_in_big_query_timed_1.json`, I see the events in Big query but around 20/29/32 sec.
    1. {"urn:uuid:c75e7657-5a98-48d0-ab55-b0ae79aa3c05": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "37308065254025"}}
    2. {"urn:uuid:52e61d74-bd42-46d3-9610-487ab04c8102": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "37314317365443"}}
10. **E-21**, Fixture `E-21_quarantine_flow.json`  Ended up in the quarantine bucket(Buckets/stream-unizin-udp-quarantine-umich-dev/STREAM-unizin-umich-umich-stream-GoldLectureCapture-dev)
      {HTTP code: Response} => {200: {"urn:uuid:d997cdea-57ee-4673-a392-191f16128e82": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "40055452352074"}}}
11. **E-20**, Fixture `E-20_event_in_big_query_timed_1.json`, Events are in BigQuery with enriched. Verifed that data that is enriched have correct CloudSQL record number. More on how to 
    verify enriched events is described below
12. **E-24**, Fixture `E-24_gte_routed_to_pub_sub_topic.json` Event UUID correctly retrived from BQ
      1. `select * from learning_datasets.enriched_events where id = 'd997cdea-57ee-4673-a392-191f16128e82' `
13. **E-03** Fixture `E-03_gte_routed_to_pub_sub_topic.json` This is caliper compliance test and endpoint responded with correct info. 
       1. {HTTP code: Response} => {200 : {"urn:uuid:c982635a-cae4-420e-a6f5-7cd1c0d8de39": {"STREAM-unizin-umich-umich-stream-GoldUMichLectureCapture-dev": "340963522}
14. **E-18** Fixture `E-18_silver_tier_with_unknown_edapp.json`
       1. {HTTP code: Response} => {200 :{"urn:uuid:d25f86dd-b742-447f-961c-c27a7983c6a9": {"STREAM-unizin-umich-umich-stream-Silver-dev": "55133711350184"}}}
15. **E-22** Fixture `E-22_st_event_in_big_query.json` Event found in BQ when queried. the file `event_in_big_query.json` is the how the event in BQ tables
             `ed_app`, `ucdm_course_id`, and `ucdm_actor_id` columns are null
       1. {HTTP code: Response} => {200 :{"urn:uuid:81a334cd-9dff-45ab-9d53-cefbad6ce7ed": {"STREAM-unizin-umich-umich-stream-Silver-dev": "55540394456272"}}}
       2. `select * from learning_datasets.enriched_events where id = '81a334cd-9dff-45ab-9d53-cefbad6ce7ed'` 
           


#### Postman tips
1. In order to reuse the fixtures in this repo UUID and the eventTime/SendTime time needs to be changed while sending to endpoint. Postman could of great help for it. All you have to do is simple steps
    1. Set the [Enviroment](https://www.getpostman.com/docs/postman/environments_and_globals/variables) in Postman
    2.  put the following code in the Postman `Pre-request Script` tab 
    
    ```
       uuid4 = function() {
           return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, _uuid4);
         };
         //// OPTIMIZATION - cache callback
         _uuid4 = function(cc) {
           var rr = Math.random() * 16 | 0; return (cc === 'x' ? rr : (rr & 0x3 | 0x8)).toString(16);
         };
         var uuid = uuid4()
         console.log(uuid)
       postman.setEnvironmentVariable('ctime',(new Date()).toISOString());
       postman.setEnvironmentVariable('uuid',uuid);
    ```
       
    3. Postman `Body` tab refer the 2 variables as `{{uuid}}` and `{{ctime}}` in caliper JSON refer it  `"id": "{{uuid}}" "sendTime": "{{ctime}}" "eventTime": "{{ctime}}"`,

### steps for checking enriched event maps to CloudSQL 
1. login to CloudSQL from a terminal using `psql -h 130.211.135.130  -U umich_readonly -p 5432 unizin`, when prompted use password stored in the Box
    1. Basic command 
        1. `\l` = shows list of Databases on the server
        2. `\dt` = show list of table in the `unizin` DB there are total of `195` tables
2. get the enriched ucdm course/user id and sis_id from the BigQuery tables `ucdm_course_offering_id`	`ucdm_actor_id`	`course_offering_id`	`event_actor_id`. Below is how where you can look for them in the caliper event
    ```
    "federatedSession": {
        "id": "urn:instructure:canvas:umich:session:bDfEedD750BbAB50d6fCB2AFd974FFb37f7eF9",
        "type": "LtiSession",
        "user": "https://mcommunity.umich.edu/#profile:ststvii",
        "messageParameters": {
          "custom_canvas_course_id": 65745,
          "custom_canvas_user_id": 115260,
          "lis_course_offering_sourcedid": "test_term_00199999003",
          "lis_person_sourcedid": 34270233,
          "resource_link_id": "9a17100e4c806512a4a34b20d61dd8786d2c9826"
        }
      }
      
    "extensions": {
        "com.unizin.canvas": {
          "ucdm_course_offering_id": "522997",
          "ucdm_actor_id": "2"
        }
      }
     ``` 
 3. `UdpEntityKeyMapping` table defines the key mapping between `SourceKey` and `UcdmKey`
    So for an SIS id of `abcd`, you can search `where SourceKey = 'abcd'` and find which `UcdmKey` that corresponds to
    If it is a `Person` record, that corresponds to their `Person.PersonId` value
    If it’s a `Course offering` record, that corresponds to their `Course.OrganizationId` value
    ```
    unizin=> select * from udpentitykeymapping where sourcekey = '34270233';
      id    | systemprovisioningid | ucdmentityid | sourcekey | ucdmkey
    ---------+----------------------+--------------+-----------+---------
    1047035 |                    3 |            1 | 34270233  |       2
    
    unizin=> select * from udpentitykeymapping where sourcekey = 'test_term_00199999003';
       id    | systemprovisioningid | ucdmentityid |       sourcekey       | ucdmkey
    ---------+----------------------+--------------+-----------------------+---------
     1047034 |                    3 |            4 | test_term_00199999003 |  522997
    ```

            

