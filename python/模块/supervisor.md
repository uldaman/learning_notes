# 1. Supervisor 介绍
Supervisor 是用 Python 实现的一款非常实用的进程管理工具, Supervisor 会帮你把应用程序转成 Daemon 程序, 而且可以方便的通过命令开启、关闭、重启等操作, 托管给它管理的进程一旦崩溃也会自动重启, 这样就可以保证程序执行中断后的情况下有自我修复的功能.

注意事项:

- 不支持任何版本的 **Windows**
- 支持 Python 2.4 +, 但**不**支持 **Python3**

Supervisor **特性**:

- 同时启动多个进程, 可以配置同时启动的进程数, 而不需要一个个启动
- 可以根据程序的退出码来判断是否需要自动重启
- 处理程序所产生的日志
- 提供管理进程(开始, 启动, 重启, 查看进程状态)的 web 界面, 和 xmlrpc 接口

Supervisor 由两部分**构成**:

- supervisord: Supervisor 服务
- supervisorctl: Supervisor 控制程序

> 简单的说就是先使用 **supervisord** 开始 Supervisor 服务, 然后通过 **supervisorctl** 管理 Supervisor 服务

# 2. 安装
```
pip install supervisor
```

可以安装在系统级的 Python 环境下, 也可以安装在 virtualenv 级的 Python 环境下, 建议安装在系统级的 Python 环境, 这样就可以把 supervisor 配成开机启动, 而且 supervisor 也算是通用扩展了...

# 3. 配置
安装完成后, 使用下面这两种方式创建配置文件:

- 创建一个全局配置文件 echo\_supervisord\_conf \> /etc/supervisord.conf
- 在当前目录创建配置文件 echo\_supervisord\_conf \> supervisord.conf, 然后使用 **\-c** 参数在启动时手动指定配置文件: `supervisord -c supervisord.conf`

默认配置文件 echo\_supervisord\_conf 内容:

