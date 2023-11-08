# Amido Stacks Messaging AWS SQS

This library is wrapper around [Amazon Simple Queue Service](https://aws.amazon.com/sqs/).
The main goals are:

1. To publish messages to a configured SQS queue.
2. To receive messages from a configured SQS queue.

## 1. Requirements

You will need an SQS instance in order to use this library. To create an SQS instance, you can follow the instructions in the [AWS SQS Documentation](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/sqs-apis-intro.html).

## 2. Registration/Usage

### 2.1 Dependencies

- `Amido.Stacks.Application.CQRS.Abstractions`
- `Amido.Stacks.Configuration`
- `Amido.Stacks.Core`
- `AWSSDK.SQS`

### 2.2 Currently Supported messages

The library currently supports:

- Publishing and receiving events that implement `Amido.Stacks.Application.CQRS.ApplicationEvents.IApplicationEvent`.

### 2.3 Usage in dotnet application

#### 2.3.1 Events

In this example, the `MenuCreatedEvent` is handled by the `MenuCreatedEventHandler`.  The event must implement the `IApplicationEvent` interface and the handler must implement the `IApplicationEventHandler<NotifyEvent>` interface.  Both of these interfaces are found in the [Amido.Stacks.Application.CQRS.ApplicationEvents](https://github.com/Ensono/stacks-dotnet-packages-cqrs-abstractions) namespace.

***MenuCreatedEvent.cs***

```cs
public class MenuCreatedEvent : IApplicationEvent
{
    public MenuCreatedEvent(Guid correlationId, int eventCode, int operationCode)
    {
        CorrelationId = correlationId;
        EventCode = eventCode;
        OperationCode = operationCode;
    }

    public Guid CorrelationId { get; }
    public int EventCode { get; }
    public int OperationCode { get; }
}
```

***MenuCreatedEventHandler.cs***

```cs
public class MenuCreatedEventHandler : IApplicationEventHandler<MenuCreatedEvent>
{
    public Task HandleAsync(MenuCreatedEvent applicationEvent)
    {
       //  Code to handle the event...
        return Task.CompletedTask;
    }
}
```

#### 2.3.1.1 AWS Options Configuration

***appsettings.json***

```json
{
    "AwsSqsConfiguration": {
        "QueueUrl": {
            "Identifier": "SQS_QUEUE_URL",
            "Source": "Environment"
        }
    }
}
```

***Usage***

```cs
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<AwsSqsConfiguration>(context.Configuration.GetSection("AwsSqsConfiguration"));
        services.AddAwsSqs();
        services.AddSecrets();  // To read the secret defined as an environment variable appsettings.json.
    }
}
```
