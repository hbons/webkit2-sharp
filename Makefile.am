SUBDIRS = sources sources/glue doc

pkgconfigdir = $(libdir)/pkgconfig
pkgconfig_DATA = $(srcdir)/webkit2-sharp-4.0.pc

EXTRA_DIST = $(srcdir)/webkit2-sharp-4.0.pc.in
DISTCLEANFILES = $(srcdir)/webkit2-sharp-4.0.pc

.PHONY: csproj
csproj:
	$(MAKE) -C $(srcdir)/sources csproj