```
; Sample supervisor config file.
;
; For more information on the config file, please see:
; http://supervisord.org/configuration.html
;
; Note: shell expansion ("~" or "$HOME") is not supported.  Environment
; variables can be expanded using this syntax: "%(ENV_HOME)s".

[unix_http_server]          ; supervisord 的 unix socket 服务配置
file=/tmp/supervisor.sock   ; socket 文件的保存目录
;chmod=0700                 ; socket 的文件权限 (default 0700)
;chown=nobody:nogroup       ; socket 的拥有者和组名
;username=user              ; 默认不需要登陆用户 (open server)
;password=123               ; 默认不需要登陆密码 (open server)

;[inet_http_server]         ; supervisord 的 tcp 服务配置
;port=127.0.0.1:9001        ; tcp 端口
;username=user              ; tcp 登陆用户
;password=123               ; tcp 登陆密码

[supervisord]                ; supervisord 的主进程配置
logfile=/tmp/supervisord.log ; 主要的进程日志配置
logfile_maxbytes=50MB        ; 最大日志体积, 默认 50MB
logfile_backups=10           ; 日志文件备份数目, 默认 10
loglevel=info                ; 日志级别, 默认 info; 还有: debug, warn, trace
pidfile=/tmp/supervisord.pid ; supervisord 的 pidfile 文件
nodaemon=false               ; 是否以守护进程的方式启动
minfds=1024                  ; 最小的有效文件描述符, 默认 1024
minprocs=200                 ; 最小的有效进程描述符, 默认 200
;umask=022                   ; 进程文件的 umask, 默认 200
;user=chrism                 ; 默认为当前用户, 如果为 root 则必填
;identifier=supervisor       ; supervisord 的表示符, 默认时 'supervisor'
;directory=/tmp              ; 在运行前 cwd 到指定的目录, 默认不执行 cmd
;nocleanup=true              ; 不在启动的时候清除临时文件, 默认 false
;childlogdir=/tmp            ; ('AUTO' child log dir, default $TEMP)
;environment=KEY=value       ; 初始键值对传递给进程
;strip_ansi=false            ; (strip ansi escape codes in logs; def. false)

; the below section must remain in the config file for RPC
; (supervisorctl/web interface) to work, additional interfaces may be
; added by defining them in separate rpcinterface: sections
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl=unix:///tmp/supervisor.sock ; use a unix:// URL  for a unix socket
;serverurl=http://127.0.0.1:9001 ; use an http:// url to specify an inet socket
;username=chris              ; 如果设置应该与 http_username 相同
;password=123                ; 如果设置应该与 http_password 相同
;prompt=mysupervisor         ; 命令行提示符, 默认 'supervisor'
;history_file=~/.sc_history  ; 命令行历史纪录

; The below sample program section shows all possible program subsection values,
; create one or more 'real' program: sections to be able to control them under
; supervisor.

;[program:theprogramname]
;command=/bin/cat              ; 运行的程序 (相对使用PATH路径, 可以使用参数)
;process_name=%(program_name)s ; 进程名表达式, 默认为 %(program_name)s
;numprocs=1                    ; 默认启动的进程数目, 默认为 1
;directory=/tmp                ; 在运行前 cwd 到指定的目录, 默认不执行 cmd
;umask=022                     ; 进程 umask, 默认 None
;priority=999                  ; 程序运行的优先级, 默认 999
;autostart=true                ; 默认随 supervisord 自动启动, 默认 true
;autorestart=unexpected        ; 被监控程序异常中断是否自动重启
;startsecs=1                   ; 被监控程序启动时持续时间 (def. 1)
;startretries=3                ; 被监控程序启动失败重试的次数 (default 3)
;exitcodes=0,2                 ; 期望的退出码, 默认 0, 2
;stopsignal=QUIT               ; 杀死进程的信号, 默认 TERM
;stopwaitsecs=10               ; max num secs to wait b4 SIGKILL (default 10)
;stopasgroup=false             ; 向 unix 进程组发送停止信号, 默认 false
;killasgroup=false             ; 向 unix 进程组发送SIGKILL信号, 默认 false
;user=chrism                   ; 为运行程序的 unix 帐号设置 setuid
;redirect_stderr=true          ; 将标准错误重定向到标准输出, 默认 false
;stdout_logfile=/a/path        ; 标准输出的文件路径 NONE＝none; 默认 AUTO
;stdout_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stdout_logfile_backups=10     ; # of stdout logfile backups (default 10)
;stdout_capture_maxbytes=1MB   ; number of bytes in 'capturemode' (default 0)
;stdout_events_enabled=false   ; emit events on stdout writes (default false)
;stderr_logfile=/a/path        ; stderr log path, NONE for none; default AUTO
;stderr_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stderr_logfile_backups=10     ; # of stderr logfile backups (default 10)
;stderr_capture_maxbytes=1MB   ; number of bytes in 'capturemode' (default 0)
;stderr_events_enabled=false   ; emit events on stderr writes (default false)
;environment=A=1,B=2           ; process environment additions (def no adds)
;serverurl=AUTO                ; override serverurl computation (childutils)

; The below sample eventlistener section shows all possible
; eventlistener subsection values, create one or more 'real'
; eventlistener: sections to be able to handle event notifications
; sent by supervisor.

;[eventlistener:theeventlistenername]
;command=/bin/eventlistener    ; 运行的程序 (相对使用 PATH 路径, 可以使用参数)
;process_name=%(program_name)s ; 进程名表达式, 默认为 %(program_name)s
;numprocs=1                    ; 默认启动的进程数目, 默认为 1
;events=EVENT                  ; event notif. types to subscribe to (req'd)
;buffer_size=10                ; 事件缓冲区队列大小, 默认 10
;directory=/tmp                ; 在运行前 cwd 到指定的目录, 默认不执行 cmd
;umask=022                     ; 进程umask, 默认 None
;priority=-1                   ; 程序运行的优先级, 默认 -1
;autostart=true                ; 默认随supervisord自动启动, 默认true
;autorestart=unexpected        ; whether/when to restart (default: unexpected)
;startsecs=1                   ; number of secs prog must stay running (def. 1)
;startretries=3                ; max # of serial start failures (default 3)
;exitcodes=0,2                 ; 期望的退出码, 默认 0,2
;stopsignal=QUIT               ; 杀死进程的信号, 默认 TERM
;stopwaitsecs=10               ; max num secs to wait b4 SIGKILL (default 10)
;stopasgroup=false             ; 向unix进程组发送停止信号, 默认false
;killasgroup=false             ; 向unix进程组发送SIGKILL信号, 默认false
;user=chrism                   ; setuid to this UNIX account to run the program
;redirect_stderr=true          ; redirect proc stderr to stdout (default false)
;stdout_logfile=/a/path        ; stdout log path, NONE for none; default AUTO
;stdout_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stdout_logfile_backups=10     ; # of stdout logfile backups (default 10)
;stdout_events_enabled=false   ; emit events on stdout writes (default false)
;stderr_logfile=/a/path        ; stderr log path, NONE for none; default AUTO
;stderr_logfile_maxbytes=1MB   ; max # logfile bytes b4 rotation (default 50MB)
;stderr_logfile_backups        ; # of stderr logfile backups (default 10)
;stderr_events_enabled=false   ; emit events on stderr writes (default false)
;environment=A=1,B=2           ; process environment additions
;serverurl=AUTO                ; override serverurl computation (childutils)

; The below sample group section shows all possible group values,
; create one or more 'real' group: sections to create "heterogeneous"
; process groups.

;[group:thegroupname]
;programs=progname1,progname2  ; 任何在[program:x]中定义的x
;priority=999                  ; 程序运行的优先级, 默认999

; The [include] section can just contain the "files" setting.  This
; setting can list multiple files (separated by whitespace or
; newlines).  It can also contain wildcards.  The filenames are
; interpreted as relative to this file.  Included files *cannot*
; include files themselves.

;[include]
;files = relative/directory/*.ini
```

