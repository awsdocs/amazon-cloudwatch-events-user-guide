# Calculating PutEvents Event Entry Sizes<a name="CalculatePutEventsEntrySize"></a>

You can inject custom events into CloudWatch Events using the `PutEvents` action\. You can inject multiple events using the `PutEvents` action as long as the total entry size is less than 256KB\. You can calculate the event entry size beforehand by following the steps below\. You can then batch multiple event entries into one request for efficiency\.

**Note**  
The size limit is imposed on the entry\. Even if the entry is less than the size limit, it does not mean that the event in CloudWatch Events is also less than this size\. On the contrary, the event size is always larger than the entry size due to the necessary characters and keys of the JSON representation of the event\. For more information, see [Event Patterns in CloudWatch Events](CloudWatchEventsandEventPatterns.md)\.

The `PutEventsRequestEntry` size is calculated as follows:
+ If the `Time` parameter is specified, it is measured as 14 bytes\.
+ The `Source` and `DetailType` parameters are measured as the number of bytes for their UTF\-8 encoded forms\.
+ If the `Detail` parameter is specified, it is measured as the number of bytes for its UTF\-8 encoded form\.
+ If the `Resources` parameter is specified, each entry is measured as the number of bytes for their UTF\-8 encoded forms\.

The following example Java code calculates the size of a given `PutEventsRequestEntry` object:

```
int getSize(PutEventsRequestEntry entry) {
    int size = 0;
    if (entry.getTime() != null) {
        size += 14;
    }
    size += entry.getSource().getBytes(StandardCharsets.UTF_8).length;
    size += entry.getDetailType().getBytes(StandardCharsets.UTF_8).length;
    if (entry.getDetail() != null) {
        size += entry.getDetail().getBytes(StandardCharsets.UTF_8).length;
    }
    if (entry.getResources() != null) {
        for (String resource : entry.getResources()) {
            if (resource != null) {
                size += resource.getBytes(StandardCharsets.UTF_8).length;
            }
        }
    }
    return size;
}
```