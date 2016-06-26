

## ReactiveX

### Rx介绍 

微软定义：函数库，利用可观察序列和LINQ风格查询操作符来编写异步和基于事件的程序

*Rx = Observables + LINQ + Schedulers*

Observables：异步数据流

LINQ 操作符：查询异步数据流

Schedulers：参数化异步数据流的并发处理



### Rx 模式

1.  观察者模式

    *   创建：方便地创建__事件__和__数据__流


    *   组合：查询式操作符和变换数据流
    *   监听：订阅任何可观察的数据流并执行并行操作

*函数式代码风格*

数据和事件序列，无需关注底层的实现

#### Observable 的优势

1.  Rx 的 Observable 模型让你可以像使用集合数据一样操作异步事件流，对异步事件流使用各种简单、可组合的操作
2.  Observable 是可组合(异步数据流)
3.  Observable 更加灵活(标量/序列/无穷，抽象)，异步双向 push
4.  Observable 无偏见（可以使用多种方式实现）



### Observable

1.  Observer 订阅 Observable，Observer 对 Observable 发射的数据或数据序列作出响应

2.  Subscribe 方法用于将观察者连接到 Observable，Observer 实现：

    *   onNext(T item)

        Observable 发送数据(item)

    *   onError(Exception ex) -- 通知

        解决 Observerable 异常，终止

    *   onComplete -- 通知

        最后 onNext 后调用

3.  冷/热

    Observable 什么时候开始发射数据序列

    *   Hot: While Created
    *   Cold: Wait For Subscribe

4.  允许使用操作符组合 Observable



###  Subject

__桥梁/代理__

同时充当 Observer(订阅多个Observable) 和 Observable(转发 Observer 数据流，发射新数据)

#### 种类

1.  AsyncSubject
    *   Subscribe => While Origin Observable Finish EMIT Last Value Of Origin Observable
        *   与何时开始 subscribe 无关
        *   异常不 emit 数据
2.  BehaviorSubject
    *   Subscribe => EMIT [default] / pre + current ->
        *   异常不 emit 数据
3.  PublishSubject
    *   Subscribe => EMIT after this moment
    *   一创建就开始发射数据
        *   异常不 emit 数据
4.  ReplaySubject
    *   Subscribe => ALL From Observable
        *   何时订阅无关
        *   不要再多线程中调用 on* 方法

### Scheduler

给 Observable 操作符链添加多线程功能，可以指定操作符(或者特定的 Observable)在特定的 Scheduler 上执行



## realTime.js

