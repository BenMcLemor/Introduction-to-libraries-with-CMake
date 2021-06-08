
# the add library take 2 parameters: the 1 one ist the name of the library. here we'll use the project name variable. 
# the 2 one is the source code file required to compile the library. there is 2 kind o library. Static library and shared library
add_library("${PROJECT_NAME}"  "")
add_library(Foo::Foo ALIAS "${PROJECT_NAME}")

# now we specifie the include directory. the build directory and the install directory hae different include directories. we need to specify them separately (line 8)
target_include_directories("${PROJECT_NAME}" PUBLIC
	"$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
	# this line add ${CMAKE_CURRENT_BINARY_DIR} to our include directory for the build directory
	"$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>" #foo_export.h	
	#here we specify the include directory and the install directory
	"$<INSTALL_INTERFACE:include>" #${CMAKE_INSTALL_PREFIX}/include
)

# Notice:
# PRIVATE		Build Requirement # to compile a Target 
# INTERFACE	Usage Requirement
# PUBLIC		PRIVATE + INTERFACE # to link against a target

#we also want to add -s to our postfix where we're building a static library. that why we usr the build-shared-libs variable
if(NOT BUILD_SHARED_LIBS)
	set(STATIC_POSTFIX "-s") # set static postfix to -s if build-shared-libs is false
	set_target_properties("${PROJECT_NAME}" PROPERTIES 
	COMPILE_FLAGS -DFOO_STATIC_DEFINE)
endif()
 
# we want here a debug version of our library : foo-d. if we would have a static version we will write foo-s-d. That what we do in the following lines
set_target_properties("${PROJECT_NAME}" PROPERTIES
	DEBUG_POSTFIX "${STATIC_POSTFIX}-d" #debud_postfix will be added to our library when we build it in debug mode
	RELEASE_POSTFIX "${STATIC_POSTFIX}"
	MINSIZEREL_POSTFIX "${STATIC_POSTFIX}-mr"
	RELWITHDEBINFO_POSTFIX "${STATIC_POSTFIX}-rd"
)

# in order to use our hello function, we have to export it with DLL export and import it with DLL import. This is usally done with an export macro. CMake can generate it for us.
include("GenerateExportHeader")
generate_export_header("${PROJECT_NAME}") 
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/foo_export.h" DESTINATION "include") # here we install the export files in DESTINATION "include"

# now we tell cmake about the include subdirectory in the src subdirectory
add_subdirectory("include")
add_subdirectory("src")