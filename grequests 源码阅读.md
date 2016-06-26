grequests是一个requests的异步版本  
使用gevent重新封装了requests

# grequests.py  
所有的代码都集中在grequests文件中.  
grequests模块是使用gevent封装requests.api实现了一个异步版本的requests, 所有的API方法都返回一个Requests实例.  

```
from functools import partial
import traceback
try:
    import gevent
    from gevent import monkey as curious_george
    from gevent.pool import Pool
except ImportError:
    raise RuntimeError('Gevent is required for grequests.')
# Monkey-patch.
curious_george.patch_all(thread=False, select=False)
from requests import Session
__all__ = (
    'map', 'imap',
    'get', 'options', 'head', 'post', 'put', 'patch', 'delete', 'request'
)
```  
partial是偏函数的意思  
引入了相关的函数和类  

```  
class AsyncRequest(object):
    """ Asynchronous request.
    Accept same parameters as ``Session.request`` and some additional:

    :param session: Session which will do request
    :param callback: Callback called on response.
                     Same as passing ``hooks={'response': callback}``
    """
    def __init__(self, method, url, **kwargs):
        #: Request method
        self.method = method
        #: URL to request
        self.url = url
        #: Associated ``Session``
        self.session = kwargs.pop('session', None)
        if self.session is None:
            self.session = Session()

        callback = kwargs.pop('callback', None)
        if callback:
            kwargs['hooks'] = {'response': callback}

        #: The rest arguments for ``Session.request``
        self.kwargs = kwargs
        #: Resulting ``Response``
        self.response = None

    def send(self, **kwargs):
        """
        Prepares request based on parameter passed to constructor and optional ``kwargs```.
        Then sends request and saves response to :attr:`response`

        :returns: ``Response``
        """
        merged_kwargs = {}
        merged_kwargs.update(self.kwargs)
        merged_kwargs.update(kwargs)
        try:
            self.response = self.session.request(self.method,
                                                 self.url, **merged_kwargs)
        except Exception as e:
            self.exception = e
            self.traceback = traceback.format_exc()
        return self
```  
__ini__: 用于初始化  
send: 使用session.request方法发送一个请求  

```
def send(r, pool=None, stream=False):
    """
    Sends the request object using the specified pool. If a pool isn't
    specified this method blocks. Pools are useful because you can specify size
    and can hence limit concurrency.
    """
    if pool is not None:
        return pool.spawn(r.send, stream=stream)
    return gevent.spawn(r.send, stream=stream)
```  
r是AsyncRequest对象
以send为参数在pool添加一个Greenlet对象  
send返回一个Greenlet对象  
```
# Shortcuts for creating AsyncRequest with appropriate HTTP method
get = partial(AsyncRequest, 'GET')
options = partial(AsyncRequest, 'OPTIONS')
head = partial(AsyncRequest, 'HEAD')
post = partial(AsyncRequest, 'POST')
put = partial(AsyncRequest, 'PUT')
patch = partial(AsyncRequest, 'PATCH')
delete = partial(AsyncRequest, 'DELETE')
```  
使用partial创建相应的方法  
get = AsyncRequest(method='GET')
使用:  
get(**kwargs)  

```  
# synonym
def request(method, url, **kwargs):
    return AsyncRequest(method, url, **kwargs)
```  
request方法  

```  
def map(requests, stream=False, size=None, exception_handler=None, gtimeout=None):
    """
    Concurrently converts a list of Requests to Responses.
    :param requests: a collection of Request objects.
    :param stream: If True, the content will not be downloaded immediately.
    :param size: Specifies the number of requests to make at a time. If None, no throttling occurs.
    :param exception_handler: Callback function, called when exception occured. Params: Request, Exception
    :param gtimeout: Gevent joinall timeout in seconds. (Note: unrelated to requests timeout)
    """
    requests = list(requests)
    pool = Pool(size) if size else None
    jobs = [send(r, pool, stream=stream) for r in requests]
    gevent.joinall(jobs, timeout=gtimeout)
    ret = []
    for request in requests:
        if request.response is not None:
            ret.append(request.response)
        elif exception_handler and hasattr(request, 'exception'):
            ret.append(exception_handler(request, request.exception))
        else:
            ret.append(None)
    return ret
```  
创建Greenlet列表并且异步执行返回一个响应列表  

```  
def imap(requests, stream=False, size=2, exception_handler=None):
    """
    Concurrently converts a generator object of Requests to a generator of Responses.
    :param requests: a generator of Request objects.
    :param stream: If True, the content will not be downloaded immediately.
    :param size: Specifies the number of requests to make at a time. default is 2
    :param exception_handler: Callback function, called when exception occured. Params: Request, Exception
    """
    pool = Pool(size)
    def send(r):
        return r.send(stream=stream)
    for request in pool.imap_unordered(send, requests):
        if request.response is not None:
            yield request.response
        elif exception_handler:
            exception_handler(request, request.exception)
    pool.join()
```  
生成器对象,不是很明白  
