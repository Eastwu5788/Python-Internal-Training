
**Python第三方包制作教程**  本教程将介绍如何制作一个可以使用`pip`命令安装的第三方包，并将制作好的包上传到PyPi，本教程仅用作内部学习参考，如有错误欢迎批评指正。

### 文件结构介绍
```
|--smart
|  |--static
|  |  |--icon.svg
|  |  |--confg.json
|  |--engine
|  |  |--__init__.py
|  |  |--core.py
|  |--__init__.py
|  |--__version__.py
|  |--api.py
|  |--utils.py
|--tests
|  |--__init__.py
|--LICENSE
|--README.rst
|--setup.py
```
smart: 作为项目核心代码模块，提供所有的对外接口和实现。其内部可以包含子模块和静态文件
tests: 包含所有的测试用例
LICENSE: 编写相关版权信息
README: 提供项目基本描述和相关使用方法介绍等
setup.py: 项目的安装配置文件

### setup.py 安装配置介绍
setup.py 主要使用setuptools的setup模块，提供打包所需要的基本信息。python依赖此脚本中的配置信息，将相关模块、静态文件，打包成一个完整的模块安装到site-packages文件。

基本示例：
```
# 需要将那些包导入
packages = ["smart", "smart.engine"]

# 导入静态文件
file_data = [
    ("smart/static", ["smart/static/icon.svg", "smart/static/config.json"]),
]

# 第三方依赖
requires = [
    "pandas>=0.23.4"
]

# 自动读取version信息
about = {}
with open(os.path.join(here, 'smart', '__version__.py'), 'r', 'utf-8') as f:
    exec(f.read(), about)

# 自动读取readme
with open('README.rst', 'r', 'utf-8') as f:
    readme = f.read()

setup(
    name=about["__title__"],  # 包名称
    version=about["__version__"],  # 包版本
    description=about["__description__"],  # 包详细描述
    long_description=readme,   # 长描述，通常是readme，打包到PiPy需要
    author=about["__author__"],  # 作者名称
    author_email=about["__author_email__"],  # 作者邮箱
    url=about["__url__"],   # 项目官网
    packages=packages,    # 项目需要的包
    data_files=file_data,   # 打包时需要打包的数据文件，如图片，配置文件等
    include_package_data=True,  # 是否需要导入静态数据文件
    python_requires=">=3.0, !=3.0.*, !=3.1.*, !=3.2.*, !=3.3*",  # Python版本依赖
    install_requires=requires,  # 第三方库依赖
    zip_safe=False,  # 此项需要，否则卸载时报windows error
    classifiers=[    # 程序的所属分类列表
        'Development Status :: 5 - Production/Stable',
        'Intended Audience :: Developers',
        'Natural Language :: English',
        'Programming Language :: Python',
        'Programming Language :: Python :: 3',
        'Programming Language :: Python :: 3.4',
        'Programming Language :: Python :: 3.5',
        'Programming Language :: Python :: 3.6',
        'Programming Language :: Python :: 3.7',
        'Programming Language :: Python :: Implementation :: CPython',
        'Programming Language :: Python :: Implementation :: PyPy'
    ],
)
```

### 源码安装
编写好源码和配置好setup.py脚本后，就可以使用`python`将当前模块安装到第三方库中
```
python setup.py install
```
但是此时的代码并不能使用`pip`进行安装。如果需要支持`pip`安装的方式，则需要对当前源码进行打包。

### 打包代码
打包之前，可以先验证setup.py的正确性
```
python setup.py check
```
如果没有任何错误或者警告，则说明您的setup.py是没问题的
如果没有问题，就可以使用下方的命令正式打包
```
python setup.py sdist
```
打包完成后，会在顶层目录下生产dist和egg两个目录

### 打包whl文件
如果您的代码包仅供内部使用，又不想直接发送源码，则可以将代码打包成whl文件
打包成whl命令
```
# --wheel-dir: 为打包存储的路径 
# 空格后为需要打包的工程路径
pip wheel --wheel-dir=D:\\work\\base_package\\dist D:\\work\\base_package
```
打包完成后就可以看到`smart-0.0.1-py3-none-any.whl`文件了，将此文件发送给有需要的同事后，对方就可以使用
```
pip install smart-0.0.1-py3-none-any.whl
```
来安装当前库了

### 上传代码到PyPi
如果您希望将代码包放到网络上，供所有人开放下载，可以将代码打包上传至PyPi。
上传代码之前，需要到官网注册pypi账户。[https://pypi.org/](https://pypi.org/)

####  直接上传
使用`register`命令是最简单的上传方式，但是使用HTTP并未加密，有可能会泄露密码。
```
# 注册包
python setup.py register
# 上传包
python setup.py sdist upload
```
#### 使用twine上传
1. 安装twine
```
pip install twine
```
2. 使用twine注册并上传代码
```
# 注册包
twine register dist/smart.whl
# 上传包
twine upload dist/*
```
