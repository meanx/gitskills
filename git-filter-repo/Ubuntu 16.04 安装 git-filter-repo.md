# Ubuntu 16.04 安装 git-filter-repo

本文档记录在 Ubuntu 16.04 中安装 git-filter-repo 遇到的问题。



## 一、安装失败

首先，安装 pip
``` shell
:~/Zzzz/git-test/git-filter-repo/ITU-ALL$ pip install git-filter-repo
程序“pip”尚未安装。 您可以使用以下命令安装：
sudo apt install python-pip
:~/Zzzz/git-test/git-filter-repo/ITU-ALL$ sudo apt install python-pip
[sudo] xcyyj 的密码： 
正在读取软件包列表... 完成
正在分析软件包的依赖关系树       
正在读取状态信息... 完成       
推荐安装：
  python-all-dev python-setuptools python-wheel
下列【新】软件包将被安装：
  python-pip
升级了 0 个软件包，新安装了 1 个软件包，要卸载 0 个软件包，有 0 个软件包未被升级。
需要下载 144 kB 的归档。
解压缩后会消耗 635 kB 的额外空间。
获取:1 http://security.ubuntu.com/ubuntu xenial-security/universe amd64 python-pip all 8.1.1-2ubuntu0.6 [144 kB]
已下载 144 kB，耗时 1秒 (101 kB/s)   
正在选中未选择的软件包 python-pip。
(正在读取数据库 ... 系统当前共安装有 230092 个文件和目录。)
正准备解包 .../python-pip_8.1.1-2ubuntu0.6_all.deb  ...
正在解包 python-pip (8.1.1-2ubuntu0.6) ...
正在处理用于 man-db (2.7.5-1) 的触发器 ...
正在设置 python-pip (8.1.1-2ubuntu0.6) ...
```
接着，安装 git-filter-repo
``` shell
:~/Zzzz/git-test/git-filter-repo/ITU-ALL$ pip install git-filter-repo
Collecting git-filter-repo
  Downloading https://files.pythonhosted.org/packages/61/17/4077f08f2ebc4356181780dee50a8c3b9d1a1a0f1638b5d4afae41781148/git_filter_repo-2.45.0.tar.gz (226kB)
    27% |████████▊                       | 61kB 1.8kB/s eta 0:01:33Exception:
Traceback (most recent call last):
  File "/usr/lib/python2.7/dist-packages/pip/basecommand.py", line 209, in main
    status = self.run(options, args)
  File "/usr/lib/python2.7/dist-packages/pip/commands/install.py", line 328, in run
    wb.build(autobuilding=True)
  File "/usr/lib/python2.7/dist-packages/pip/wheel.py", line 748, in build
    self.requirement_set.prepare_files(self.finder)
  File "/usr/lib/python2.7/dist-packages/pip/req/req_set.py", line 360, in prepare_files
    ignore_dependencies=self.ignore_dependencies))
  File "/usr/lib/python2.7/dist-packages/pip/req/req_set.py", line 577, in _prepare_file
    session=self.session, hashes=hashes)
  File "/usr/lib/python2.7/dist-packages/pip/download.py", line 810, in unpack_url
    hashes=hashes
  File "/usr/lib/python2.7/dist-packages/pip/download.py", line 649, in unpack_http_url
    hashes)
  File "/usr/lib/python2.7/dist-packages/pip/download.py", line 871, in _download_http_url
    _download_url(resp, link, content_file, hashes)
  File "/usr/lib/python2.7/dist-packages/pip/download.py", line 595, in _download_url
    hashes.check_against_chunks(downloaded_chunks)
  File "/usr/lib/python2.7/dist-packages/pip/utils/hashes.py", line 46, in check_against_chunks
    for chunk in chunks:
  File "/usr/lib/python2.7/dist-packages/pip/download.py", line 563, in written_chunks
    for chunk in chunks:
  File "/usr/lib/python2.7/dist-packages/pip/utils/ui.py", line 139, in iter
    for x in it:
  File "/usr/lib/python2.7/dist-packages/pip/download.py", line 552, in resp_read
    decode_content=False):
  File "/usr/share/python-wheels/urllib3-1.13.1-py2.py3-none-any.whl/urllib3/response.py", line 344, in stream
    data = self.read(amt=amt, decode_content=decode_content)
  File "/usr/share/python-wheels/urllib3-1.13.1-py2.py3-none-any.whl/urllib3/response.py", line 311, in read
    flush_decoder = True
  File "/usr/lib/python2.7/contextlib.py", line 35, in __exit__
    self.gen.throw(type, value, traceback)
  File "/usr/share/python-wheels/urllib3-1.13.1-py2.py3-none-any.whl/urllib3/response.py", line 240, in _error_catcher
    raise ReadTimeoutError(self._pool, None, 'Read timed out.')
ReadTimeoutError: HTTPSConnectionPool(host='files.pythonhosted.org', port=443): Read timed out.
```
似乎出现 https 网络连接失败，继续尝试安装 git-filter-repo
```shell
:~/Zzzz/git-test/git-filter-repo/ITU-ALL$ pip install git-filter-repo
Collecting git-filter-repo
Exception:
Traceback (most recent call last):
  File "/usr/lib/python2.7/dist-packages/pip/basecommand.py", line 209, in main
    status = self.run(options, args)
  File "/usr/lib/python2.7/dist-packages/pip/commands/install.py", line 328, in run
    wb.build(autobuilding=True)
  File "/usr/lib/python2.7/dist-packages/pip/wheel.py", line 748, in build
    self.requirement_set.prepare_files(self.finder)
  File "/usr/lib/python2.7/dist-packages/pip/req/req_set.py", line 360, in prepare_files
    ignore_dependencies=self.ignore_dependencies))
  File "/usr/lib/python2.7/dist-packages/pip/req/req_set.py", line 577, in _prepare_file
    session=self.session, hashes=hashes)
  File "/usr/lib/python2.7/dist-packages/pip/download.py", line 810, in unpack_url
    hashes=hashes
  File "/usr/lib/python2.7/dist-packages/pip/download.py", line 649, in unpack_http_url
    hashes)
  File "/usr/lib/python2.7/dist-packages/pip/download.py", line 842, in _download_http_url
    stream=True,
  File "/usr/share/python-wheels/requests-2.9.1-py2.py3-none-any.whl/requests/sessions.py", line 492, in get
    return self.request('GET', url, **kwargs)
  File "/usr/lib/python2.7/dist-packages/pip/download.py", line 378, in request
    return super(PipSession, self).request(method, url, *args, **kwargs)
  File "/usr/share/python-wheels/requests-2.9.1-py2.py3-none-any.whl/requests/sessions.py", line 480, in request
    resp = self.send(prep, **send_kwargs)
  File "/usr/share/python-wheels/requests-2.9.1-py2.py3-none-any.whl/requests/sessions.py", line 588, in send
    r = adapter.send(request, **kwargs)
  File "/usr/share/python-wheels/CacheControl-0.11.5-py2.py3-none-any.whl/cachecontrol/adapter.py", line 46, in send
    resp = super(CacheControlAdapter, self).send(request, **kw)
  File "/usr/share/python-wheels/requests-2.9.1-py2.py3-none-any.whl/requests/adapters.py", line 376, in send
    timeout=timeout
  File "/usr/share/python-wheels/urllib3-1.13.1-py2.py3-none-any.whl/urllib3/connectionpool.py", line 610, in urlopen
    _stacktrace=sys.exc_info()[2])
  File "/usr/share/python-wheels/urllib3-1.13.1-py2.py3-none-any.whl/urllib3/util/retry.py", line 238, in increment
    total -= 1
TypeError: unsupported operand type(s) for -=: 'Retry' and 'int'
You are using pip version 8.1.1, however version 24.2 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
```
还是失败，但并不是 https 连接错误; 尝试更新 pip
```shell
:~/Zzzz/git-test/git-filter-repo/ITU-ALL$ pip install --upgrade pip
Collecting pip
Exception:
Traceback (most recent call last):
  File "/usr/lib/python2.7/dist-packages/pip/basecommand.py", line 209, in main
    status = self.run(options, args)
  File "/usr/lib/python2.7/dist-packages/pip/commands/install.py", line 328, in run
    wb.build(autobuilding=True)
  File "/usr/lib/python2.7/dist-packages/pip/wheel.py", line 748, in build
    self.requirement_set.prepare_files(self.finder)
  File "/usr/lib/python2.7/dist-packages/pip/req/req_set.py", line 360, in prepare_files
    ignore_dependencies=self.ignore_dependencies))
  File "/usr/lib/python2.7/dist-packages/pip/req/req_set.py", line 512, in _prepare_file
    finder, self.upgrade, require_hashes)
  File "/usr/lib/python2.7/dist-packages/pip/req/req_install.py", line 273, in populate_link
    self.link = finder.find_requirement(self, upgrade)
  File "/usr/lib/python2.7/dist-packages/pip/index.py", line 442, in find_requirement
    all_candidates = self.find_all_candidates(req.name)
  File "/usr/lib/python2.7/dist-packages/pip/index.py", line 400, in find_all_candidates
    for page in self._get_pages(url_locations, project_name):
  File "/usr/lib/python2.7/dist-packages/pip/index.py", line 545, in _get_pages
    page = self._get_page(location)
  File "/usr/lib/python2.7/dist-packages/pip/index.py", line 648, in _get_page
    return HTMLPage.get_page(link, session=self.session)
  File "/usr/lib/python2.7/dist-packages/pip/index.py", line 757, in get_page
    "Cache-Control": "max-age=600",
  File "/usr/share/python-wheels/requests-2.9.1-py2.py3-none-any.whl/requests/sessions.py", line 492, in get
    return self.request('GET', url, **kwargs)
  File "/usr/lib/python2.7/dist-packages/pip/download.py", line 378, in request
    return super(PipSession, self).request(method, url, *args, **kwargs)
  File "/usr/share/python-wheels/requests-2.9.1-py2.py3-none-any.whl/requests/sessions.py", line 480, in request
    resp = self.send(prep, **send_kwargs)
  File "/usr/share/python-wheels/requests-2.9.1-py2.py3-none-any.whl/requests/sessions.py", line 588, in send
    r = adapter.send(request, **kwargs)
  File "/usr/share/python-wheels/CacheControl-0.11.5-py2.py3-none-any.whl/cachecontrol/adapter.py", line 46, in send
    resp = super(CacheControlAdapter, self).send(request, **kw)
  File "/usr/share/python-wheels/requests-2.9.1-py2.py3-none-any.whl/requests/adapters.py", line 376, in send
    timeout=timeout
  File "/usr/share/python-wheels/urllib3-1.13.1-py2.py3-none-any.whl/urllib3/connectionpool.py", line 610, in urlopen
    _stacktrace=sys.exc_info()[2])
  File "/usr/share/python-wheels/urllib3-1.13.1-py2.py3-none-any.whl/urllib3/util/retry.py", line 238, in increment
    total -= 1
TypeError: unsupported operand type(s) for -=: 'Retry' and 'int'
You are using pip version 8.1.1, however version 24.2 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
```



