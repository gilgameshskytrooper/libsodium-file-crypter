cmake_minimum_required(VERSION 3.9)
project(libsodium-file-crypter C)

set(CMAKE_C_STANDARD 11)

# Find libsodium
FIND_PATH(LIBSODIUM_INCLUDE_DIR NAMES sodium.h PATHS /usr/include/ /usr/local/include/ /usr/local/opt/libsodium/include/)
FIND_LIBRARY(LIBSODIUM_LIBRARY NAMES sodium libsodium PATHS /usr/lib /usr/local/lib /usr/local/opt/libsodium/lib/)
IF (LIBSODIUM_INCLUDE_DIR AND LIBSODIUM_LIBRARY)
    SET(LIBSODIUM_FOUND TRUE)
ENDIF (LIBSODIUM_INCLUDE_DIR AND LIBSODIUM_LIBRARY)


IF (LIBSODIUM_FOUND)
    IF (NOT LIBSODIUM_FIND_QUIETLY)
        MESSAGE(STATUS "Found libsodium:")
        MESSAGE(STATUS "  (Headers)       ${LIBSODIUM_INCLUDE_DIR}")
        MESSAGE(STATUS "  (Library)       ${LIBSODIUM_LIBRARY}")
    ENDIF (NOT LIBSODIUM_FIND_QUIETLY)
ELSE (LIBSODIUM_FOUND)
    MESSAGE(FATAL_ERROR "Could not find libsodium")
ENDIF (LIBSODIUM_FOUND)


IF (APPLE)
  FIND_PATH(LIBARGP_INCLUDE_DIR NAMES argp.h PATHS /usr/local/opt/argp-standalone/include)
  FIND_LIBRARY(LIBARGP_LIBRARY NAMES argp libargp PATHS /usr/local/opt/argp-standalone/lib)
  IF (LIBARGP_INCLUDE_DIR AND LIBARGP_LIBRARY)
    SET(LIBARGP_FOUND TRUE)
  ENDIF (LIBARGP_INCLUDE_DIR AND LIBARGP_LIBRARY)


  IF (LIBARGP_FOUND)
    IF (NOT LIBARGP_FIND_QUIETLY)
          MESSAGE(STATUS "Found argp:")
          MESSAGE(STATUS "  (Headers)       ${LIBARGP_INCLUDE_DIR}")
          MESSAGE(STATUS "  (Library)       ${LIBARGP_LIBRARY}")
        ENDIF (NOT LIBARGP_FIND_QUIETLY)
      ELSE (LIBARGP_FOUND)
        MESSAGE(FATAL_ERROR "Could not find argp for macOS. Please install argp-standalone using Homebrew.")
      ENDIF (LIBARGP_FOUND)
ENDIF (APPLE)

add_executable(
        libsodium-file-crypter
        main.c
        crypto/decrypt_file.c crypto/decrypt_file.h
        crypto/encrypt_file.c crypto/encrypt_file.h
        crypto/initialize_libsodium.c crypto/initialize_libsodium.h
        utils/logger.c utils/logger.h
        utils/file_utils.c utils/file_utils.h)

target_link_libraries(libsodium-file-crypter ${LIBSODIUM_LIBRARY})

IF (APPLE)
  target_link_libraries(libsodium-file-crypter ${LIBARGP_LIBRARY})
  target_include_directories(libsodium-file-crypter PUBLIC /usr/local/opt/argp-standalone/include /usr/local/opt/libsodium/include/)
ENDIF (APPLE)
