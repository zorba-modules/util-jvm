# Copyright 2006-2010 The FLWOR Foundation.
# 
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
# 
# http://www.apache.org/licenses/LICENSE-2.0
# 
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

MESSAGE(STATUS "")
MESSAGE(STATUS "+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++")
MESSAGE(STATUS "Configuring external module <util-jvm>")
MESSAGE(STATUS "-------------------------------------------------------------")
MESSAGE(STATUS "")

CMAKE_MINIMUM_REQUIRED(VERSION 2.6)

PROJECT (zorba_util-jvm_module)

IF (ZORBA_SUPPRESS_JAVA)
  MESSAGE (STATUS "ZORBA_SUPPRESS_JAVA is true - not searching for Java")
  
  MESSAGE(STATUS "")
  MESSAGE(STATUS "-------------------------------------------------------------")  
  MESSAGE(STATUS "Configuration SUPPRESSED for external module <util-jvm>")
  
ELSE (ZORBA_SUPPRESS_JAVA)
  INCLUDE(${CMAKE_CURRENT_SOURCE_DIR}/config/UtilJavaUse.cmake)

  MESSAGE(STATUS "")
  MESSAGE(STATUS "--- Module:   util-jvm    ---")

  FIND_PACKAGE (Zorba REQUIRED HINTS "${ZORBA_BUILD_DIR}")
  INCLUDE ("${Zorba_USE_FILE}")

  SET_CMAKE_MODULE_PATH()

  MESSAGE (STATUS "Looking for JNI")
  ZORBA_FIND_JNI()

  MESSAGE (STATUS "Looking for Java")
  FIND_PACKAGE(Java)

  IF (JNI_FOUND)
      SET (UTIL_JVM_VERSION 1.0)
      
      # Export this include directory by setting ZORBA_PROJECT_INCLUDE_DIRS
      SET (ZORBA_PROJECT_INCLUDE_DIRS "${CMAKE_CURRENT_SOURCE_DIR}/include")
      INCLUDE_DIRECTORIES ("${ZORBA_PROJECT_INCLUDE_DIRS}")
      INSTALL (FILES include/JavaVMSingleton.h DESTINATION include)

      # Set up a "use file" for other modules to import
      SET (ZORBA_PROJECT_USE_FILE
	"${CMAKE_CURRENT_SOURCE_DIR}/config/UtilJavaUse.cmake")
      ADD_SUBDIRECTORY ("src")

      # Set up configuration for install
      ADD_SUBDIRECTORY ("config")

      DONE_DECLARING_ZORBA_URIS ()
      
      MESSAGE(STATUS "")
      MESSAGE(STATUS "-------------------------------------------------------------")
      MESSAGE(STATUS "Configuration SUCCEEDED for external module <util-jvm>")

  ELSE (JNI_FOUND)
    MESSAGE(STATUS "Building Zorba without util-jvm module.")
    
    MESSAGE(STATUS "")
    MESSAGE(STATUS "-------------------------------------------------------------")  
    MESSAGE(STATUS "Configuration FAILED for external module <util-jvm>")
    
  ENDIF (JNI_FOUND)  
ENDIF(ZORBA_SUPPRESS_JAVA)

# Cache these variables for ease of packages depending on us
SET (JAVA_FOUND "${JAVA_FOUND}" CACHE BOOL "Whether Java was found" FORCE)
SET (JNI_FOUND "${JNI_FOUND}" CACHE BOOL "Whether JNI was found" FORCE)

MESSAGE(STATUS "+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++")