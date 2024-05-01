## 构建APP的CI的方法

 构建helloworld和httpclient复用了ArceOS的CI，做了如下修改：

##### **对于helloworld模块：**

**对于build.yml：**

删除了其它架构只保留了riscv64，删除了steps下的其它的构建文件的步骤只保留了build helloworld,最后修改了启动的cmd命令：

```yml
    steps:
    - uses: actions/checkout@v3
    - uses: actions-rs/toolchain@v1
      with:
        profile: minimal
        toolchain: ${{ matrix.rust-toolchain }}
        components: rust-src
    - uses: actions-rs/install@v0.1
      with:
        crate: cargo-binutils
        version: latest
        use-tool-cache: true
    - name: Build helloworld
      run: CURRENT_DIR=`pwd` make ARCH=${{ matrix.arch }} A=apps/helloworld
```

修复了job clippy 的bug，因为调用了script/make/cargo.mk中的方法：

```

```

**对于test.yml：**

​     删除了matrix矩阵下arch中的其它架构只保留了riscv64，修改了run命令：

```yml
      run: |
        make disk_img
        CURRENT_DIR=`pwd` make test ARCH=${{ matrix.arch }}
```

  对make test指令进行了溯源和追踪修改：

```makefile
test:
	$(call app_test)
```

```makefile
define app_test
  $(CURDIR)/scripts/test/app_test.sh $(test_app)
endef
```

最后修改了app_test.sh中的test_list,只保留了helloworld:

```sh
if [ -z "$1" ]; then
    test_list=(
        "apps/helloworld"
    )
elseif [ -z "$1" ]; then
    test_list=(
        "apps/helloworld"
    )
else构建httpserver、udpserver和echoserver的难点和方法
```

对net/httpclient的修改是类似的。



实际上test的方式很简单：

```makefile
test_one "LOG=info" "expect_info.out"
test_one "SMP=4 LOG=info" "expect_info_smp4.out"
```

将跑完之后的info输出与预期输出对比，一样就通过了。



### 构建httpserver、udpserver和echoserver的难点和方法

​	这几个应用都是作为服务器等待客户机访问，一直不停机，不利于测试，并且需要其它主机访问才能测试其信息是否有问题。这里在测试机的ubuntu上创建了另一个线程等服务器创建好后发出ping，比对actual和expect的输出，同时，这里需要修改test.sh将time_out也视为一种可以进行比对的情况。



### 构建bwbench的难点和方法

​	难点在于有一个显示带宽的输出，而每次测试带宽大小都不一样，再加上每一次测试输出的行数也不一样，所以直接比对必然失败，这里修改比对脚本使用了折中方案，让每一行比对的信息大致相同（不同字符数量少于10个，表示速度的数字就是有10个）即算该行比对成功，最后也是可以顺利通过测试。

