# Adding Events with PutEvents<a name="AddEventsPutEvents"></a>

The `PutEvents` action sends multiple events to CloudWatch Events in a single request\. For more information, see [PutEvents](https://docs.aws.amazon.com/AmazonCloudWatchEvents/latest/APIReference/API_PutEvents.html) in the *Amazon CloudWatch Events API Reference* and [put\-events](https://docs.aws.amazon.com/cli/latest/reference/events/put-events.html) in the *AWS CLI Command Reference*\.

Each `PutEvents` request can support a limited number of entries\. For more information, see [CloudWatch Events Limits](cloudwatch_limits_cwe.md)\. The `PutEvents` operation attempts to process all entries in the natural order of the request\. Each event has a unique id that is assigned by CloudWatch Events after you call `PutEvents`\.

The following example Java code sends two identical events to CloudWatch Events:

```
PutEventsRequestEntry requestEntry = new PutEventsRequestEntry()
        .withTime(new Date())
        .withSource("com.mycompany.myapp")
        .withDetailType("myDetailType")
        .withResources("resource1", "resource2")
        .withDetail("{ \"key1\": \"value1\", \"key2\": \"value2\" }");

PutEventsRequest request = new PutEventsRequest()
        .withEntries(requestEntry, requestEntry);

PutEventsResult result = awsEventsClient.putEvents(request);

for (PutEventsResultEntry resultEntry : result.getEntries()) {
    if (resultEntry.getEventId() != null) {
        System.out.println("Event Id: " + resultEntry.getEventId());
    } else {
        System.out.println("Injection failed with Error Code: " + resultEntry.getErrorCode());
    }
}
```

The `PutEvents` result includes an array of response entries\. Each entry in the response array directly correlates with an entry in the request array using natural ordering, from the top to the bottom of the request and response\. The response `Entries` array always includes the same number of entries as the request array\.

## Handling Failures When Using PutEvents<a name="FailureHandling"></a>

By default, failure of individual entries within a request does not stop the processing of subsequent entries in the request\. This means that a response Entries array includes both successfully and unsuccessfully processed entries\. You must detect unsuccessfully processed entries and include them in a subsequent call\.

Successful result entries include Id value, and unsuccessful result entries include `ErrorCode` and `ErrorMessage` values\. The `ErrorCode` parameter reflects the type of error\. `ErrorMessage` provides more detailed information about the error\. The example below has three result entries for a `PutEvents` request\. The second entry has failed and is reflected in the response\.

**Example: PutEvents Response Syntax**

```
{
    "FailedEntryCount": 1, 
    "Entries": [
        {
            "EventId": "11710aed-b79e-4468-a20b-bb3c0c3b4860"
        },
        {   "ErrorCode": "InternalFailure",
            "ErrorMessage": "Internal Service Failure"
        }, 
            "EventId": "d804d26a-88db-4b66-9eaf-9a11c708ae82"
        }
    ]
}
```

Entries that were unsuccessfully processed can be included in subsequent `PutEvents` requests\. First, check the `FailedRecordCount` parameter in the `PutEventsResult` to confirm if there are failed records in the request\. If so, each `Entry` that has an `ErrorCode` that is not null should be added to a subsequent request\. For an example of this type of handler, refer to the following code\.

**Example: PutEvents failure handler**

```
PutEventsRequestEntry requestEntry = new PutEventsRequestEntry()
        .withTime(new Date())
        .withSource("com.mycompany.myapp")
        .withDetailType("myDetailType")
        .withResources("resource1", "resource2")
        .withDetail("{ \"key1\": \"value1\", \"key2\": \"value2\" }");

List<PutEventsRequestEntry> putEventsRequestEntryList = new ArrayList<>();
for (int i = 0; i < 3; i++) {
    putEventsRequestEntryList.add(requestEntry);
}

PutEventsRequest putEventsRequest = new PutEventsRequest();
putEventsRequest.withEntries(putEventsRequestEntryList);
PutEventsResult putEventsResult = awsEventsClient.putEvents(putEventsRequest);

while (putEventsResult.getFailedEntryCount() > 0) {
    final List<PutEventsRequestEntry> failedEntriesList = new ArrayList<>();
    final List<PutEventsResultEntry> PutEventsResultEntryList = putEventsResult.getEntries();
    for (int i = 0; i < PutEventsResultEntryList.size(); i++) {
        final PutEventsRequestEntry putEventsRequestEntry = putEventsRequestEntryList.get(i);
        final PutEventsResultEntry putEventsResultEntry = PutEventsResultEntryList.get(i);
        if (putEventsResultEntry.getErrorCode() != null) {
            failedEntriesList.add(putEventsRequestEntry);
        }
    }
    putEventsRequestEntryList = failedEntriesList;
    putEventsRequest.setEntries(putEventsRequestEntryList);
    putEventsResult = awsEventsClient.putEvents(putEventsRequest);
    }
```

## Sending Events Using the AWS CLI<a name="SendEventsAWSCli"></a>

You can use the AWS CLI to send custom events\. The following example puts one custom event into CloudWatch Events:

```
aws events put-events \
--entries '[{"Time": "2016-01-14T01:02:03Z", "Source": "com.mycompany.myapp", "Resources": ["resource1", "resource2"], "DetailType": "myDetailType", "Detail": "{ \"key1\": \"value1\", \"key2\": \"value2\" }"}]'
```

You can also create a file for example, **entries\.json**, like the following:

```
[
  {
    "Time": "2016-01-14T01:02:03Z",
    "Source": "com.mycompany.myapp",
    "Resources": [
      "resource1",
      "resource2"
    ],
    "DetailType": "myDetailType",
    "Detail": "{ \"key1\": \"value1\", \"key2\": \"value2\" }"
  }
]
```

You can use the AWS CLI to read the entries from this file and send events\. At a command prompt, type:

```
aws events put-events --entries file://entries.json
```