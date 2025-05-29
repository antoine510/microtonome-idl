set(FBS_FILES
    battery.fbs
)

foreach (_file ${FBS_FILES})
    cmake_path(GET _file STEM _gen_output)
    cmake_path(APPEND_STRING _gen_output "_generated.h")
    cmake_path(ABSOLUTE_PATH _file OUTPUT_VARIABLE _gen_input)
    list(APPEND GEN_OUTPUT ${_gen_output})
    list(APPEND GEN_INPUT ${_gen_input})
endforeach()

if(FLATBUFFERS_FLATC_EXECUTABLE)
    set(FLATC_TARGET "")
    set(FLATC ${FLATBUFFERS_FLATC_EXECUTABLE})
elseif(TARGET flatbuffers::flatc)
    set(FLATC_TARGET flatbuffers::flatc)
    set(FLATC flatbuffers::flatc)
else()
    set(FLATC_TARGET flatc)
    set(FLATC flatc)
endif()

add_custom_command(OUTPUT ${GEN_OUTPUT}
    COMMAND ${FLATC} --cpp --scoped-enums --gen-mutable ${GEN_INPUT}
    DEPENDS ${FLATC_TARGET} ${GEN_INPUT}
)

add_library(flatbuffers_target INTERFACE ${GEN_OUTPUT})
target_include_directories(flatbuffers_target INTERFACE ${CMAKE_CURRENT_BINARY_DIR})
target_link_libraries(flatbuffers_target INTERFACE flatbuffers::flatbuffers)
