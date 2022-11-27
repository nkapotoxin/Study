## wasm

### 1 what is wasm(WebAssembly)

	wasm是一种运行在网络浏览器中的新型代码, 并且提供新的特性和效果.
	wasm的设计目标不是为了手写wasm代码, 而是为了C++/C和Rust等语言源语言提供一个高效的编译目标.
	wasm的模块可以被导入到一个网络app(例如node.js)中, 并暴露出提供JavaScript使用的WebAssembly函数.	
	wasm设计哲学/目标:
		1) 快速 高效 可移植
		2) 可读 可调式
		3) 保持安全
		4) 不破坏网络

### 2 what is emscripten

	emscripten是一个wasm的compiler toolchain, 通过emscripten可以移植c/c++程序到wasm.
	emcc(Emscripten Compiler Frontend)是用于call the emscripten compiler的命令行工具.
	emsdk(Emscripten SDK)是emscripten的sdk环境.

### 3 build wasm compiler env with emscripten

	以下内容来自emscripten官网: https://emscripten.org/docs/getting_started/downloads.html

#### 3.1 use emsdk install emscripten

	# Get the emsdk repo
	git clone https://github.com/emscripten-core/emsdk.git

	# Enter that directory
	cd emsdk

	# Fetch the latest version of the emsdk (not needed the first time you clone)
	git pull

	# Download and install the latest SDK tools.
	./emsdk install latest

	# Make the "latest" SDK "active" for the current user. (writes .emscripten file)
	./emsdk activate latest

	# Activate PATH and other environment variables in the current terminal
	source ./emsdk_env.sh	

#### 3.2 use docker(on linux recommend)

	docker run --rm -v $(pwd):/src -u $(id -u):$(id -g) emscripten/emsdk emcc helloworld.cpp -o helloworld.js


### 4 debug with chrome use c/c++ DevTools Support plugins(DWARF)

	DWARF是一种wasm的代码调试文件的格式文件.
	1) emcc编译时指定-g.
	2) chrome devtools插件配置WebAssembly debugging with DWARF.
    3) 带c/c++源码调试: emcc -g4 --source-map-base http://host:port/base-path-to-source-maps-and-sources/ ...


