CMAKE_MINIMUM_REQUIRED( VERSION 3.1.1 )
PROJECT( qt_desktop )

INCLUDE( WGToolsCoreProject )
INCLUDE( WGProjectUserFile )
INCLUDE( WGTFAppMacros )

find_package(QT5 ${QT_FIND_PACKAGE_VERSION})

ADD_DEFINITIONS( -DAPPLICATION_SETTINGS_NAME="QtDesktop" )

SET( PLUGIN_QT_COMMON_DIR ${WG_TOOLS_SOURCE_DIR}/core/plugins/plg_qt_common )
SET( APP_QT_COMMON_DIR ${WG_TOOLS_SOURCE_DIR}/core/app/qt_desktop/app )

SET( APP_CODE_SRCS
	${APP_QT_COMMON_DIR}/main.cpp
	${APP_QT_COMMON_DIR}/qt_desktop_application.hpp
	${APP_QT_COMMON_DIR}/qt_desktop_application.cpp
)
SOURCE_GROUP( "App" FILES ${APP_CODE_SRCS} )

FILE( GLOB PLUGIN_CONFIG_FILES
	config/plugins_pre_qt.txt
	config/plugins_post_qt.txt
)

SOURCE_GROUP( "Plugin Configurations" FILES ${PLUGIN_CONFIG_FILES} )
BW_CUSTOM_COPY_TO_PROJECT_OUTPUT( plugins "${PLUGIN_CONFIG_FILES}" )

BW_QT_ADD_QRC(WGControls ${PLUGIN_QT_COMMON_DIR}/resources/WGControls)
BW_QT_ADD_QRC(private ${PLUGIN_QT_COMMON_DIR}/resources/private)
SET(QRC_FILE
	resources.qrc
)
source_group("Resource Files" FILES ${QRC_FILE})
qt5_add_resources(COMPILED_RESOURCES ${QRC_FILE})
SOURCE_GROUP("Compiled Resources" FILES ${COMPILED_RESOURCES})

BW_QT_ADD_CONFIG_QRC()
BW_GLOB_RESOURCE_FILES()

WG_BLOB_SOURCES( BLOB_SRCS
	${APP_CODE_SRCS}
	${QT_CODE_SRCS}
	${PLUGIN_CONFIG_FILES}
	${COMPILED_RESOURCES}
)
BW_ADD_TOOL_EXE( ${PROJECT_NAME} WIN32 ${BLOB_SRCS} )

WG_APP_PLATFORM_CONFIGURATION()

IF (Qt5_WEB_ENGINE_SUPPORT)
	ADD_DEFINITIONS( -DUSE_Qt5_WEB_ENGINE=1 )
	SET(Qt5_WEB_ENGINE_LIBRARY Qt5::WebEngine)
ELSE()
	SET(Qt5_WEB_ENGINE_LIBRARY)
ENDIF()

BW_TARGET_LINK_LIBRARIES( ${PROJECT_NAME} PRIVATE
	core_generic_plugin_manager
	core_wgtf_app
	wgtf_memory
	core_qt_common
	core_file_logging
	core_serialization_xml
	Qt5::Core
	Qt5::Gui
	Qt5::QuickWidgets
	Qt5::Quick
	Qt5::UiTools
	Qt5::Xml
	${Qt5_WEB_ENGINE_LIBRARY}

	# external libraries
	${PLATFORM_LIBRARIES}
)

INCLUDE_DIRECTORIES(${Qt5Core_PRIVATE_INCLUDE_DIRS})
INCLUDE_DIRECTORIES(${PLUGIN_QT_COMMON_DIR})

BW_PROJECT_CATEGORY( ${PROJECT_NAME} "Executables" )
