基本步骤：
1. 创建工程文件夹；
2. 安装Sphinx，pip install Sphinx；
3. 配置文档工程，创建文档，make html；
4. 登陆readthedocs.org，导入github的repo（自己先提前建好空的repo，用于这个项目）；
5. 注意python的配置文件路径要正确；
6. 在github中，将repo配置service hooks到readthedocs，激活这个服务；
7. 使用git将本地工程文档推送到github：make clean工程文件夹，git add，commit，push完成；
8. 然后到readthedocs查看更新的结果，应该几分钟内就完成，如果创建失败，查看一些配置文件是否有问题。
