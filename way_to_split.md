## ArceOS模块拆分方法

​	ArceOS模块拆分的第一个目标是把ArceOS对应的所有模块都保存到远程仓库中，本地除了启动模块和必要的工具之外其它所有的模块都从git上获取，其基本步骤为：

**1、绘制各模块的依赖图**

​    安装depgraph工具后执行命令：

```shell
cargo depgraph --workspace-only | dot -Tpng > graph.png
```

​    模块依赖图会保存在根目录下的graph.png图片中。

**2、依据模块依赖图，先拆分当前只有指入边没有指出边的模块，这样的模块不依赖本地的任何其它模块，符合拆分的条件了，等拆分完所有符合条件的模块后，再重复生成模块依赖图，重复步骤2即可。**

**3、模块的拆分方式分为以下几步：**

​      1）将该模块上传到git仓库中并删除本地该模块；

​       2）对ArceOS中所有直接依赖该模块的模块的cargo.toml文件进行修改，把本地path依赖修改为git依赖，git的目标链接为https形式的git仓库地址。

​     3）若该模块包含build.rs及与build有关的makefile文件，则需要对与文件路径相关的语句进行修改，因为当新的用户以cargo包的方式下载该模块时，各模块间的相对位置发生了变化，各模块构建的文件（如链接脚本等）以原有的相对路径无法访问，会出现bug。解决方法是传入一个代表根目录的环境变量CURRENT_DIR，构建和访问文件的路径均以CURRENT_DIR为基准进行，即可解决这个问题。



**一些小trick**

​	如果发现某模块从本地删除后会产生无法解决的玄学bug，想绕过它先去拆分后面的模块，可以选择对该模块先增加patch，将丢github链接的访问转化为对本地的访问，这样至少在本地测试的时候，可以无脑将该绕过的模块用git的方式调用。等到该模块的bug解决，直接将本模块真正拆分也不会影响到已经拆分好的依赖它的模块的正确执行。





