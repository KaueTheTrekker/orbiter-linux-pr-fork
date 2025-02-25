set(MESH_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/Meshes)
set(TEXTURE_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/Textures)
set(CONFIG_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/Config)
set(DOC_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/Doc)

if(OPENGL_FOUND) # OpenGL is required for this target

add_library(Dragonfly SHARED
	Dragonfly.cpp
	Internal.cpp
	instruments.cpp
	Matrix.cpp
	quaternion.cpp
	vectors.cpp
	Hsystems.cpp
	Thermal.cpp
	Esystems.cpp
	panel.rc
)

set_target_properties(Dragonfly
	PROPERTIES
	FOLDER Vessels
)

target_include_directories(Dragonfly
	PUBLIC ${ORBITER_SOURCE_SDK_INCLUDE_DIR}
)

target_link_libraries(Dragonfly
	${ORBITER_LIB}
	${ORBITER_SDK_LIB}
	OpenGL::GL
	OpenGL::GLU
)

add_dependencies(Dragonfly
	${OrbiterTgt}
	Orbitersdk
)

add_custom_command(
	TARGET Dragonfly PRE_BUILD
	COMMAND ${CMAKE_COMMAND} -E copy_directory ${CONFIG_SOURCE_DIR}/ ${CMAKE_BINARY_DIR}/Config/Vessels
)

add_custom_command(
	TARGET Dragonfly PRE_BUILD
	COMMAND ${CMAKE_COMMAND} -E copy_directory ${MESH_SOURCE_DIR}/ ${CMAKE_BINARY_DIR}/Meshes
)

add_custom_command(
	TARGET Dragonfly PRE_BUILD
	COMMAND ${CMAKE_COMMAND} -E copy_directory ${TEXTURE_SOURCE_DIR}/ ${ORBITER_BINARY_TEXTURES_DIR}/Dragonfly
)

add_custom_command(
	TARGET Dragonfly PRE_BUILD
	COMMAND ${CMAKE_COMMAND} -E make_directory ${ORBITER_BINARY_DOC_DIR}
	COMMAND ${CMAKE_COMMAND} -E copy ${DOC_SOURCE_DIR}/Dragonfly.pdf ${ORBITER_BINARY_DOC_DIR}
)

install(TARGETS Dragonfly
	RUNTIME
	DESTINATION ${ORBITER_INSTALL_MODULE_DIR}
)
install(DIRECTORY ${CONFIG_SOURCE_DIR}/
	DESTINATION ${ORBITER_INSTALL_ROOT_DIR}/Config/Vessels
)
install(DIRECTORY ${MESH_SOURCE_DIR}/
	DESTINATION ${ORBITER_INSTALL_ROOT_DIR}/Meshes/Dragonfly
)
install(DIRECTORY ${TEXTURE_SOURCE_DIR}/
	DESTINATION ${ORBITER_INSTALL_TEXTURES_DIR}/Dragonfly
)
install(FILES ${DOC_SOURCE_DIR}/Dragonfly.pdf
	DESTINATION ${ORBITER_INSTALL_DOC_DIR}
)

else()

message(WARNING "OpenGL libraries not found. Skipping Dragonfly build.")

endif()
