add_executable(unittests
    ClassTests.cpp
    DiagnosticTests.cpp
    EvalTests.cpp
    ExpressionParsingTests.cpp
    ExpressionTests.cpp
    FileTests.cpp
    HierarchyTests.cpp
    LexerTests.cpp
    LookupTests.cpp
    main.cpp
    MemberParsingTests.cpp
    MemberTests.cpp
    MIRTests.cpp
    NumericTests.cpp
    PortTests.cpp
    PreprocessorTests.cpp
    StatementParsingTests.cpp
    StatementTests.cpp
    SystemFuncTests.cpp
    TypeTests.cpp
    UtilTests.cpp
    VisitorTests.cpp
)

target_link_libraries(unittests PRIVATE slangcompiler)

if(CI_BUILD)
    message("Running CI build")
    target_compile_definitions(unittests PRIVATE CI_BUILD)
endif()

# Copy the data directory for running tests from the build folder.
add_custom_command(
    TARGET unittests POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR}/data ${CMAKE_CURRENT_BINARY_DIR}/data
)

add_test(NAME unittests COMMAND unittests)

# Set timeout property based on CI_BUILD and SLANG_COVERAGE
set(timeout 5)
if(CI_BUILD)
    math(EXPR timeout "${timeout} * 4")
endif()
if(SLANG_COVERAGE)
    math(EXPR timeout "${timeout} * 16")
endif()
set_tests_properties(unittests PROPERTIES TIMEOUT ${timeout})
