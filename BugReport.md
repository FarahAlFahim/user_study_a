## Title: Missing NullPointer check in AppSchedulingInfo causes RM to die

## Description
The issue arises in the `checkForDeactivation()` method of the `AppSchedulingInfo` class, where a `NullPointerException` is thrown due to the lack of null checks on the result of `getResourceRequest()`. This method can return null if the `requests` map does not contain an entry for the specified priority. The subsequent call to `request.getNumContainers()` on a null reference leads to the ResourceManager crashing.

## StackTrace
```
java.lang.NullPointerException,
    at org.apache.hadoop.yarn.server.resourcemanager.scheduler.AppSchedulingInfo.checkForDeactivation(AppSchedulingInfo.java:383),
    at org.apache.hadoop.yarn.server.resourcemanager.scheduler.AppSchedulingInfo.decrementOutstanding(AppSchedulingInfo.java:375)
    at org.apache.hadoop.yarn.server.resourcemanager.scheduler.AppSchedulingInfo.allocateOffSwitch(AppSchedulingInfo.java:360),
    at org.apache.hadoop.yarn.server.resourcemanager.scheduler.AppSchedulingInfo.allocate(AppSchedulingInfo.java:270)
```

## RootCause
The NullPointerException is caused by the `checkForDeactivation()` method attempting to dereference a null ResourceRequest object returned by `getResourceRequest()`, which can occur if the requests map does not contain an entry for the specified priority.

## Suggestions
Implement null checks in the `checkForDeactivation()` method to handle cases where `getResourceRequest()` returns null.

## ProblemLocation
```
{
    class: AppSchedulingInfo.java,
    methods: [checkForDeactivation, getResourceRequest, getPriorities]
}
```

## PossibleFix
```java
ResourceRequest request = getResourceRequest(priority, ResourceRequest.ANY);
if (request != null){
    if (request.getNumContainers() > 0) {
    deactivate = false;
    break;
}
```