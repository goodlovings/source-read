- 1、interceptors作用于全局，所有请求的发送和接收都会进行拦截；
- 2、interceptors不缺分接口请求类型；
- 3、interceptors中能够访问到所有的请求配置项；

- 2、transformXxxx（transformReuest和transformResponse）通过配置作用于单个请求的发送和接收拦截；
- transformReuest之作用于 'PUT', 'POST' 和 'PATCH' 这几个请求方法；
- transformXxxx 只能访问数据和请求/响应的头配置；

