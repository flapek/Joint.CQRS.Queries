# Joint.CQRS.Queries

## Queries

Adds ability to create and process **queries** in the sense of [CQRS](https://martinfowler.com/bliki/CQRS.html).

## Installation

```
dotnet add package Joint.CQRS.Queries
```

## Dependencies

- [Joint](https://www.nuget.org/packages/Joint/)

## Usage

Implement `IQuery<TResult>` interface in the selected class:

```c#
public class GetAccount : IQuery<AccountDto>
{
    public Guid Id { get; set; }
}
```

Create dedicated **query handler** class that implements `IQueryHandler<TQuery, TResult>` interface with `HandleAsync()` method:

```c#
public class GetAccountHandler : IQueryHandler<GetAccount, AccountDto>
{
    public Task<AccountDto> HandleAsync(GetAccount query)
    {
        //put the handling code here
    }
}
```

You can easily register all query handlers in DI container by calling `AddQueryHandlers()` method on `IJointBuilder`:

```c#
public IServiceProvider ConfigureServices(this IServiceCollection services)
{
    var builder = JointBuilder
        .Create(services)
        .AddQueryHandlers();

    //other registrations
    return builder.Build();
}
```

Dispatching a particular query object can be also done using Joint package. Start with registering in-memory dispatcher on your `IJointBuilder` by calling a `AddInMemoryEventDispatcher()` method:

```c#
public IServiceProvider ConfigureServices(this IServiceCollection services)
{
    services.AddOpenTracing();

    var builder = JointBuilder
        .Create(services)
        .AddQueryHandlers()
        .AddInMemoryQueryDispatcher();

    //other registrations
    return builder.Build();
}
```

Then simply inject `IQueryDispatcher` into a class and call `QueryAsync()` method:

```c#
public class AccountsService
{
    private readonly IQueryDispatcher _dispatcher;

    public AccountsService(IQueryDispatcher dispatcher)
    {
        _dispatcher = dispatcher;
    }

    public Task<AccountDto> GetAccountAsync(Guid id)
        => _dispatcher.QueryAsync(new GetAccount { Id = id });
}
```

## Table of Contents

- [Joint](https://github.com/flapek/Joint)
- [Joint.Auth](https://github.com/flapek/Joint.Auth)
- [Joint.CQRS.Commands](https://github.com/flapek/Joint.CQRS.Commands)
- [Joint.CQRS.Events](https://github.com/flapek/Joint.CQRS.Events)
- [Joint.CQRS.Queries](https://github.com/flapek/Joint.CQRS.Queries)
- [Joint.DB.Mongo](https://github.com/flapek/Joint.DB.Mongo)
- [Joint.DB.Redis](https://github.com/flapek/Joint.DB.Redis)
- [Joint.Docs.Swagger](https://github.com/flapek/Joint.Docs.Swagger)
- [Joint.Exception](https://github.com/flapek/Joint.Exception)
- [Joint.Logging](https://github.com/flapek/Joint.Logging)
- [Joint.WebApi](https://github.com/flapek/Joint.WebApi)