```javascript
angular.module('vc.service.realtime', [])
    .factory('RealtimeService', ['rx', 'trackJs', 'Config', 'UserService', 'UIService', '$rootScope', '$q', '$timeout',
        function (rx, trackJs, Config, UserService, UIService, $rootScope, $q, $timeout) {

            var connection = null;
            var connectionDeferred = $q.defer();
            var connectionObservable = new Rx.BehaviorSubject();

            // Connection 方法
            var Connection = function (session) {
                this.session = session;
                this.rtObject = null;
                this.messageSubjects = {};
                this.counters = {};
                // Subject 对象
                this.countObservable = new rx.ReplaySubject();
                this.newChatSubject = new rx.ReplaySubject();
                this.notificationSubject = new rx.ReplaySubject();
                this.totalCountSubject = new rx.BehaviorSubject();
                this.totalCount = 0;
                this.preventedUserId = null;
                // 对小助手的特殊处理
                this.roomIdMapToUserId = {
                    littleHelperRoom: 'littleHelper'
                };
            };

            // 释放
            Connection.prototype.dispose = function () {
                var self = this;
                _.forEach(this.messageSubjects, function (s) {
                    // messageSubject 对象取消订阅完成并执行 dispose
                    s.onCompleted();
                    s.dispose();
                });
                this.session = null;
                // 尝试关闭 rtObject
                try {
                    console.log('1st attempt to close rtObject...');
                    self.rtObject.close();
                } catch (e) {
                    console.error(e);
                    // 使用 trackJs 追踪异常情况
                    trackJs.track({err: e, poi: 'RealtimeService/startRealtime/close/1', rtObject: self.rtObject});
                    // 5s 后再次尝试关闭 rtObject
                    $timeout(function () {
                        try {
                            console.log('2nd attempt to close rtObject...');
                            self.rtObject.close();
                        } catch (e) {
                            console.error(e);
                            trackJs.track({
                                err: e,
                                poi: 'RealtimeService/startRealtime/close/2',
                                rtObject: self.rtObject
                            });
                            // 5s 后尝试关闭 rtObject
                            $timeout(function () {
                                try {
                                    console.log('3rd attempt to close rtObject...');
                                    self.rtObject.close();
                                } catch (e) {
                                    console.error(e);
                                    trackJs.track({
                                        err: e,
                                        poi: 'RealtimeService/startRealtime/close/3',
                                        rtObject: self.rtObject
                                    });
                                    console.log('can not close rtObject... something wrong...');
                                }
                            }, 5000);
                        }
                    }, 5000);
                }
                // reset
                this.rtObject = null;
                this.messageSubjects = {};
                this.counters = {};
                // Subject dispose 工作
                this.newChatSubject.onCompleted();
                this.newChatSubject.dispose();
                this.newChatSubject = null;
                this.notificationSubject.onCompleted();
                this.notificationSubject.dispose();
                this.notificationSubject = null;
                this.totalCountSubject.onCompleted();
                this.totalCountSubject.dispose();
                this.totalCountSubject = null;
                // 重置 totalCount
                this.totalCount = 0;
                // ?
                this.preventedUserId = null;
            };

            // Connection 对象的 setCounter 方法
            Connection.prototype.setCounter = function (userId, count) {
                var oldValue = this.counters[userId];
                // 如果计数器中存在 userId 的计数值
                // 综述减去 userId 的值
                // 设置/重置 userId 的值
                if (oldValue) {
                    this.totalCount -= oldValue;
                }
                this.counters[userId] = count;
                // 特定用户计数器 Subject 发射一个数据
                this.countObservable.onNext({userId: userId, unreadCount: count});
                // 重新设置 totalCount
                this.totalCount += count;
                // 总数计数器 Subject 发射一个数据
                this.totalCountSubject.onNext(this.totalCount);
            };

            // Connection 对象的 start 方法
            Connection.prototype.start = function () {
                $$setAlert('正在连接聊天服务器，请稍候...', true);
                var self = this;
                // LeanCloud 相关
                var $$lc = function (lcMessage) {
                    return {
                        roomId: lcMessage.cid,
                        from: lcMessage.fromPeerId,
                        message: lcMessage.msg.message,
                        category: lcMessage.msg.category,
                        data: lcMessage.msg.data,
                        createAt: lcMessage.timestamp,
                        _id: lcMessage.msg._id,
                        to: lcMessage.peerId,
                        isOffline: lcMessage.offline
                    };
                };

                // 调度
                var $$dispatch = function (msg) {
                    // 利用 roomId 找到 userId
                    var roomId = msg.roomId;
                    var userId = self.roomIdMapToUserId[roomId];
                    // 该消息是否需要通知
                    // preventedUserId 应该是根据是否正在聊天获得的
                    msg.shouldSkipNotification = (userId == self.preventedUserId);
                    // 设置
                    if (msg.from == userId && userId != self.preventedUserId) {
                        // 没有正在聊天的用户的计数器才需要增加
                        var newCount = (self.counters[userId] || 0) + 1;
                        self.setCounter(userId, newCount);
                    }
                    // 不是离线消息
                    if (!msg.isOffline) {
                        // messageSubject 中已经有该房间，发射一条消息
                        if (self.messageSubjects[roomId]) {
                            self.messageSubjects[roomId].onNext(msg);
                        }
                        // 使用 newChatSubject 发射一条消息
                        self.newChatSubject.onNext(msg);
                    }
                };

                // 通知调度
                var $$dispatchNotification = function (msg) {
                    // 是 message
                    if (msg && msg.action === "message") {
                        $$dispatch(msg.message);
                    } else {
                        // 不是 message 则发射一条新的 msg
                        self.notificationSubject.onNext(msg);
                    }
                };

                return $q(function (resolve, reject) {
                    try {
                        // 实时服务
                        // rtObject 是基于 LeanCloud 的实时服务对象
                        // LeanCloud API 还需要去了解
                        self.rtObject = AV.realtime({
                            appId: Config.LeanCloud.AppId,
                            clientId: self.session.userId,
                            region: Config.LeanCloud.Region,
                            secure: true
                        }, function () {
                            resolve(self);
                        });
                        // 监听 message
                        self.rtObject.on('message', function (msg) {
                            // 分发消息或者通知
                            if (msg.fromPeerId == 'liaoyuan') {
                                $$dispatchNotification(msg.msg);
                            } else {
                                $$dispatch($$lc(msg));
                            }
                        });
                        // 监听 reuse
                        // ?reuse 是什么意思
                        self.rtObject.on('reuse', function (e) {
                            console.error('REUSE!!!!!!!!!!!!!!!!!!!');
                            console.error(e);
                            trackJs.track({err: e, poi: 'RealtimeService/startRealtime/reuse'});
                        });
                        // 监听 error
                        self.rtObject.on('error', function (e) {
                            console.error(e);
                            // 异常情况的处理
                            trackJs.track({err: e, poi: 'RealtimeService/startRealtime/error'});
                            $$setAlert('到聊天服务的连接已中断，点击这里重新连接...', false, function () {
                                $$setAlert();
                                if (self.session) {
                                    return self.start();
                                } else {
                                    return UIService.alert('您的登录可能已经失效，请重新登录！');
                                }
                            });
                        });
                    } catch (e) {
                        console.log(e);
                        reject(e);
                        trackJs.track({err: e, poi: 'RealtimeService/startRealtime/catch'});
                    }
                }).finally(function (res) {
                    // 最终
                    $$setAlert();
                    return res;
                });
            };
          
			// Connection 的 getRoom 方法
            Connection.prototype.getRoom = function (userId) {
                var self = this;
                // 成员是 userId 和 当前登录用户
                var members = [userId, this.session.userId];
                // 给小助手发送消息
                return (userId == "littleHelper" ?
                        $q.when({
                            id: "littleHelperRoom",
                            join: function () {
                            },
                            send: function (data, callback) {
                                callback();
                            }
                        }) : $q(function (resolve, reject) {
                        // room 这个方法的作用是什么
                        self.rtObject.room({
                            members: members,
                            unique: true
                        }, resolve);
                    })
                ).then(function (room) {
                    console.log(room);
                    var roomId = room.id;
                    self.roomIdMapToUserId[roomId] = userId;
                    // 新建房间
                    if (!self.messageSubjects[roomId]) {
                        // 每个 Subject 的通知都会广播给所有已经订阅的观察者
                        self.messageSubjects[roomId] = new rx.Subject();
                    }
                    // LeanCloud API
                    room.join();
                    // 返回的 room 对象
                    return {
                        roomId: roomId,
                        send: function (msg, skipMessageOnNext) {
                            var deferred = $q.defer();
                            var data = {
                                message: msg.message,
                                category: msg.category,
                                data: msg.data,
                                _id: msg._id
                            };
                            // 封装了 LeanCloud 的API
                            room.send(data, function () {
                                msg.roomId = roomId;
                                if (!skipMessageOnNext) {
                                    // 发射一条 msg
                                    self.messageSubjects[roomId].onNext(msg);
                                }
                                // 给 newChatSubject 发射 msg, WHY?
                                self.newChatSubject.onNext(msg);
                                deferred.resolve(msg);
                            });
                            return deferred.promise;
                        },
                        observable: self.messageSubjects[roomId],
                        connection: self,
                        setCounter: function (count) {
                            self.setCounter(userId, count);
                        },
                        setPrevented: function () {
                            // 这个在下面
                            self.setPreventedUserId(userId);
                        },
                        unsetPrevented: function () {
                            self.setPreventedUserId();
                        },
                        // dispose 方法
                        dispose: function () {
                            // 如果有房间
                            if (self.messageSubjects[roomId]) {
                                self.messageSubjects[roomId].onCompleted();
                                self.messageSubjects[roomId].dispose();
                                delete self.messageSubjects[roomId];
                            }
                        }
                    };
                });
            };

            // Connection 的 设置用户被 prevent 方法
            Connection.prototype.setPreventedUserId = function (_preventedUserId) {
                this.preventedUserId = _preventedUserId;
            };
   			// Connection 的获取 newChatSubject 的方法
            Connection.prototype.getNewChatObservable = function () {
                return this.newChatSubject;
            };
			// Connection 的获取 TotalCountSubject 的方法
            Connection.prototype.getTotalCountObservable = function () {
                return this.totalCountSubject;
            };
			// Connection 的获取 countSubject 的方法
            Connection.prototype.getCountObservable = function () {
                return this.countObservable;
            };
			// Connection 的获取 notificationSubject 的方法
            Connection.prototype.getNotificationObservable = function () {
                return this.notificationSubject;
            };

            // alert
            var $$setAlert = function (message, isLoading, onClick) {
                if (message) {
                    $rootScope.chatAlert = {
                        message: message,
                        onClick: onClick,
                        isLoading: isLoading
                    };
                } else {
                    delete $rootScope.chatAlert;
                }
            };
			
            // 使用 getSessionObservable 订阅 session 
            // 每当用户登录时就维持一个用户的 Connection
            UserService.getSessionObservable().subscribe(function (_session) {
                var _connection;
                // 新建 connection
                if (!connection) {
                    // 存在 session 是创建新的 Connection 对象
                    if (_session) {
                        _connection = new Connection(_session);
                        // 同时 start 这个 Connection 对象
                        return _connection.start().then(function () {
                            connection = _connection;
                            // 对于 deferred 还是有点疑惑
                            connectionDeferred.resolve(connection);
                            connectionObservable.onNext(connection);
                        });
                    }
                } else {
                    // 没有 session 但是却有 connection
                    if (!_session) {
                        connection.dispose();
                        connection = null;
                    } else {
                        // 判断当前存在的 connection 是否属于当前用户
                        if (connection.session && connection.session.userId == _session.userId) {

                        } else {
                            connection.dispose();
                            _connection = new Connection(_session);
                            return _connection.start().then(function () {
                                connection = _connection;
                                connectionObservable.onNext(connection);
                            });
                        }
                    }
                }
            });

            return {
                $apply: function () {
                    return $rootScope.$safeApply();
                },

                // 获取当前 Connection
                getCurrentConnection: function () {
                    if (connection) {
                        return $q.when(connection);
                    } else {
                        return connectionDeferred.promise;
                    }
                },

                // 获取 ConnectionObservable
                getConnectionObservable: function () {
                    return connectionObservable;
                },

                // 获取 totalCountObservable
                // 不是很明白为什么要这样返回
                getTotalCountObservable: function () {
                    // flatMap
                    // 将 Observable 发射的数据(s)按照一定规则变为多个 Observerable
                    return connectionObservable.flatMap(function (connection) {
                        // connection 存在 totalCountObservable 存在
                        return connection && connection.getTotalCountObservable() || $q.when();
                    });
                },

                // 同上
                getNotificationObservable: function () {
                    return connectionObservable.flatMap(function (connection) {
                        return connection && connection.getNotificationObservable() || $q.when();
                    });
                },

                // 同上
                getNewChatObservable: function () {
                    return connectionObservable.flatMap(function (connection) {
                        return connection && connection.getNewChatObservable() || $q.when();
                    });
                },

                // 设置 counter
                setCounter: function (userId, count) {
                    return connection && connection.setCounter(userId, count);
                }
            };
        }
    ])
;
```

可以再看一下 Conversation.js (在 content 里的那个)