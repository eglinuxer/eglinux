---
title: Lua
description: 上善若水，水善利万物而不争！
icon: simple/lua
---

# Lua

上善若水，水善利万物而不争！

``` shell
╭─eg@mac ~
╰─$ lua
Lua 5.4.6  Copyright (C) 1994-2023 Lua.org, PUC-Rio
> print("Hello, world")
Hello, world
>
```

Lua 保留字：

``` lua
and     break   do      else    elseif
end     false   goto    for     function
if      in      local   nil     not
or      repeat  return  then    true
until   while
```

Lua 基本类型：

``` shell
╭─eg@mac ~
╰─$ lua
Lua 5.4.6  Copyright (C) 1994-2023 Lua.org, PUC-Rio
> type(nil)
nil
> type(true)
boolean
> type(10.4 * 3)
number
> type("Hello world")
string
> type(io.stdin)
userdata
> type(print)
function
> type(type)
function
> type({})
table
> type(type(X))
string
>
```