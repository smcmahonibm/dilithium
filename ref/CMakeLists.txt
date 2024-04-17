set(DILITHIUM_SRCS sign.c packing.c polyvec.c poly.c ntt.c reduce.c rounding.c)
set(DILITHIUM_FIPS202_SRCS ${DILITHIUM_SRCS} symmetric-shake.c)
set(FIPS202_SRCS fips202.c)
set(TEST_DILITHIUM_SRCS test/test_dilithium.c randombytes.c)
set(TEST_VECTORS_SRCS test/test_vectors.c)
set(TEST_SPEED_SRCS test/test_speed.c test/speed_print.c test/cpucycles.c randombytes.c)

if(MSVC)
  add_compile_options(/nologo /W4 /wd4146 /wd4244)
else()
  add_compile_options(-Wall -Wextra -Wpedantic -Werror)
  add_compile_options(-Wmissing-prototypes -Wredundant-decls -Wshadow -Wpointer-arith)
  add_compile_options(-O3 -fomit-frame-pointer)
endif()

add_library(fips202_ref ${FIPS202_SRCS})

# Dilithium 2
add_library(dilithium2_ref ${DILITHIUM_FIPS202_SRCS})
target_compile_definitions(dilithium2_ref PUBLIC DILITHIUM_MODE=2)
target_link_libraries(dilithium2_ref INTERFACE fips202_ref)

add_executable(test_dilithium2_ref ${TEST_DILITHIUM_SRCS})
add_executable(test_vectors2_ref ${TEST_VECTORS_SRCS})
target_link_libraries(test_dilithium2_ref dilithium2_ref)
target_link_libraries(test_vectors2_ref dilithium2_ref)

# Dilithium 3
add_library(dilithium3_ref ${DILITHIUM_FIPS202_SRCS})
target_compile_definitions(dilithium3_ref PUBLIC DILITHIUM_MODE=3)
target_link_libraries(dilithium3_ref INTERFACE fips202_ref)

add_executable(test_dilithium3_ref ${TEST_DILITHIUM_SRCS})
add_executable(test_vectors3_ref ${TEST_VECTORS_SRCS})
target_link_libraries(test_dilithium3_ref dilithium3_ref)
target_link_libraries(test_vectors3_ref dilithium3_ref)

# Dilithium 5
add_library(dilithium5_ref ${DILITHIUM_FIPS202_SRCS})
target_compile_definitions(dilithium5_ref PUBLIC DILITHIUM_MODE=5)
target_link_libraries(dilithium5_ref INTERFACE fips202_ref)

add_executable(test_dilithium5_ref ${TEST_DILITHIUM_SRCS})
add_executable(test_vectors5_ref ${TEST_VECTORS_SRCS})
target_link_libraries(test_dilithium5_ref dilithium5_ref)
target_link_libraries(test_vectors5_ref dilithium5_ref)

add_test(NAME dilithium2_ref COMMAND test_dilithium2_ref)
add_test(NAME dilithium3_ref COMMAND test_dilithium3_ref)
add_test(NAME dilithium5_ref COMMAND test_dilithium5_ref)

if(WIN32)
  add_test(NAME vectors2_ref COMMAND PowerShell -Command "$<TARGET_FILE:test_vectors2_ref> | dos2unix > tvecs2")
  add_test(NAME vectors3_ref COMMAND PowerShell -Command "$<TARGET_FILE:test_vectors3_ref> | dos2unix > tvecs3")
  add_test(NAME vectors5_ref COMMAND PowerShell -Command "$<TARGET_FILE:test_vectors5_ref> | dos2unix > tvecs5")
else()
  add_test(NAME vectors2_ref COMMAND sh -c "\"$<TARGET_FILE:test_vectors2_ref>\" > tvecs2")
  add_test(NAME vectors3_ref COMMAND sh -c "\"$<TARGET_FILE:test_vectors3_ref>\" > tvecs3")
  add_test(NAME vectors5_ref COMMAND sh -c "\"$<TARGET_FILE:test_vectors5_ref>\" > tvecs5")
endif()

add_test(NAME hashes COMMAND sha256sum -c ../../SHA256SUMS)
