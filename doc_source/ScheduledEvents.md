# Schedule Expressions for Rules<a name="ScheduledEvents"></a>

You can create rules that self\-trigger on an automated schedule in CloudWatch Events using cron or rate expressions\. All scheduled events use UTC time zone and the minimum precision for schedules is 1 minute\.

CloudWatch Events supports cron expressions and rate expressions\. Rate expressions are simpler to define but don't offer the fine\-grained schedule control that cron expressions support\. For example, with a cron expression, you can define a rule that triggers at a specified time on a certain day of each week or month\. In contrast, rate expressions trigger a rule at a regular rate, such as once every hour or once every day\.

**Note**  
CloudWatch Events does not provide second\-level precision in schedule expressions\. The finest resolution using a cron expression is a minute\. Due to the distributed nature of the CloudWatch Events and the target services, the delay between the time the scheduled rule is triggered and the time the target service honors the execution of the target resource might be several seconds\. Your scheduled rule is triggered within that minute, but not on the precise 0th second\.

**Topics**
+ [Cron Expressions](#CronExpressions)
+ [Rate Expressions](#RateExpressions)

## Cron Expressions<a name="CronExpressions"></a>

Cron expressions have six required fields, which are separated by white space\. 

**Syntax**

```
cron(fields)
```


| **Field** | **Values** | **Wildcards** | 
| --- | --- | --- | 
|  Minutes  |  0\-59  |  , \- \* /  | 
|  Hours  |  0\-23  |  , \- \* /  | 
|  Day\-of\-month  |  1\-31  |  , \- \* ? / L W  | 
|  Month  |  1\-12 or JAN\-DEC  |  , \- \* /  | 
|  Day\-of\-week  |  1\-7 or SUN\-SAT  |  , \- \* ? L \#  | 
|  Year  |  1970\-2199  |  , \- \* /  | 

**Wildcards**
+ The **,** \(comma\) wildcard includes additional values\. In the Month field, JAN,FEB,MAR would include January, February, and March\.
+ The **\-** \(dash\) wildcard specifies ranges\. In the Day field, 1\-15 would include days 1 through 15 of the specified month\.
+ The **\*** \(asterisk\) wildcard includes all values in the field\. In the Hours field, **\*** would include every hour\. You cannot use **\*** in both the Day\-of\-month and Day\-of\-week fields\. If you use it in one, you must use **?** in the other\.
+ The **/** \(forward slash\) wildcard specifies increments\. In the Minutes field, you could enter 1/10 to specify every tenth minute, starting from the first minute of the hour \(for example, the 11th, 21st, and 31st minute, and so on\)\.
+ The **?** \(question mark\) wildcard specifies one or another\. In the Day\-of\-month field you could enter **7** and if you didn't care what day of the week the 7th was, you could enter **?** in the Day\-of\-week field\.
+ The **L** wildcard in the Day\-of\-month or Day\-of\-week fields specifies the last day of the month or week\.
+ The **W** wildcard in the Day\-of\-month field specifies a weekday\. In the Day\-of\-month field, 3W specifies the day closest to the third weekday of the month\.
+ The **\#** wildcard in the Day\-of\-week field specifies a certain instance of the specified day of the week within a month\. For example, 3\#2 would be the second Tuesday of the month: the 3 refers to Tuesday because it is the third day of each week, and the 2 refers to the second day of that type within the month\.

**Limits**
+ You can't specify the Day\-of\-month and Day\-of\-week fields in the same cron expression\. If you specify a value \(or a \*\) in one of the fields, you must use a **?** \(question mark\) in the other\.
+ Cron expressions that lead to rates faster than 1 minute are not supported\.

**Examples**  
You can use the following sample cron strings when creating a rule with schedule\.


| Minutes | Hours | Day of month | Month | Day of week | Year | Meaning | 
| --- | --- | --- | --- | --- | --- | --- | 
|  0  |  10  |  \*  |  \*  |  ?  |  \*  |  Run at 10:00 am \(UTC\) every day  | 
|  15  |  12  |  \*  |  \*  |  ?  |  \*  |  Run at 12:15 pm \(UTC\) every day  | 
|  0  |  18  |  ?  |  \*  |  MON\-FRI  |  \*  |  Run at 6:00 pm \(UTC\) every Monday through Friday  | 
|  0  |  8  |  1  |  \*  |  ?  |  \*  |  Run at 8:00 am \(UTC\) every 1st day of the month  | 
|  0/15  |  \*  |  \*  |  \*  |  ?  |  \*  |  Run every 15 minutes  | 
|  0/10  |  \*  |  ?  |  \*  |  MON\-FRI  |  \*  |  Run every 10 minutes Monday through Friday  | 
|  0/5  |  8\-17  |  ?  |  \*  |  MON\-FRI  |  \*  |  Run every 5 minutes Monday through Friday between 8:00 am and 5:55 pm \(UTC\)  | 

The following examples show how to use Cron expressions with the AWS CLI [put\-rule](https://docs.aws.amazon.com/cli/latest/reference/events/put-rule.html) command\. The first example creates a rule that is triggered every day at 12:00pm UTC\.

```
aws events put-rule --schedule-expression "cron(0 12 * * ? *)" --name MyRule1
```

The next example creates a rule that is triggered every day, at 5 and 35 minutes past 2:00pm UTC\.

```
aws events put-rule --schedule-expression "cron(5,35 14 * * ? *)" --name MyRule2
```

The next example creates a rule that is triggered at 10:15am UTC on the last Friday of each month during the years 2002 to 2005\.

```
aws events put-rule --schedule-expression "cron(15 10 ? * 6L 2002-2005)" --name MyRule3
```

## Rate Expressions<a name="RateExpressions"></a>

A rate expression starts when you create the scheduled event rule, and then runs on its defined schedule\.

Rate expressions have two required fields\. Fields are separated by white space\.

**Syntax**

```
rate(value unit)
```

value  
A positive number\.

unit  
The unit of time\. Different units are required for values of 1, such as `minute`, and values over 1, such as `minutes`\.  
Valid values: minute \| minutes \| hour \| hours \| day \| days

**Limits**  
If the value is equal to 1, then the unit must be singular\. Similarly, for values greater than 1, the unit must be plural\. For example, rate\(1 hours\) and rate\(5 hour\) are not valid, but rate\(1 hour\) and rate\(5 hours\) are valid\.

**Examples**  
The following examples show how to use rate expressions with the AWS CLI [put\-rule](https://docs.aws.amazon.com/cli/latest/reference/events/put-rule.html) command\. The first example triggers the rule every minute, the second example triggers it every 5 minutes, the next triggers it once an hour, and the third example triggers it once a day\.

```
aws events put-rule --schedule-expression "rate(1 minute)" --name MyRule2
```

```
aws events put-rule --schedule-expression "rate(5 minutes)" --name MyRule3
```

```
aws events put-rule --schedule-expression "rate(1 hour)" --name MyRule4
```

```
aws events put-rule --schedule-expression "rate(1 day)" --name MyRule5
```