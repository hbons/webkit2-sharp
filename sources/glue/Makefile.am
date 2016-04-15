lib_LTLIBRARIES = libwebkit2sharpglue-2.10.9.la

libwebkit2sharpglue_2_10_9_la_SOURCES =

nodist_libwebkit2sharpglue_2_10_9_la_SOURCES = generated.c

libwebkit2sharpglue_2_10_9_la_LDFLAGS = -module -avoid-version -no-undefined

libwebkit2sharpglue_2_10_9_la_LIBADD = $(WEBKIT2_LIBS)

INCLUDES = $(WEBKIT2_CFLAGS) -I$(top_srcdir)

libwebkit2sharpglue.dll: $(libwebkit2sharpglue_2_10_9_la_OBJECTS) libwebkit2sharpglue.rc libwebkit2sharpglue.def
	./build-dll libwebkit2sharpglue $(VERSION)

CLEANFILES = lib*.a lib*.dll

EXTRA_DIST =
