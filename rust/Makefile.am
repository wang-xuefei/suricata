EXTRA_DIST = Cargo.toml Cargo.lock \
		src \
		.cargo/config.in \
		gen-c-headers.py

if HAVE_CARGO_VENDOR
EXTRA_DIST +=	vendor
endif

if HAVE_RUST_VENDOR
FROZEN = --frozen
endif

if !DEBUG
RELEASE = --release
endif

if HAVE_LUA
RUST_FEATURES +=	lua $(LUA_INT8)
endif

if DEBUG
RUST_FEATURES +=	debug
endif

if RUST_CROSS_COMPILE 
RUST_TARGET = --target $(host_triplet)
endif

all-local:
if HAVE_PYTHON
	cd $(top_srcdir)/rust && $(HAVE_PYTHON) ./gen-c-headers.py
endif
if HAVE_CYGPATH
	rustpath=`cygpath -a -t mixed $(abs_top_builddir)`
	cd $(top_srcdir)/rust && @rustup_home@ \
		CARGO_HOME="$(CARGO_HOME)" \
		CARGO_TARGET_DIR="$$rustpath/rust/target" \
		$(CARGO) build $(RELEASE) $(FROZEN) \
			--features "$(RUST_FEATURES)" $(RUST_TARGET)
else
	cd $(top_srcdir)/rust && @rustup_home@ \
		CARGO_HOME="$(CARGO_HOME)" \
		CARGO_TARGET_DIR="$(abs_top_builddir)/rust/target" \
		$(CARGO) build $(RELEASE) $(FROZEN) \
			--features "$(RUST_FEATURES)" $(RUST_TARGET)
endif

clean-local:
	-rm -rf target gen

distclean-local: clean-local
	rm -rf vendor gen Cargo.lock

check:
	CARGO_HOME="$(CARGO_HOME)" @rustup_home@
		$(CARGO) test --features "$(RUST_FEATURES)"

Cargo.lock: Cargo.toml
	CARGO_HOME="$(CARGO_HOME)" @rustup_home@ $(CARGO) \
		generate-lockfile

if HAVE_CARGO_VENDOR
vendor:
	CARGO_HOME="$(CARGO_HOME)" @rustup_home@ $(CARGO) vendor > /dev/null
else
vendor:
endif

# Can only include the headers if we have Python to generate them.
if HAVE_PYTHON
EXTRA_DIST +=	gen/c-headers
gen/c-headers:
	cd $(top_srcdir)/rust && $(HAVE_PYTHON) ./gen-c-headers.py
else
gen/c-headers:
endif