## 二、原因分析

猜测可能是 Python2 和 Python3 版本的问题；

``` shell
:~/Zzzz/git_filter_repo-2.45.0$ python --version
Python 2.7.12
:~/Zzzz/git_filter_repo-2.45.0$ which python
/usr/bin/python
:~/Zzzz/git_filter_repo-2.45.0$ python3 --version
Python 3.5.2
:~/Zzzz/git_filter_repo-2.45.0$ which python3
/usr/bin/python3
:~/Zzzz/git_filter_repo-2.45.0$ pip --version
pip 8.1.1 from /usr/lib/python2.7/dist-packages (python 2.7)
:~/Zzzz/git_filter_repo-2.45.0$ pip3 --version
pip 8.1.1 from /usr/lib/python3/dist-packages (python 3.5)
```

于是，使用 python3 的 pip 安装 git-filter-repo

```shell
:~/Zzzz/git_filter_repo-2.45.0$ pip3 install git-filter-repo
Collecting git-filter-repo
  Downloading https://files.pythonhosted.org/packages/07/2f/949068bc92fbfe632c966b2c19e7979602752477bb729304572785c23c76/git_filter_repo-2.45.0-py3-none-any.whl (64kB)
    100% |████████████████████████████████| 71kB 18kB/s 
Installing collected packages: git-filter-repo
Successfully installed git-filter-repo-2.45.0
```

安装成功！
