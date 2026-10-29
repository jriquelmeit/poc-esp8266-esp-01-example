cmake_minimum_required(VERSION 3.16)
project(esp8266_esp01_wifi C CXX)

set(CMAKE_CXX_STANDARD 17)

# PlatformIO CLI executable
find_program(PLATFORMIO_CMD pio)
if(NOT PLATFORMIO_CMD)
    set(PLATFORMIO_CMD "pio")
endif()

# Directories for CLion code analysis and autocompletion
set(PIO_CORE_DIR "$ENV{HOME}/.platformio/packages/framework-arduinoespressif8266/cores/esp8266")
set(PIO_VARIANT_DIR "$ENV{HOME}/.platformio/packages/framework-arduinoespressif8266/variants/generic")
set(PIO_TOOLS_DIR "$ENV{HOME}/.platformio/packages/framework-arduinoespressif8266/tools/sdk/include")

include_directories(
    include
    src
    ${PIO_CORE_DIR}
    ${PIO_VARIANT_DIR}
    ${PIO_TOOLS_DIR}
)

add_definitions(-DESP8266 -DARDUINO=10805 -DF_CPU=80000000L)

# Project sources
file(GLOB_RECURSE SRC_FILES "src/*.*")

# Target for CLion code inspection / navigation
add_library(esp8266_esp01_wifi EXCLUDE_FROM_ALL ${SRC_FILES})

# PlatformIO execution targets
add_custom_target(
    PLATFORMIO_BUILD ALL
    COMMAND ${PLATFORMIO_CMD} run
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
    COMMENT "Compilando firmware para ESP8266 ESP-01 con PlatformIO..."
)

add_custom_target(
    PLATFORMIO_UPLOAD
    COMMAND ${PLATFORMIO_CMD} run -t upload
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
    COMMENT "Subiendo firmware a ESP8266 ESP-01..."
)

add_custom_target(
    PLATFORMIO_CLEAN
    COMMAND ${PLATFORMIO_CMD} run -t clean
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
    COMMENT "Limpiando artefactos de compilacion..."
)

add_custom_target(
    PLATFORMIO_MONITOR
    COMMAND ${PLATFORMIO_CMD} device monitor
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
    COMMENT "Abriendo monitor serie (115200 baud)..."
)
