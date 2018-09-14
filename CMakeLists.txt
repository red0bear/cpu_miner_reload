cmake_minimum_required(VERSION 3.10)

include(cmake/cable/bootstrap.cmake)

include(CableBuildInfo)
include(CableBuildType)
include(CableToolchains)
include(HunterGate)

set(HUNTER_CONFIGURATION_TYPES Release CACHE STRING "Build type of Hunter packages")
set(HUNTER_JOBS_NUMBER 6 CACHE STRING "Number of parallel builds used by Hunter")
HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.21.9.tar.gz"
    SHA1 "0056188988906abb63a06c6f1aaef01832c62b74"
    LOCAL
)

enable_language(C ASM)

project(minred_cpu)
set(PROJECT_VERSION_MINRED 0.1)

hunter_add_package(jansson)
find_package(jansson CONFIG REQUIRED)
include_directories( ${JANSSON_INCLUDE_DIRS} )

hunter_add_package(CURL)
find_package(CURL CONFIG REQUIRED)
include_directories( ${CURL_INCLUDE_DIRS} )

include_directories("includes")
include_directories("crypto")
include_directories("sha3")

configure_file (
  "cpuminer-config.h.in"
  "cpuminer-config.h"
  )

set(CMAKE_ASM_FLAGS "-DHAVE_CONFIG_H -pthread -fno-strict-aliasing -I${JANSSON_INCLUDE_DIRS} -L${JANSSON_LIBRARIES} -Ofast -flto -fuse-linker-plugin -lpthread -ljansson -lcrypto -lcurl -g -O3 ")
set(CMAKE_C_FLAGS "-DHAVE_CONFIG_H -pthread -fno-strict-aliasing -I${JANSSON_INCLUDE_DIRS} -L${JANSSON_LIBRARIES} -Ofast -flto -fuse-linker-plugin -lpthread -ljansson -lcrypto -lcurl -g -O3 ")

add_executable( minred
    includes/miner.h
    util.c  
    sha2.c 
    cryptonight.c
    scrypt.c 
    keccak.c 
    heavy.c 
    quark.c 
    skein.c 
    ink.c 
    x11.c
    x13.c
    x14.c
    x15.c
    blake.c
    sha3/sph_keccak.c 
    sha3/sph_hefty1.c 
    sha3/sph_groestl.c
    sha3/sph_skein.c 
    sha3/sph_bmw.c 
    sha3/sph_jh.c
    sha3/sph_shavite.c 
    sha3/sph_blake.c 
    sha3/sph_luffa.c
    sha3/sph_cubehash.c 
    sha3/sph_simd.c 
    sha3/sph_echo.c
    sha3/sph_hamsi.c 
    sha3/sph_fugue.c 
    sha3/sph_shabal.c
    sha3/sph_whirlpool.c 
    crypto/oaes_lib.c 
    crypto/c_keccak.c
    crypto/c_groestl.c 
    crypto/c_blake256.c 
    crypto/c_jh.c
    crypto/c_skein.c
    crypto/hash.c 
    crypto/aesb.c 
    crypto/aesb-x86-impl.c 
    includes/aesb-x64.S
    includes/scrypt-x64.S
    includes/sha2-x64.S
    fresh.c
    cpu-miner.c
)
