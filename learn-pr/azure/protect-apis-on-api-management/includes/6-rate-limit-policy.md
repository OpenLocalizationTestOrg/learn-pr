It is common to find that a few users over-use an API to the extent that you incur extra costs or that responsiveness to other uses is reduced. You can use throttling to limit access to API endpoints by putting limits on the number of times an API can be called within a specified period of time. 

The Census API, for example, is distributed to lots of government agencies, so the number of calls to the API may become significant. By applying a rate limit policy, we can enable a quick response to all requests as it will not be possible for a single client to use all the resource for the Census API. 

In this unit, you will learn how to use API Management policies to impose two types of throttling.

## Limit by subscription throttling

Subscription throttling allows you to set the rate limits by a specific API operation. It does not discriminate by the client. Instead, every request to the API or the specified operation is throttled in the same way. Using our census example, we could use subscription throttling to limit the number of times any of the APIs are called within a certain period. This configuration would result in clients receiving a 429 error when that limit was reached. The problem with this type of throttling is that it allows one client to use up all the requests before another client can use it.

For example, the following code demonstrates an example configuration that applies to all API operations:

```XML
<rate-limit calls="3" renewal-period="15" />
```

Alternatively, this configuration can be used to target a particular API operation:

```XML
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

## Limit by key throttling

Key throttling allows you to configure different rate limits by any client request value. This type of throttling offers a better way of managing the rate limits as it applies the limit to a specified request key, often the client IP address. It gives every client equal bandwidth for calling the API

```XML
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />
```

An example configuration to limit rate limit by a requests IP Address would be:

```XML
<rate-limit-by-key calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
```

When you choose to throttle by key, you will need to decide on specific requirements for rate limiting. For example, the table below lists three common ways of specifying the **counter-key**:

| Value | Detail |
| ----- | ------ |
| **context.Request.IpAddress** | Rates limited by client IP address |
| **context.Subscription.Id** | Rates limited by subscription ID |
| **context.Request.Headers.GetValue("My-Custom-Header-Value")** | Rates limited by a specified client request header value |
| | |

You may decide that you want each individual client IP to have its own bandwidth set, in which case you would use the **context.Request.IpAddress**. Alternatively, it could be that you want all requests from a particular domain name to be throttled as certain domains have many calls to the API. In that case you would specify **context.Request.Headers.GetValue("host")** which would rate limit by the domains from which the call was made.

> [!NOTE]
> The `<rate-limit-by-key>` policy is not available when your API Management gateway is in the Consumption tier. You can use `<rate-limit>`instead.