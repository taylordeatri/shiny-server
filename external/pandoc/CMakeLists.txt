project(pandoc)

set(PANDOC_VERSION 1.17.2)

get_filename_component(PANDOC_PREFIX 
                       "${CMAKE_CURRENT_SOURCE_DIR}/../../ext/pandoc"
                       REALPATH)

set(PANDOC_SHA1 889343ed65691dc172c6b271fe73d8b72e7cb19e)

set(MY_DOWNLOAD_PATH "${CMAKE_CURRENT_BINARY_DIR}/pandoc-${PANDOC_VERSION}.zip")

if (NOT EXISTS "${MY_DOWNLOAD_PATH}")
    file(DOWNLOAD "http://s3.amazonaws.com/rstudio-buildtools/pandoc-${PANDOC_VERSION}.zip" "${MY_DOWNLOAD_PATH}" 
    EXPECTED_MD5 9d53413f3d69fbf0bd369f5cebb7dc8b)
endif()

execute_process(
    COMMAND mkdir -p ${PANDOC_PREFIX}
    DEPENDS "${MY_DOWNLOAD_PATH}")

# Detect distribution
if(EXISTS "/etc/debian_version")
  set(DISTRIBUTION debian)
    # Then we'll want the dynamic versions
    execute_process(
        COMMAND unzip -o -u -j ${MY_DOWNLOAD_PATH} pandoc-${PANDOC_VERSION}/linux/${DISTRIBUTION}/x86_64/pandoc -d ${PANDOC_PREFIX}
        DEPENDS "${MY_DOWNLOAD_PATH}")
    
    execute_process(
        COMMAND unzip -o -u -j ${MY_DOWNLOAD_PATH} pandoc-${PANDOC_VERSION}/linux/${DISTRIBUTION}/x86_64/pandoc-citeproc -d ${PANDOC_PREFIX}
        DEPENDS "${MY_DOWNLOAD_PATH}")
endif()

if(EXISTS "/etc/redhat-release")
  set(DISTRIBUTION rpm)

  execute_process(
    COMMAND sed -nr "s/.*release\\ ([0-9]).*/\\1/p" /etc/redhat-release
    OUTPUT_VARIABLE REDHAT_VER)

  if (${REDHAT_VER} EQUAL "5")
    # Need the dynamic versions of pandoc.
    execute_process(
        COMMAND unzip -o -u -j ${MY_DOWNLOAD_PATH} pandoc-${PANDOC_VERSION}/linux/${DISTRIBUTION}/x86_64/pandoc -d ${PANDOC_PREFIX}
        DEPENDS "${MY_DOWNLOAD_PATH}")
    
    execute_process(
        COMMAND unzip -o -u -j ${MY_DOWNLOAD_PATH} pandoc-${PANDOC_VERSION}/linux/${DISTRIBUTION}/x86_64/pandoc-citeproc -d ${PANDOC_PREFIX}
        DEPENDS "${MY_DOWNLOAD_PATH}")
  endif()
endif()

execute_process(
  COMMAND mkdir -p ${PANDOC_PREFIX}/static
  DEPENDS "${MY_DOWNLOAD_PATH}")

execute_process(
  COMMAND unzip -o -u -j ${MY_DOWNLOAD_PATH} pandoc-${PANDOC_VERSION}/linux/debian/x86_64/pandoc -d ${PANDOC_PREFIX}/static
  DEPENDS "${MY_DOWNLOAD_PATH}")

execute_process(
    COMMAND unzip -o -u -j ${MY_DOWNLOAD_PATH} pandoc-${PANDOC_VERSION}/linux/debian/x86_64/pandoc-citeproc -d ${PANDOC_PREFIX}/static
    DEPENDS "${MY_DOWNLOAD_PATH}")
