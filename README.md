# Local Package, Pip Install OK, Import works on VENV, Import Fails on Docker

The code in the following repo has the following problem:

- If you run  `pip install .` and `python -c "import your_project_name"` it works just fine

- If you do a `docker build . -t your_project_container` and `docker run --rm your_project_container` it fails, see error message.

## Error Message

```
2024-01-13 09:38:21 Traceback (most recent call last):
2024-01-13 09:38:21   File "/usr/lib64/python3.8/runpy.py", line 194, in _run_module_as_main
2024-01-13 09:38:21     return _run_code(code, main_globals, None,
2024-01-13 09:38:21   File "/usr/lib64/python3.8/runpy.py", line 87, in _run_code
2024-01-13 09:38:21     exec(code, run_globals)
2024-01-13 09:38:21   File "/opt/venv/lib/python3.8/site-packages/uvicorn/__main__.py", line 4, in <module>
2024-01-13 09:38:21     uvicorn.main()
2024-01-13 09:38:21   File "/opt/venv/lib/python3.8/site-packages/click/core.py", line 1157, in __call__
2024-01-13 09:38:21     return self.main(*args, **kwargs)
2024-01-13 09:38:21   File "/opt/venv/lib/python3.8/site-packages/click/core.py", line 1078, in main
2024-01-13 09:38:21     rv = self.invoke(ctx)
2024-01-13 09:38:21   File "/opt/venv/lib/python3.8/site-packages/click/core.py", line 1434, in invoke
2024-01-13 09:38:21     return ctx.invoke(self.callback, **ctx.params)
2024-01-13 09:38:21   File "/opt/venv/lib/python3.8/site-packages/click/core.py", line 783, in invoke
2024-01-13 09:38:21     return __callback(*args, **kwargs)
2024-01-13 09:38:21   File "/opt/venv/lib/python3.8/site-packages/uvicorn/main.py", line 416, in main
2024-01-13 09:38:21     run(
2024-01-13 09:38:21   File "/opt/venv/lib/python3.8/site-packages/uvicorn/main.py", line 587, in run
2024-01-13 09:38:21     server.run()
2024-01-13 09:38:21   File "/opt/venv/lib/python3.8/site-packages/uvicorn/server.py", line 61, in run
2024-01-13 09:38:21     return asyncio.run(self.serve(sockets=sockets))
2024-01-13 09:38:21   File "/usr/lib64/python3.8/asyncio/runners.py", line 44, in run
2024-01-13 09:38:21     return loop.run_until_complete(main)
2024-01-13 09:38:21   File "/usr/lib64/python3.8/asyncio/base_events.py", line 616, in run_until_complete
2024-01-13 09:38:21     return future.result()
2024-01-13 09:38:21   File "/opt/venv/lib/python3.8/site-packages/uvicorn/server.py", line 68, in serve
2024-01-13 09:38:21     config.load()
2024-01-13 09:38:21   File "/opt/venv/lib/python3.8/site-packages/uvicorn/config.py", line 467, in load
2024-01-13 09:38:21     self.loaded_app = import_from_string(self.app)
2024-01-13 09:38:21   File "/opt/venv/lib/python3.8/site-packages/uvicorn/importer.py", line 24, in import_from_string
2024-01-13 09:38:21     raise exc from None
2024-01-13 09:38:21   File "/opt/venv/lib/python3.8/site-packages/uvicorn/importer.py", line 21, in import_from_string
2024-01-13 09:38:21     module = importlib.import_module(module_str)
2024-01-13 09:38:21   File "/usr/lib64/python3.8/importlib/__init__.py", line 127, in import_module
2024-01-13 09:38:21     return _bootstrap._gcd_import(name[level:], package, level)
2024-01-13 09:38:21   File "<frozen importlib._bootstrap>", line 1014, in _gcd_import
2024-01-13 09:38:21   File "<frozen importlib._bootstrap>", line 991, in _find_and_load
2024-01-13 09:38:21   File "<frozen importlib._bootstrap>", line 961, in _find_and_load_unlocked
2024-01-13 09:38:21   File "<frozen importlib._bootstrap>", line 219, in _call_with_frames_removed
2024-01-13 09:38:21   File "<frozen importlib._bootstrap>", line 1014, in _gcd_import
2024-01-13 09:38:21   File "<frozen importlib._bootstrap>", line 991, in _find_and_load
2024-01-13 09:38:21   File "<frozen importlib._bootstrap>", line 973, in _find_and_load_unlocked
2024-01-13 09:38:21 ModuleNotFoundError: No module named 'your_package_name'
```

## Relevant Environment Details

The following code in the Dockerfile `CMD`:

```
echo $PYTHONPATH
python -c "import sys; print(sys.path)"
python -c "import sysconfig; print(sysconfig.get_paths()['purelib'])"
```

results in the following **echo**:
```
2024-01-13 09:38:20 /opt/venv/lib/python3.8/site-packages:/opt/venv/lib64/python3.8/site-packages:
2024-01-13 09:38:20 /opt/venv/lib/python3.8/site-packages
2024-01-13 09:38:20 ['', '/opt/venv/lib/python3.8/site-packages', '/opt/venv/lib64/python3.8/site-packages', '/var/api', '/usr/lib64/python38.zip', '/usr/lib64/python3.8', '/usr/lib64/python3.8/lib-dynload']
```
