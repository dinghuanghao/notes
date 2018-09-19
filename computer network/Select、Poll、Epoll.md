# Select、Poll、Epoll

## Select

```python
while True:
	fd_sets = xxx
	select(fd_sets, xxx)
	for fd in fd_sets:
		xxx
```

+ 每次调用select，会将fd_sets从用户态拷贝到内核态
+ 内核定期轮训fd_sets的状态（遍历fd_sets）
+ 内核返回fd_sets相关的状态给用户态



问题：

+ 每次都会拷贝fd_sets，在频繁IO过程中，会存在大量的拷贝动作
+ fd_sets很大时，遍历的效率很低
+ 最大只支持1024个fd（可以修改内核配置，并重新编译，但是select在fd大了之后性能很低）



## Poll

与select差别不大，仅仅是改变了fd_sets的结构



## Epoll

epoll_create()

epoll_ctl()

epoll_wait()

```python
epoll_create()
epoll_ctl()

while True:
	epoll_wait()
	xxx
```



解决select的三个问题：

+ 仅仅在epoll_ctl()函数中拷贝fd_sets到内核，而在epoll_wait()中则不会再拷贝
+ 设备收到消息后，调用每个fd的回调函数，将对应fd加入就绪队列，这样内核每次只需要判断就绪队列是否为空，而不需要轮训
+ epoll不限制fd的数量