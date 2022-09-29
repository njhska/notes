## nuget相关

Update-Package -Reinstall
Update-Package -ProjectName 'YourProjectNameGoesHere' -Reinstall

## .csproj

.net core ：remove

## async await

.Result .Wait() 会发生死锁

async ()=>{

awit xxx

}

IAsyncEnumerable `<T>`

## orderby

thenby

## groupby

return IEnumerable `<IGroupping<Key,T>>`

IGrouping `<Key,T>继承自 IEnumerable<T>`

## 依赖注入

### scope生命周期

```c#
using(IServiceScope scope = serviceProvider.CreateScope()))
{}
```

离开scope对象会自动调用服务的dispose方法

## DDD中的Domain设计
