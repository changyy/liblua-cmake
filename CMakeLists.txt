cmake_minimum_required(VERSION 2.8)
project (liblua)

if (DEFINED CMAKE_TOOLCHAIN_FILE)
	message("Include Cross Platform cmake: " ${CROSS_PLATFORM_CMAKE})
	get_filename_component(COMPILER_NAME ${CMAKE_C_COMPILER} NAME)
	message( "CMAKE_C_COMPILER: ${COMPILER_NAME}")
	string(REGEX REPLACE "(.*)-gcc" "\\1" target_platform "${COMPILER_NAME}")
	message( "target_platform: ${target_platform}")
	set(target_platform_preconfigure_options "CC=${target_platform}-gcc;AR=\"${target_platform}-ar;rcu\";LD=${target_platform}-ld;RANLIB=${target_platform}-ranlib;NM=${target_platform}-nm;AS=${target_platform}-as;OBJDUMP=${target_platform}-objdump;OBJCOPY=${target_platform}-objcopy;STRIP=${target_platform}-strip;STRINGS=${target_platform}-strings;SIZE=${target_platform}-size")
endif(DEFINED CMAKE_TOOLCHAIN_FILE)

#set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
#set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
#set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

set(TOOL_LIBLUA_SOURCE lua-5.1.5.tar.gz)
set(TOOL_LIBLUA_DIR lua-5.1.5)
set(CONFIG_ADD_OPTIONS "")
	if(DEFINED CMAKE_TOOLCHAIN_FILE)
		add_custom_command(
			OUTPUT BUILD_LIBLUA
			COMMAND curl http://www.lua.org/ftp/lua-5.1.5.tar.gz | tar -zxvf - && cd ${CMAKE_BINARY_DIR}/${TOOL_LIBLUA_DIR} && make posix ${target_platform_preconfigure_options} INSTALL_TOP=${CMAKE_BINARY_DIR} ${CONFIG_ADD_OPTIONS} install
			WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
			COMMENT "Build liblua"
		)
	else(DEFINED CMAKE_TOOLCHAIN_FILE)
		add_custom_command(
			OUTPUT BUILD_LIBLUA
			COMMAND curl http://www.lua.org/ftp/lua-5.1.5.tar.gz | tar -zxvf - && cd ${CMAKE_BINARY_DIR}/${TOOL_LIBLUA_DIR} && make linux INSTALL_TOP=${CMAKE_BINARY_DIR} ${CONFIG_ADD_OPTIONS} install
			WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
			COMMENT "Build liblua" 
		)
	endif(DEFINED CMAKE_TOOLCHAIN_FILE)
	add_custom_target(build_liblua ALL DEPENDS BUILD_LIBLUA )
