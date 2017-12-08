if(RAZXX_BUILD_EXAMPLES)
    # examples
    add_executable(zoom zoom.cpp)
    target_link_libraries(zoom razxx)
endif()