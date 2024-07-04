
**1、zipkin搭建：**
 docker run -d -p 9411:9411 --name zipkin openzipkin/zipkin


**2、nuget包安装：**
zipkin4net（追踪器）
zipkin4net.middleware.aspnetcore（aspnetcore 中间件）


**3、Program.cs**
var app = builder.Build();
#region 注册zipkin
RegisterZipkinTracer(app, app.Lifetime);
#endregion


void RegisterZipkinTracer(IApplicationBuilder app,  IHostApplicationLifetime lifetime)
{
    //依赖注入
    //var lifetime = app.ApplicationServices.GetService<IHostApplicationLifetime>();
    var loggerFactory = app.ApplicationServices.GetService<ILoggerFactory>();   //ILoggerFactory loggerFactory,
    lifetime.ApplicationStarted.Register(() =>
    {
        TraceManager.SamplingRate = 1.0f; //记录数据密度，1.0代表全部记录
        var logger = new TracingLogger(loggerFactory, "zipkin4net");//内存数据
        var httpSender = new HttpZipkinSender("http://localhost:9411", "application/json");//zipkin服务器地址及端口号

        //注册zipkin
        var tracer = new ZipkinTracer(httpSender, new JSONSpanSerializer(), new Statistics());
        //var tracer = new ZipkinTracer(httpSender, new JSONSpanSerializer());

        /// 控制台输出
        var consoleTracer = new zipkin4net.Tracers.ConsoleTracer();

        TraceManager.RegisterTracer(tracer);//注册
        TraceManager.RegisterTracer(consoleTracer);//控制台输入日志
        TraceManager.Start(logger);//放到内存中的数据
    });
    lifetime.ApplicationStopped.Register(() => TraceManager.Stop());
    app.UseTracing("service_a");//微服务名称，保证唯一
}



