# ngx_http_fastcgi_module

- [指令](#directives)
    - [fastcgi_bind](#fastcgi_bind)
    - [fastcgi_buffer_size](#fastcgi_buffer_size)
    - [fastcgi_buffering](#fastcgi_buffering)
    - [fastcgi_buffers](#fastcgi_buffers)
    - [fastcgi_busy_buffers_size](#fastcgi_busy_buffers_size)
    - [fastcgi_cache](#fastcgi_cache)
    - [fastcgi_cache_background_update](#fastcgi_cache_background_update)
    - [fastcgi_cache_bypass](#fastcgi_cache_bypass)
    - [fastcgi_cache_key](#fastcgi_cache_key)
    - [fastcgi_cache_lock](#fastcgi_cache_lock)
    - [fastcgi_cache_lock_age](#fastcgi_cache_lock_age)
    - [fastcgi_cache_lock_timeout](#fastcgi_cache_lock_timeout)
    - [fastcgi_cache_max_range_offset](#fastcgi_cache_max_range_offset)
    - [fastcgi_cache_methods](#fastcgi_cache_methods)
    - [fastcgi_cache_min_uses](#fastcgi_cache_min_uses)
    - [fastcgi_cache_path](#fastcgi_cache_path)
    - [fastcgi_cache_purge](#fastcgi_cache_purge)
    - [fastcgi_cache_revalidate](#fastcgi_cache_revalidate)
    - [fastcgi_cache_use_stale](#fastcgi_cache_use_stale)
    - [fastcgi_cache_valid](#fastcgi_cache_valid)
    - [fastcgi_catch_stderr](#fastcgi_catch_stderr)
    - [fastcgi_connect_timeout](#fastcgi_connect_timeout)
    - [fastcgi_force_ranges](#fastcgi_force_ranges)
    - [fastcgi_hide_header](#fastcgi_hide_header)
    - [fastcgi_ignore_client_abort](#fastcgi_ignore_client_abort)
    - [fastcgi_ignore_headers](#fastcgi_ignore_headers)
    - [fastcgi_index](#fastcgi_index)
    - [fastcgi_intercept_errors](#fastcgi_intercept_errors)
    - [fastcgi_keep_conn](#fastcgi_keep_conn)
    - [fastcgi_limit_rate](#fastcgi_limit_rate)
    - [fastcgi_max_temp_file_size](#fastcgi_max_temp_file_size)
    - [fastcgi_next_upstream](#fastcgi_next_upstream)
    - [fastcgi_next_upstream_timeout](#fastcgi_next_upstream_timeout)
    - [fastcgi_next_upstream_tries](#fastcgi_next_upstream_tries)
    - [fastcgi_no_cache](#fastcgi_no_cache)
    - [fastcgi_param](#fastcgi_param)
    - [fastcgi_pass](#fastcgi_pass)
    - [fastcgi_pass_header](#fastcgi_pass_header)
    - [fastcgi_pass_request_body](#fastcgi_pass_request_body)
    - [fastcgi_pass_request_headers](#fastcgi_pass_request_headers)
    - [fastcgi_read_timeout](#fastcgi_read_timeout)
    - [fastcgi_request_buffering](#fastcgi_request_buffering)
    - [fastcgi_send_lowat](#fastcgi_send_lowat)
    - [fastcgi_send_timeout](#fastcgi_send_timeout)
    - [fastcgi_split_path_info](#fastcgi_split_path_info)
    - [fastcgi_store](#fastcgi_store)
    - [fastcgi_store_access](#fastcgi_store_access)
    - [fastcgi_temp_file_write_size](#fastcgi_temp_file_write_size)
    - [fastcgi_temp_path](#fastcgi_temp_path)
- [传参到 FastCGI 服务器](#parameters)
- [内嵌变量](#embedded_variables)

`ngx_http_fastcgi_module` 模块允许将请求传递给 FastCGI 服务器。

<a id="example_configuration"></a>

## 示例配置

```nginx
location / {
    fastcgi_pass  localhost:9000;
    fastcgi_index index.php;

    fastcgi_param SCRIPT_FILENAME /home/www/scripts/php$fastcgi_script_name;
    fastcgi_param QUERY_STRING    $query_string;
    fastcgi_param REQUEST_METHOD  $request_method;
    fastcgi_param CONTENT_TYPE    $content_type;
    fastcgi_param CONTENT_LENGTH  $content_length;
}
```

<a id="directives"></a>

## 指令

### fastcgi_bind

|\-|说明|
|------:|------|
|**语法**|**fastcgi_bind** `address [transparent]` \| `off`;|
|**默认**|——|
|**上下文**|http、server、location|
|**提示**|该指令在 0.8.22 版本中出现|

通过一个可选的端口（1.11.2）从指定的本地 IP 地址发出到 FastCGI 服务器的传出连接。参数值可以包含变量（1.3.12）。特殊值 `off`（1.3.12）取消从上层配置级别继承到的 `fastcgi_bind` 指令作用，这允许系统自动分配本地 IP 地址和端口。

`transparent` 参数（1.11.0）允许从非本地 IP 地址（例如来自客户端的真实 IP 地址）的到 FastCGI 服务器的传出连接：

```nginx
fastcgi_bind $remote_addr transparent;
```

为了使这个参数起作用，有必要以超级用户权限运行 nginx 工作进程，并配置内核路由来拦截来自 FastCGI 服务器的网络流量。

### fastcgi_buffer_size

|\-|说明|
|------:|------|
|**语法**|**fastcgi_buffer_size** `size`;|
|**默认**|fastcgi_buffer_size 4k\|8k;|
|**上下文**|http、server、location|

设置读取 FastCGI 服务器收到的响应的第一部分的缓冲区的 `size`（大小）。该部分通常包含一个小的响应头。默认情况下，缓冲区大小等于一个内存页。为 4K 或 8K，因平台而异。但是，它可以设置得更小。

### fastcgi_buffering

|\-|说明|
|------:|------|
|**语法**|**fastcgi_buffering** `on` \| `off`;|
|**默认**|fastcgi_buffering on;|
|**上下文**|http、server、location|
|**提示**|该指令在 1.5.6 版本中出现|

启用或禁用来自 FastCGI 服务器的响应缓冲。

当启用缓冲时，nginx 会尽可能快地收到接收来自 FastCGI 服务器的响应，并将其保存到由 [fastcgi_buffer_size](#fastcgi_buffer_size) 和 [fastcgi_buffers](#fastcgi_buffers) 指令设置的缓冲区中。如果内存放不下整个响应，响应的一部分可以保存到磁盘上的[临时文件](#fastcgi_temp_path)中。写入临时文件由 [fastcgi_max_temp_file_size ](#fastcgi_max_temp_file_size) 和 [fastcgi_temp_file_write_size](#fastcgi_temp_file_write_size) 指令控制。

当缓冲被禁用时，nginx 在收到响应时立即同步传递给客户端，不会尝试从 FastCGI 服务器读取整个响应。nginx 一次可以从服务器接收的最大数据量由 [fastcgi_buffer_size](#fastcgi_buffer_size) 指令设置。

通过在 `X-Accel-Buffering` 响应头字段中通过 `yes` 或 `no` 也可以启用或禁用缓冲。可以使用 [fastcgi_ignore_headers](#fastcgi_ignore_headers) 指令禁用此功能。

### fastcgi_buffes 

|\-|说明|
|------:|------|
|**语法**|**fastcgi_buffes** `number size`;|
|**默认**|fastcgi_buffers 8 4k\|8k;|
|**上下文**|http、server、location|

设置单个连接从 FastCGI 服务器读取响应的缓冲区的 `number` （数量）和 `size` （大小）。默认情况下，缓冲区大小等于一个内存页。为 4K 或 8K，因平台而异。

### fastcgi_busy_buffers_size

|\-|说明|
|------:|------|
|**语法**|**fastcgi_busy_buffers_size** `size`;|
|**默认**|fastcgi_busy_buffers_size 8k\|16k;|
|**上下文**|http、server、location|

当启用 FastCGI 服务器响应[缓冲](#fastcgi_buffering)时，限制缓冲区的总大小（`size`）在当响应尚未被完全读取时可向客户端发送响应。同时，其余的缓冲区可以用来读取响应，如果需要的话，缓冲部分响应到临时文件中。默认情况下，`size` 受 [fastcgi_buffer_size](#fastcgi_buffer_size) 和 [fastcgi_buffers](#fastcgi_buffers) 指令设置的两个缓冲区的大小限制。

### fastcgi_cache

|\-|说明|
|------:|------|
|**语法**|**fastcgi_cache** `zone` \| `off`;|
|**默认**|fastcgi_cache off;|
|**上下文**|http、server、location|

定义用于缓存的共享内存区域。同一个区域可以在几个地方使用。参数值可以包含变量（1.7.9）。`off` 参数将禁用从上级配置级别继承的缓存配置。

### fastcgi_cache_background_update

|\-|说明|
|------:|------|
|**语法**|**fastcgi_cache_background_update** `on` \| `off`;|
|**默认**|fastcgi_cache_background_update off;|
|**上下文**|http、server、location|
|**提示**|该指令在 1.11.10. 版本中出现|

允许启动后台子请求来更新过期的缓存项，而过时的缓存响应则返回给客户端。请注意，有必要在更新时[允许](#fastcgi_cache_use_stale_updating)使用陈旧的缓存响应。

### fastcgi_cache_bypass

|\-|说明|
|------:|------|
|**语法**|**fastcgi_cache_bypass** `string ...`;|
|**默认**|——|
|**上下文**|http、server、location|

定义不从缓存中获取响应的条件。如果字符串参数中有一个值不为空且不等于 `0`，则不会从缓存中获取响应：

```nginx
fastcgi_cache_bypass $cookie_nocache $arg_nocache$arg_comment;
fastcgi_cache_bypass $http_pragma    $http_authorization;
```

可以和 [fastcgi_no_cache](#fastcgi_no_cache) 指令一起使用。

### fastcgi_cache_key

|\-|说明|
|------:|------|
|**语法**|**fastcgi_cache_key** `string`;|
|**默认**|——|
|**上下文**|http、server、location|

为缓存定义一个 key，例如：

```nginx
fastcgi_cache_key localhost:9000$request_uri;
```

### fastcgi_cache_lock

|\-|说明|
|------:|------|
|**语法**|**fastcgi_cache_lock** `on` \| `off`;|
|**默认**|fastcgi_cache_lock off;|
|**上下文**|http、server、location|
|**提示**|该指令在 1.1.12 版本中出现|

当启用时，同一时间只允许一个请求通过将请求传递给 FastCGI 服务器来填充 [fastcgi_cache_key](#fastcgi_cache_key) 指令标识的新缓存元素。同一缓存元素的其他请求将等待响应出现在缓存中，或等待此元素的缓存锁释放，直到 [fastcgi_cache_lock_timeout](#fastcgi_cache_lock_timeout) 指令设置的时间。

### fastcgi_cache_lock_age

|\-|说明|
|------:|------|
|**语法**|**fastcgi_cache_lock_age** `time`;|
|**默认**|fastcgi_cache_lock_age 5s;|
|**上下文**|http、server、location|
|**提示**|该指令在 1.7.8 版本中出现|

如果传递给 FastCGI 服务器的最后一个请求填充新缓存元素没能在指定的 `time` 内完成，则可能会有其他另一个请求被传递给 FastCGI 服务器。

### fastcgi_cache_lock_timeout

|\-|说明|
|------:|------|
|**语法**|**fastcgi_cache_lock_timeout** `time`;|
|**默认**|fastcgi_cache_lock_timeout 5s;|
|**上下文**|http、server、location|
|**提示**|该指令在 1.1.12 版本中出现|

设置 [fastcgi_cache_lock](#fastcgi_cache_lock_timeout) 的超时时间。当时间到期时，请求将被传递给 FastCGI 服务器，但是，响应不会被缓存。

> 在 1.7.8 之前，响应可以被缓存。

### fastcgi_cache_max_range_offset

|\-|说明|
|------:|------|
|**语法**|**fastcgi_cache_max_range_offset** `number`;|
|**默认**|——|
|**上下文**|http、server、location|
|**提示**|该指令在 1.11.6 版本中出现|

为 byte-range 请求设置字节偏移量。如果 range 超出 `number`（偏移量），range 请求将被传递给 FastCGI 服务器，并且不会缓存响应。

**待续……**

## 原文档

[http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html](http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html)