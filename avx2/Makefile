#CC ?= /usr/bin/cc
WIN32=1
ifdef WIN32
CC=cl
CFLAGS += -O3
OUT=-Fo
SO_FLAGS=-dll
SO_EXT=.dll
else
CFLAGS += -Wall -Wextra -Wpedantic -Wmissing-prototypes -Wredundant-decls \
  -Wshadow -Wpointer-arith -mavx2 -mpopcnt \
  -march=native -mtune=native -O3
OUT=-o 
SO_FLAGS=-shared -fPIC
SO_EXT=.so
endif
NISTFLAGS += -Wno-unused-result -mavx2 -mpopcnt \
  -march=native -mtune=native -O3
SOURCES = sign.c packing.c polyvec.c poly.c ntt.S invntt.S pointwise.S \
  shuffle.S consts.c rejsample.c rounding.c
HEADERS = align.h config.h params.h api.h sign.h packing.h polyvec.h poly.h ntt.h \
  consts.h shuffle.inc rejsample.h rounding.h symmetric.h randombytes.h
KECCAK_SOURCES = $(SOURCES) fips202.c fips202x4.c f1600x4.S symmetric-shake.c
KECCAK_HEADERS = $(HEADERS) fips202.h fips202x4.h

.PHONY: all shared clean

all: \
  test/test_dilithium2 \
  test/test_dilithium3 \
  test/test_dilithium5 \
  test/test_vectors2 \
  test/test_vectors3 \
  test/test_vectors5 \
  speed

speed: \
  test/test_speed2 \
  test/test_speed3 \
  test/test_speed5 \

shared: \
  libpqcrystals_dilithium2_avx2$(SO_EXT) \
  libpqcrystals_dilithium3_avx2.so \
  libpqcrystals_dilithium5_avx2.so \
  libpqcrystals_fips202_avx2.so \
  libpqcrystals_fips202x4_avx2.so \

libpqcrystals_fips202_avx2.so: fips202.c fips202.h
	$(CC) $(SO_FLAGS) $(CFLAGS) $(OUT)$@ $<

libpqcrystals_fips202x4_avx2.so: fips202x4.c fips202x4.h f1600x4.S
	$(CC) $(SO_FLAGS) $(CFLAGS) $(OUT)$@ $< f1600x4.S

libpqcrystals_dilithium2_avx2.so: $(SOURCES) $(HEADERS) symmetric-shake.c
	$(CC) $(SO_FLAGS) $(CFLAGS) -DDILITHIUM_MODE=2 \
	  $(OUT)$@ $(SOURCES) symmetric-shake.c

libpqcrystals_dilithium3_avx2.so: $(SOURCES) $(HEADERS) symmetric-shake.c
	$(CC) $(SO_FLAGS) $(CFLAGS) -DDILITHIUM_MODE=3 \
	  $(OUT)$@ $(SOURCES) symmetric-shake.c

libpqcrystals_dilithium5_avx2.so: $(SOURCES) $(HEADERS) symmetric-shake.c
	$(CC) $(SO_FLAGS) $(CFLAGS) -DDILITHIUM_MODE=5 \
	  $(OUT)$@ $(SOURCES) symmetric-shake.c

test/test_dilithium2: test/test_dilithium.c randombytes.c $(KECCAK_SOURCES) \
  $(KECCAK_HEADERS)
	$(CC) $(CFLAGS) -DDILITHIUM_MODE=2 \
	  $(OUT)$@ $< randombytes.c $(KECCAK_SOURCES)

test/test_dilithium3: test/test_dilithium.c randombytes.c $(KECCAK_SOURCES) \
  $(KECCAK_HEADERS)
	$(CC) $(CFLAGS) -DDILITHIUM_MODE=3 \
	  $(OUT)$@ $< randombytes.c $(KECCAK_SOURCES)

test/test_dilithium5: test/test_dilithium.c randombytes.c $(KECCAK_SOURCES) \
  $(KECCAK_HEADERS)
	$(CC) $(CFLAGS) -DDILITHIUM_MODE=5 \
	  $(OUT)$@ $< randombytes.c $(KECCAK_SOURCES)

test/test_vectors2: test/test_vectors.c $(KECCAK_SOURCES) $(KECCAK_HEADERS)
	$(CC) $(CFLAGS) -DDILITHIUM_MODE=2 \
	  $(OUT)$@ $< $(KECCAK_SOURCES)

test/test_vectors3: test/test_vectors.c $(KECCAK_SOURCES) $(KECCAK_HEADERS)
	$(CC) $(CFLAGS) -DDILITHIUM_MODE=3 \
	  $(OUT)$@ $< $(KECCAK_SOURCES)

test/test_vectors5: test/test_vectors.c $(KECCAK_SOURCES) $(KECCAK_HEADERS)
	$(CC) $(CFLAGS) -DDILITHIUM_MODE=5 \
	  $(OUT)$@ $< $(KECCAK_SOURCES)

test/test_speed2: test/test_speed.c test/speed_print.c test/speed_print.h \
  test/cpucycles.c test/cpucycles.h randombytes.c $(KECCAK_SOURCES) \
  $(KECCAK_HEADERS)
	$(CC) $(CFLAGS) -DDILITHIUM_MODE=2 \
	  $(OUT)$@ $< test/speed_print.c test/cpucycles.c randombytes.c \
	  $(KECCAK_SOURCES)

test/test_speed3: test/test_speed.c test/speed_print.c test/speed_print.h \
  test/cpucycles.c test/cpucycles.h randombytes.c $(KECCAK_SOURCES) \
  $(KECCAK_HEADERS)
	$(CC) $(CFLAGS) -DDILITHIUM_MODE=3 \
	  $(OUT)$@ $< test/speed_print.c test/cpucycles.c randombytes.c \
	  $(KECCAK_SOURCES)

test/test_speed5: test/test_speed.c test/speed_print.c test/speed_print.h \
  test/cpucycles.c test/cpucycles.h randombytes.c $(KECCAK_SOURCES) \
  $(KECCAK_HEADERS)
	$(CC) $(CFLAGS) -DDILITHIUM_MODE=5 \
	  $(OUT)$@ $< test/speed_print.c test/cpucycles.c randombytes.c \
	  $(KECCAK_SOURCES)

test/test_mul: test/test_mul.c randombytes.c $(KECCAK_SOURCES) \
  $(KECCAK_HEADERS)
	$(CC) $(CFLAGS) -UDBENCH $(OUT)$@ $< randombytes.c $(KECCAK_SOURCES)


clean:
	rm -f *.o *.a *.so
	rm -f test/test_dilithium2
	rm -f test/test_dilithium3
	rm -f test/test_dilithium5
	rm -f test/test_vectors2
	rm -f test/test_vectors3
	rm -f test/test_vectors5
	rm -f test/test_speed2
	rm -f test/test_speed3
	rm -f test/test_speed5
	rm -f test/test_mul
