# we creare 3 variables
set(FILES "${CMAKE_CURRENT_SOURCE_DIR}/Hello.h")
set(SUBDIRECTORIES "")
set(RELATIVE_PATH "include/Foo")

#Add all subdirectories 
foreach(VAR ${SUBDIRECTORIES}) # in each loop we put subdirectories in VAR
	add_subdirectory(${VAR}) # and then we call add subdirectory on VAR
endforeach()

# now we check if there are any files in this directory
if(FILES)
	#Add source files our target foo. Means we're adding this source file ${FILES} to this targed "${PROJECT_NAME}"
	target_sources("${PROJECT_NAME}" PRIVATE ${FILES})
	
	#Install our source files FILES ind DESTINATION
	install(FILES ${FILES} DESTINATION ${RELATIVE_PATH})
endif()