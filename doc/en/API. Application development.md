Applications (custom components) for FastCGI-Daemon are the shared objects (so). You should compile `libfastcgi-daemon2.so` and use headers in `include/fastcgi2` for development.

API contains the namespace `fastcgi`, with the classes:

|**Class**|**Description**|
|---------|------------|
|[Component](Class Component)|Abstract class, mandatory for inheritance by a custom component. It is the base of a custom component.|
|[ComponentContext](Class ComponentContext)|Keeps the context of a custom component.|
|[Config](Class Config)|Contains configuration settings of daemon.|
|[Cookie](Class Cookie)|Operates with cookies.|
|[DataBuffer](Class DataBuffer)|Operates with data.|
|[SegmentIterator](Class SegmentIterator)|Iterator for the segmented `DataBuffer`.|
|[Handler](Class Handler)|Abstract class, mandatory for inheritance by a custom component. It is the base of a custom request handler.|
|[HandlerContext](Class HandlerContext)|Stores global parameters of a custom request handler.|
|[Logger](Class Logger)|Operates with logging.|
|[Request](Class Request)|Operates with a request. It used to parse and modify request and to create a response for it.|

Custom component must inherit from `Component` and `Handler`. Methods `onLoad()`, `onUnload()` and `handleRequest(...)` should be defined. Component should use macros defined in `ComponentFactory.h` in order to provide its functionality to daemon.

For example, user defines 2 components in its module (.so):

```
class ExampleComponent1 : virtual public fastcgi::Component, virtual public fastcgi::Handler{...}
class ExampleComponent2 : virtual public fastcgi::Component, virtual public fastcgi::Handler{...}
```

They should be registered as follows:

```
FCGIDAEMON_REGISTER_FACTORIES_BEGIN()
FCGIDAEMON_ADD_DEFAULT_FACTORY("factory1", ExampleComponent1)
FCGIDAEMON_ADD_DEFAULT_FACTORY("factory2", ExampleComponent2)
FCGIDAEMON_REGISTER_FACTORIES_END()
```

To use syslog, custom component should get `daemon-logger` using the `findComponent` method of [ComponentContext](Class ComponentContext) class. Then use methods of [Logger](Class Logger) class as follows:

```
fastcgi::ComponentContext *cntx = context();
fastcgi::Logger *log = cntx->findComponent<fastcgi::Logger>("daemon-logger");
log->info("some useful info");
```
