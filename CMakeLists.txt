cmake_minimum_required(VERSION 3.21)

project(LLScriptEngine)

set(CMAKE_CXX_STANDARD 20)

file(
    GLOB_RECURSE SRC_FILES_DIR
    ${PROJECT_SOURCE_DIR}/src/*.cpp
    ${PROJECT_SOURCE_DIR}/src/*.hpp
    ${PROJECT_SOURCE_DIR}/src/*.h
    ${PROJECT_SOURCE_DIR}/src/*.inc
    ${PROJECT_SOURCE_DIR}/src/*.rc
)

file(
    GLOB_RECURSE THIRD_PARTY_LIBS
    ${PROJECT_SOURCE_DIR}/third-party/lib/*.lib
)

target_include_directories(
    ${PROJECT_NAME} PRIVATE
    ${CMAKE_SOURCE_DIR}
    ${LiteLoader_SOURCE_DIR}/include/
    ${LiteLoader_SOURCE_DIR}/third-party/include
    ${PROJECT_SOURCE_DIR}/src
    ${PROJECT_SOURCE_DIR}/third-party/include
    ${PROJECT_SOURCE_DIR}/third-party/ScriptX/src/include
)

target_link_libraries(
    ${PROJECT_NAME}
    LiteLoader
    ScriptX-${LLSE_BACKEND_LIBRARY}
    "${LiteLoader_SOURCE_DIR}/third-party/lib/openssl/libcrypto.lib"
    "${LiteLoader_SOURCE_DIR}/third-party/lib/openssl/libssl.lib"
    ${THIRD_PARTY_LIBS}

    # Make sure that libraries related to BDS are at the bottom of the
    # list.
    "${LiteLoader_SOURCE_DIR}/lib/bedrock_server_api.lib"
    "${LiteLoader_SOURCE_DIR}/lib/bedrock_server_var.lib"

    SymDBHelper
)

add_definitions(
    -DCPPHTTPLIB_OPENSSL_SUPPORT
    -DNDEBUG
    -D_CONSOLE
    -D_AMD64_
    -DNOMINMAX
    -DSCRIPTX_BACKEND_TRAIT_PREFIX=../backend/${SCRIPTX_BACKEND}/trait/Trait
    -D_WINDLL
    -D_UNICODE
    -DUNICODE
    -D "LLSE_BACKEND_${LLSE_BACKEND}"
    -DCMAKE
)

add_compile_options(
    /permissive-
    /MP
    /GS
    /GL
    /W3
    /Gy
    /Zc:wchar_t
    /Zi
    /Gm-
    /O2
    /sdl
    /Zc:inline
    /fp:precise
    /errorReport:prompt
    /WX-
    /Zc:forScope
    /Gd
    /Oi
    /MD
    /FC
    /nologo
    /diagnostics:column
    /utf-8
    /EHa
)

add_link_options(
    /MANIFEST
    /LTCG:incremental
    /NXCOMPAT
    /DEBUG:FULL
    /DLL
    /MACHINE:X64
    /OPT:REF
    /INCREMENTAL:NO
    /SUBSYSTEM:WINDOWS
    /MANIFESTUAC:NO
    /OPT:ICF
    /ERRORREPORT:PROMPT
    /NOLOGO
    /TLBID:1

    # Import BDS APIs manually and bedrock_server.dll is not required when
    # linking.
    /DELAYLOAD:bedrock_server.dll 
)

add_library(${PROJECT_NAME} SHARED ${SRC_FILES_DIR})