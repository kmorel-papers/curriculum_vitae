CMAKE_MINIMUM_REQUIRED(VERSION 3.23)

PROJECT(CURRICULUM_VITAE NONE)
MARK_AS_ADVANCED(
  CMAKE_BUILD_TYPE
  CMAKE_INSTALL_PREFIX
  EXECUTABLE_OUTPUT_PATH
  LIBRARY_OUTPUT_PATH
  )

INCLUDE(UseLATEX.cmake)

find_package(Python)

function(add_parse_bib bib_type)
  add_custom_command(
    OUTPUT ${CMAKE_CURRENT_SOURCE_DIR}/${bib_type}.tex
    COMMAND ${Python_EXECUTABLE}
      ${CMAKE_CURRENT_SOURCE_DIR}/parse-bib.py
      ${CMAKE_CURRENT_SOURCE_DIR}/${bib_type}.bib
      ${CMAKE_CURRENT_SOURCE_DIR}/${bib_type}.tex
    DEPENDS
      ${CMAKE_CURRENT_SOURCE_DIR}/${bib_type}.bib
      ${CMAKE_CURRENT_SOURCE_DIR}/parse-bib.py
  )
endfunction()

set(pub_lists
  phd
  journal
  workshop
  nopeer
  poster
  presentation
  panel
  )

if (Python_FOUND)
  foreach(bibfile IN LISTS pub_lists)
    add_parse_bib(${bibfile})
  endforeach()

  add_custom_command(
    OUTPUT ${CMAKE_CURRENT_SOURCE_DIR}/professional-activities.tex
    COMMAND ${Python_EXECUTABLE}
      ${CMAKE_CURRENT_SOURCE_DIR}/yaml2activities.py
      ${CMAKE_CURRENT_SOURCE_DIR}/professional-activities.yaml
      ${CMAKE_CURRENT_SOURCE_DIR}/professional-activities.tex
    DEPENDS
      ${CMAKE_CURRENT_SOURCE_DIR}/professional-activities.yaml
      ${CMAKE_CURRENT_SOURCE_DIR}/yaml2activities.py
    )
endif()

list(TRANSFORM pub_lists APPEND ".tex")

ADD_LATEX_DOCUMENT(curriculum_vitae.tex
  INPUTS ${pub_lists} professional-activities.tex
  )
