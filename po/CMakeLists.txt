include(FindGettext)
find_program(GETTEXT_XGETTEXT_EXECUTABLE xgettext)

set(DOMAIN ${PROJECT_NAME})
set(POT_FILE ${DOMAIN}.pot)
file(GLOB PO_FILES *.po)

add_custom_target(${POT_FILE} ALL
    COMMENT "Generating translation template"
    COMMAND ${INTLTOOL_EXTRACT} --update --type=gettext/ini
        --srcdir=${CMAKE_SOURCE_DIR} ${DESKTOP_FILE}.in.in
    COMMAND ${GETTEXT_XGETTEXT_EXECUTABLE} -o ${POT_FILE}
        --from-code=UTF-8
        --c++ --qt --add-comments=TRANSLATORS
        --keyword=tr --keyword=tr:1,2 --keyword=N_
        --package-name='${PROJECT}'
        --copyright-holder='Canonical Ltd.'
        -D ${CMAKE_CURRENT_SOURCE_DIR} ${I18N_SRC_FILES}
        -D ${CMAKE_CURRENT_BINARY_DIR} ${DESKTOP_FILE}.in.in.h
   COMMAND ${CMAKE_COMMAND} -E copy ${POT_FILE} ${CMAKE_CURRENT_SOURCE_DIR})

foreach(PO_FILE ${PO_FILES})
    get_filename_component(LANG ${PO_FILE} NAME_WE)
    gettext_process_po_files(${LANG} ALL PO_FILES ${PO_FILE})
    set(INSTALL_DIR ${CMAKE_INSTALL_LOCALEDIR}/${LANG}/LC_MESSAGES)
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${LANG}.gmo
            DESTINATION share/locale/${INSTALL_DIR}
            RENAME ${DOMAIN}.mo)
    message("installing po to ${INSTALL_DIR}")
endforeach(PO_FILE)
