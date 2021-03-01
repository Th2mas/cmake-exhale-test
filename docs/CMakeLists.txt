# Has to be installed manually on the machine, e.g. 'sudo apt install doxygen'
find_package(Doxygen REQUIRED)

# Has to be installed manually on the machine with the remaining packages inside the requirements.txt file
find_package(Sphinx REQUIRED)

# set input and output files
set(DOXYGEN_INPUT_DIR ${PROJECT_SOURCE_DIR}/common)
set(DOXYGEN_OUTPUT_DIR ${CMAKE_CURRENT_BINARY_DIR}/doxygen)
set(DOXYGEN_INDEX_FILE ${DOXYGEN_OUTPUT_DIR}/xml/index.xml)
set(DOXYFILE_IN ${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile)
set(DOXYFILE_OUT ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)

# Replace variables inside @@ with the current values
configure_file(${DOXYFILE_IN} ${DOXYFILE_OUT} @ONLY)

# Only regenerate Doxygen when the Doxyfile changes
add_custom_command(OUTPUT ${DOXYGEN_INDEX_FILE}
        COMMAND ${DOXYGEN_EXECUTABLE} ${DOXYFILE_OUT}
        MAIN_DEPENDENCY ${DOXYFILE_OUT} ${DOXYFILE_IN}
        COMMENT "Generating API documentation with Doxygen"
        VERBATIM)

# Run 'make Doxygen' to build the Doxygen documentation
add_custom_target(Doxygen ALL DEPENDS ${DOXYGEN_INDEX_FILE})

set(SPHINX_SOURCE ${CMAKE_CURRENT_SOURCE_DIR})
set(SPHINX_BUILD ${CMAKE_CURRENT_BINARY_DIR}/sphinx)
set(SPHINX_INDEX_FILE ${SPHINX_BUILD}/index.html)
# set(SPHINX_DOCS_SOURCE ${SPHINX_BUILD}/generated)

# Copy the conf.py and index.rst files, so that we can build the documentation inside the build directory
# This is needed for the Exhale plugin to work only inside the target directory, not the source directory
# configure_file(${CMAKE_CURRENT_SOURCE_DIR}/index.rst ${SPHINX_DOCS_SOURCE}/index.rst COPYONLY)
# configure_file(${CMAKE_CURRENT_SOURCE_DIR}/conf.py ${SPHINX_DOCS_SOURCE}/conf.py COPYONLY)

# Only regenerate Sphinx when
# - Doxygen has rerun
# - Our doc files have been updated
# - The Sphinx config has been updated
add_custom_command(OUTPUT ${SPHINX_INDEX_FILE}
        COMMAND ${SPHINX_EXECUTABLE} -b html
        ${SPHINX_SOURCE} ${SPHINX_BUILD}
        WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
        DEPENDS
        # Other docs files you want to track should go here (or in some variable)
        ${SPHINX_SOURCE}/index.rst
        ${DOXYGEN_INDEX_FILE}
        MAIN_DEPENDENCY ${SPHINX_SOURCE}/conf.py
        COMMENT "Generating documentation with Sphinx"
        )

# Run 'make Sphinx' to build the Sphinx documentation
add_custom_target(Sphinx ALL DEPENDS ${SPHINX_INDEX_FILE})
