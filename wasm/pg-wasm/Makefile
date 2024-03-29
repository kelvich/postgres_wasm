.PHONY: debug-build debug-datadir build datadir

# debug-build:
# 	EMCC_CFLAGS="-Wl,--allow-undefined" \
# 	emconfigure ./configure CFLAGS='-Oz' \
# 		--without-readline \
# 		--without-zlib \
# 		--disable-thread-safety \
# 		--disable-spinlocks \
# 		--with-system-tzdata=/usr/share/zoneinfo \
# 		--enable-debug
# 	EMCC_CFLAGS="-s ERROR_ON_UNDEFINED_SYMBOLS=0 -s WARN_ON_UNDEFINED_SYMBOLS=0 -s TOTAL_MEMORY=65536000 -s EMULATE_FUNCTION_POINTER_CASTS=1 -s ASSERTIONS=1" \
# 		emmake make -C src/backend MAKELEVEL=0
# 	mkdir -p wasm/debug
# 	cp src/backend/postgres wasm/debug/postgres.js
# 	cp src/backend/postgres.wasm wasm/debug/postgres.wasm

# debug-datadir:
# 	mkdir -p tmp_install
# 	DESTDIR="$(abspath tmp_install)" \
# 	EMCC_CFLAGS="-s ERROR_ON_UNDEFINED_SYMBOLS=0 -s WARN_ON_UNDEFINED_SYMBOLS=0 -s TOTAL_MEMORY=65536000 -s EMULATE_FUNCTION_POINTER_CASTS=1 -s ASSERTIONS=1" \
# 		emmake make MAKELEVEL=0 -C src/backend/ install
# 	node wasm/initdb.js
# 	cd wasm/debug && \
# 	`em-config EMSCRIPTEN_ROOT`/tools/file_packager pgdata.data --preload ./temp_pgdata@/pgdata --js-output=pgdata.js && \
# 	`em-config EMSCRIPTEN_ROOT`/tools/file_packager share.data --preload ../../tmp_install/usr/local/pgsql/share@/usr/local/pgsql/share --js-output=share.js

build:
	EMCC_CFLAGS="-Wl,--allow-undefined" \
	emconfigure ./configure CFLAGS='-Oz' \
		--without-readline \
		--without-zlib \
		--disable-thread-safety \
		--disable-spinlocks \
		--with-system-tzdata=/usr/share/zoneinfo
	EMCC_CFLAGS="-s ERROR_ON_UNDEFINED_SYMBOLS=0 -s WARN_ON_UNDEFINED_SYMBOLS=0 -s TOTAL_MEMORY=65536000 -s EMULATE_FUNCTION_POINTER_CASTS=1 -s MODULARIZE=1 -s EXPORT_ES6=1 -s EXPORTED_RUNTIME_METHODS='FS'" \
		emmake make -C src/backend MAKELEVEL=0
	mkdir -p wasm/pg-wasm/release
	cp src/backend/postgres wasm/pg-wasm/release/postgres.js
	cp src/backend/postgres.wasm wasm/pg-wasm/release/postgres.wasm

datadir:
	mkdir -p tmp_install
	DESTDIR="$(abspath tmp_install)" \
	EMCC_CFLAGS="-s ERROR_ON_UNDEFINED_SYMBOLS=0 -s WARN_ON_UNDEFINED_SYMBOLS=0 -s TOTAL_MEMORY=65536000 -s EMULATE_FUNCTION_POINTER_CASTS=1 -s MODULARIZE=1 -s EXPORT_ES6=1 -s EXPORTED_RUNTIME_METHODS='FS'" \
		emmake make MAKELEVEL=0 -C src/backend/ install
	PGWASM_BUILD_TYPE=release node wasm/pg-wasm/initdb.js
	cd wasm/pg-wasm/release && \
	`em-config EMSCRIPTEN_ROOT`/tools/file_packager pgdata.data --preload ./temp_pgdata@/pgdata --js-output=pgdata.js --no-node && \
	`em-config EMSCRIPTEN_ROOT`/tools/file_packager share.data --preload ../../../tmp_install/usr/local/pgsql/share@/usr/local/pgsql/share --js-output=share.js --no-node


	echo -e "function load(Module) {$(cat pgdata.js)" > pgdata.js