# 4. 添加一个程序
编辑配置文件 supervisord.conf, 在后面添加新程序:

```
[program:test_py]
command=python /etc/my_pro/test.py
autorestart=true
directory=/etc/my_pro
stdout_logfile=/etc/my_pro/test_stdout.log
stderr_logfile=/etc/my_pro/test_stderr.log
```


> 后来发现在 python 中使用 print 时, test_stdout.log 文件里不输出内容, 原来 print 是输出到缓冲区, 在满足某些条件下才会从缓冲区写出, 使用 **\-u** 参数来停用缓冲区, 直接写文件: `python -u /etc/my_pro/test.py`

**注意!!**<br>
如果配置中的 command 是执行一个 bash, 然后在 bash 里执行了 python 脚本, 那么当使用 supervisor 停止程序时, 只有上层进程被停止(即 bash 被停止了), 但 python 进程没有被停止.

解决办法:<br>
在配置文件中设置:

```
stopasgroup=true
killasgroup=true
```

# 5. 使用 supervisor 管理程序
- 使用指定配置文件启动: supervisord -c supervisord.conf
- 使用默认配置文件启动: supervisord

常用命令:<br>
控制命令基本都通过 supervisorctl 执行, 输入 help 可以看到命令列表, 这是一些常用命令:

- 得到所有程序状态 supervisorctl status
- 关闭目标程序 supervisorctl stop spider
- 启动目标程序 supervisorctl start spider
- 关闭所有程序 supervisorctl shutdown
- 重载配置文件 supervisorctl reload

> 可以先键入 supervisorctl 进入交互状态, 再使用 status、start 等命令.

# 6. 通过 web 方式查看状态
配置文件中配置:

```
[inet_http_server]         ; inet (TCP) server disabled by default
port=127.0.0.1:9001        ; (ip_address:port specifier, *:port for all iface)
```

那么可以从浏览器通过访问 `127.0.0.1:9001` 来查看进程状态
