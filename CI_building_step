## 构建APP的CI的方法

 构建helloworld和httpclient复用了ArceOS的CI，做了如下修改：

##### **对于helloworld模块：**

**对于build.yml：**

删除了jobs中的clippy，删除了其它架构只保留了riscv64，删除了steps下的其它的构建文件的步骤只保留了build helloworld,最后修改了启动的cmd命令：

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
else
```

对net/httpclient的修改是类似的。



实际上test的方式很简单：

```makefile
test_one "LOG=info" "expect_info.out"
test_one "SMP=4 LOG=info" "expect_info_smp4.out"
```

将跑完之后的info输出与预期输出对比，一样就通过了。