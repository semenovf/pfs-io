project(pfs-io-tests CXX)

# This is the "object library" target: compiles the sources only once
add_library(CATCH_OBJLIB OBJECT catch-main.cpp)
# Optional: shared libraries need PIC
set_property(TARGET CATCH_OBJLIB PROPERTY POSITION_INDEPENDENT_CODE 1)

#
# Add unit test targets
#
list(APPEND MY_TEST_TARGETS buffer)
list(APPEND MY_TEST_TARGETS file)
# list(APPEND MY_TEST_TARGETS io-buffered_device)
# list(APPEND MY_TEST_TARGETS io-device_manager)
# list(APPEND MY_TEST_TARGETS io-device_notifier_pool)

################################################################################

# set(test-integral_SOURCES
#     integral/test.cpp
#     integral/parse_integral_part.cpp
#     integral/to_integral.cpp)

add_library(catch SHARED $<TARGET_OBJECTS:CATCH_OBJLIB>)

#
# Process all unit test targets
#
foreach (MY_TEST_TARGET ${MY_TEST_TARGETS})
    if (EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/bunch/${MY_TEST_TARGET}.cpp")
        add_executable(test-${MY_TEST_TARGET} "${CMAKE_CURRENT_SOURCE_DIR}/bunch/${MY_TEST_TARGET}.cpp")
    elseif (test-${MY_TEST_TARGET}_SOURCES)
        add_executable(test-${MY_TEST_TARGET} ${test-${MY_TEST_TARGET}_SOURCES})
    else()
        add_executable(test-${MY_TEST_TARGET} ${MY_TEST_TARGET}/test.cpp)
    endif()

    if(test-${MY_TEST_TARGET}_LIBS)
        target_link_libraries(test-${MY_TEST_TARGET} catch ${test-${MY_TEST_TARGET}_LIBS})
    else()
        target_link_libraries(test-${MY_TEST_TARGET} catch)
    endif()
    
    add_test(NAME ${MY_TEST_TARGET} COMMAND test-${MY_TEST_TARGET})
endforeach(MY_TEST_TARGET)