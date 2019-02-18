#Trace Logger for Sitecore

Over 10 years ago, I wrote an article on [trace logging with Log4Net](https://www.codeproject.com/Articles/15155/Tracing-with-Log4Net-and-the-Context-Singleton-Des) that described an approach to instrumenting code that will produced time results in your logs. I used this pattern for many web projects over the years and wanted a way to use it with Sitecore. 

I used this approach to fine tune [Handlebars](https://github.com/mtelligent/Handlebars-For-Sitecore) Binding Performance, and leverage it every time I want to figure out where the bottlenecks in code are. Implemented as a Pipeline Component that flushes on end,  it can be easily turned on and off to avoid writing unnecessary log statements. 

Here’s an example of the kind of output to expect: (which are real numbers for Handlebars for Sitecore to bind a simple collection container with four items to a simple template)

```
23456 16:35:16 INFO  Performance Data for: http://starterkit91.dev.local/Blog
Time		Since First	Since Last	Comments
4:35:16 PM	0.0000000	0.0000000	Timer Initialized
4:35:16 PM	0.0000000	0.0000000	Call to SetupContainer for ItemList
4:35:16 PM	0.0000000	0.0000000	Converting Items to Object
4:35:16 PM	0.0000000	0.0000000	{6310B9CB-0596-45BA-8019-35FCC5EE7E70}
4:35:16 PM	0.0009660	0.0009660	{82D317CD-92CC-4C52-A8CC-87010844BA80}
4:35:16 PM	0.0009660	0.0000000	{F887A83E-A772-4339-B0E5-6BE62188BABD}
4:35:16 PM	0.0009660	0.0000000	{0F9FCACB-E0DB-4A46-BF18-335EB6515721}
4:35:16 PM	0.0009660	0.0000000	Items processed, storing in context.
4:35:16 PM	0.0009660	0.0000000	Call to HandlebarManager.GetTempaltedContent
4:35:16 PM	0.0019959	0.0010299	Step 1: Fetching Compiled Template
4:35:16 PM	0.0019959	0.0000000	Getting Cache
4:35:16 PM	0.0019959	0.0000000	Template Ready, Step 2: Binding to Context Data
4:35:16 PM	0.0528503	0.0508544	Content Bound. Output Ready
4:35:16 PM	0.0647924	0.0119421	Call to SetupContainer for ItemList
4:35:16 PM	0.0657909	0.0009985	Converting Items to Object
4:35:16 PM	0.0657909	0.0000000	{49263816-E65D-45E7-8A31-BCA5378A4A7A}
4:35:16 PM	0.0657909	0.0000000	{BA98F9C4-E599-4E43-8A58-4B4F5BFC43C7}
4:35:16 PM	0.0657909	0.0000000	Items processed, storing in context.
```

You can use this approach to sample complex calls to identify which areas are taking time. 

The logger itself is implemented as a Singleton, so you can log across classes, projects and solutions. Just add a reference to SF.Foundation.TraceLogger and you can write code like this.

```TraceLogger.Current.Write("Call to SetupContainer for ItemList");```

When enabled, the output is flushed at the end of the request. If you don't want to wait until the end of the request, you can set the "SF.TraceLogger.LogImmediately" Sitecore Setting to true, and each logged item will log as it happens, in addition to the performance data logged at the end.
