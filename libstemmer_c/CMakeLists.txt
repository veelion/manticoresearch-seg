cmake_minimum_required ( VERSION 2.8 )

PROJECT ( stemmer )
FILE ( READ "${CMAKE_CURRENT_SOURCE_DIR}/mkinc.mak" _CONTENT )
# replace '\ ' into one big line
STRING ( REGEX REPLACE "\\\\\n" " " _CONTENT "${_CONTENT}" )
# escape ';' (if any)
STRING ( REGEX REPLACE ";" "\\\\;" _CONTENT "${_CONTENT}" )
# now replace lf into ';' (it makes list from the line)
STRING ( REGEX REPLACE "\n" ";" _CONTENT "${_CONTENT}" )
FOREACH ( LINE ${_CONTENT} )
	# skip comments (beginning with #)
	IF ( NOT "${LINE}" MATCHES "^#.*" )
		# parse 'name=value1 value2..." - extract the 'name' part
		STRING ( REGEX REPLACE "=.*$" "" _NAME "${LINE}" )
		# extract the list of values part
		STRING ( REGEX REPLACE "^.*=" "" _LIST "${LINE}" )
		# replace (multi)spaces into ';' (it makes list from the line)
		STRING ( REGEX REPLACE " +" ";" _LIST "${_LIST}" )
		# finally get our two variables
		IF ( "${_NAME}" MATCHES "snowball_sources" )
			SET ( _SOURCES "${_LIST}" )
		ELSEIF ( "${_NAME}" MATCHES "snowball_headers" )
			SET ( _HEADERS "${_LIST}" )
		ENDIF ()
	endif ()
endforeach ()
# all the sources parsed. Now just add the lib
add_library ( stemmer STATIC ${_SOURCES} ${_HEADERS} )
#SET (STEMMER_INCLUDE_DIR "${SPHINXSEARCH_SOURCE_DIR}/libstemmer_c/include" PARENT_SCOPE )
#SET (STEMMER_LIBRARIES stemmer PARENT_SCOPE)
#SET (STEMMER_FOUND TRUE PARENT_SCOPE)
#MARK_AS_ADVANCED(STEMMER_INCLUDE_DIR )
