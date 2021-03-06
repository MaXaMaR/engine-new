cmake_minimum_required(VERSION 3.4)
project(ccgost)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c98 -O3")

set(GOST_INCLUDE_DIRECTORIES "${OPENSSL_PATH}/include" "${OPENSSL_PATH}/crypto/include")

set(GOST_LINK_DIRECTORIES "${OPENSSL_PATH}")

include_directories("${GOST_INCLUDE_DIRECTORIES}")

set(BIN_DIRECTORY bin)

set(OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/${BIN_DIRECTORY})

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${OUTPUT_DIRECTORY})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${OUTPUT_DIRECTORY})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${OUTPUT_DIRECTORY})

set(GOST_89_SOURCE_FILES
        gost89.c
        gost89.h
        )

set(GOST_HASH_SOURCE_FILES
        gosthash.c
        gosthash.h
        )

set(GOST_HASH_2012_SOURCE_FILES
        gosthash2012.c
        gosthash2012.h
        gosthash2012_const.h
        gosthash2012_precalc.h
        gosthash2012_ref.h
        gosthash2012_sse2.h
        )

set(GOST_GRASSHOPPER_SOURCE_FILES
        gost_grasshopper.h
        gost_grasshopper_core.h
        gost_grasshopper_core.c
        gost_grasshopper_defines.h
        gost_grasshopper_defines.c
        gost_grasshopper_math.h
        gost_grasshopper_galois_precompiled.c
        gost_grasshopper_precompiled.c
        gost_grasshopper_cipher.h
        gost_grasshopper_cipher.c
        gost_grasshopper_mac.h
        gost_grasshopper_mac.c
        )

set(GOST_CORE_SOURCE_FILES
        e_gost_err.c
        e_gost_err.h
        gost_asn1.c
        gost_crypt.c
        gost_ctl.c
        gost_eng.c
        gost_keywrap.c
        gost_keywrap.h
        gost_lcl.h
        gost_params.c
        )

set(GOST_EC_SOURCE_FILES
        gost_ec_keyx.c
        gost_ec_sign.c
        )

set(GOST_ENGINE_SOURCE_FILES
        ${GOST_CORE_SOURCE_FILES}
        ${GOST_EC_SOURCE_FILES}
        ${GOST_89_SOURCE_FILES}
        gost_ameth.c
        gost_md.c
        gost_md2012.c
        gost_pmeth.c
        ${GOST_HASH_SOURCE_FILES}
        ${GOST_GRASSHOPPER_SOURCE_FILES}
        ${GOST_HASH_2012_SOURCE_FILES})

link_directories(${GOST_LINK_DIRECTORIES})

add_library(gost_engine SHARED ${GOST_ENGINE_SOURCE_FILES})

add_library(crypto STATIC IMPORTED)

set_target_properties(crypto PROPERTIES IMPORTED_LOCATION ${OPENSSL_PATH}/libcrypto.a)

target_link_libraries(gost_engine crypto)

set(GOST_12_SUM_SOURCE_FILES
        gost12sum.c
        )

add_executable(gost12sum ${GOST_12_SUM_SOURCE_FILES})

target_link_libraries(gost12sum gost_engine)

set(GOST_SUM_SOURCE_FILES
        gostsum.c
        )

add_executable(gostsum ${GOST_SUM_SOURCE_FILES})

target_link_libraries(gostsum gost_engine)

set(GOST_SUM_12_SOURCE_FILES
        gostsum12.c
        )

add_executable(gostsum12 ${GOST_SUM_12_SOURCE_FILES})

target_link_libraries(gostsum12 gost_engine)
