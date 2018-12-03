Reference:

- [PyPi Packaging Tutorial](https://packaging.python.org/tutorials/packaging-projects/)
- [web3-gear](https://github.com/uldaman/web3-gear)

# 准备工作
- setup.py、setup.cfg、requirements、LICENSE、README.rst(注意不是 markdown)、MANIFEST.in 等文件
- `https://pypi.org` 的账号
- `pip install --upgrade setuptools wheel twine`

# 打包/发布
```
python setup.py sdist bdist_wheel
twine upload --repository-url https://pypi.org/legacy/ dist/*
```

然后会提示输入 PyPI 的用户名和密码.

# 过程优化
上面的过程会有几个繁琐的地方:

- `--repository-url` 参数太长
- 每次需要输入用户名和密码
- 打包/发布前要删除掉之前的结果, 也就是 dist 目录

```
vim ~/.pypirc

[distutils]
index-servers =
    pypi

[pypi]
repository: https://upload.pypi.org/legacy/
username: ...
password: ...
```

此时, 发布命令变成: `twine upload -r pypi dist/*`, 并且无需输入用户名和密码.

配合 Makefile 文件会让整个过程更友好:

```
.PHONY: clean-pyc clean-build

help:
    @echo "clean - remove build artifacts & Python file artifacts"
    @echo "clean-build - remove build artifacts"
    @echo "clean-pyc - remove Python file artifacts"
    @echo "release - package and upload a release"
    @echo "sdist - package"

clean: clean-build clean-pyc

clean-build:
    rm -fr build/
    rm -fr dist/
    rm -fr *.egg-info

clean-pyc:
    find . -name '*.pyc' -exec rm -f {} +
    find . -name '*.pyo' -exec rm -f {} +
    find . -name '*~' -exec rm -f {} +

release: clean
    python setup.py sdist bdist_wheel
    twine upload -r pypi dist/*

sdist: clean
    python setup.py sdist bdist_wheel
    ls -l dist
```
