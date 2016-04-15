RAW_API = $(srcdir)/$(ASSEMBLY_NAME)-api.raw
API = $(srcdir)/$(ASSEMBLY_NAME)-api.xml
METADATA = $(srcdir)/$(ASSEMBLY_NAME).metadata
ASSEMBLY_DLL_NAME = $(ASSEMBLY_NAME).dll
ASSEMBLY_CONFIG_NAME = $(ASSEMBLY_DLL_NAME).config
ASSEMBLY_CONFIG = $(srcdir)/$(ASSEMBLY_CONFIG_NAME)
OUTDIR = $(top_srcdir)/out
GLUEDIR = $(srcdir)/glue
SRC_FILES_XML = $(srcdir)/src_files.xml
CSPROJ_XSLT = $(srcdir)/csproj.xslt
GEN_SOURCES = $(srcdir)/generated.sources
CUSTOM_SOURCES = $(srcdir)/custom.sources

gapidir = $(GAPIXMLDIR)
gapi_DATA = $(API)

sources = $(shell sed 's|\#.*$$||g' $(CUSTOM_SOURCES) | grep -Ev '^[[:blank:]]*$$')
build_sources = $(srcdir)/AssemblyInfo.cs $(sources)

PROFILES = V_2_10

V_2_10_DEFINES = V_2_2,V_2_3,V_2_4,V_2_6,V_2_8,V_2_10

DLLS = $(foreach profile,$(PROFILES),$(OUTDIR)/$(profile)/$(ASSEMBLY_DLL_NAME))
DLL_CONFIGS = $(foreach profile,$(PROFILES),$(OUTDIR)/$(profile)/$(ASSEMBLY_CONFIG_NAME))
CS_PROJECTS = $(foreach profile,$(PROFILES),$(srcdir)/$(ASSEMBLY_NAME)-$(profile).csproj)

CLEANFILES = $(DLLS) $(DLL_CONFIGS) $(API) $(SRC_FILES_XML)

DISTCLEANFILES = $(srcdir)/AssemblyInfo.cs

noinst_DATA = $(DLLS) $(DLL_CONFIGS)

EXTRA_DIST = \
	$(RAW_API) \
	$(CUSTOM_SOURCES) \
	$(sources) \
	$(METADATA) \
	$(srcdir)/AssemblyInfo.cs.in \
	$(srcdir)/$(ASSEMBLY_NAME).snk \
	$(ASSEMBLY_CONFIG)

$(API): $(RAW_API) $(METADATA)
	cp $(RAW_API) $(API)
	chmod u+w $(API)
	$(GAPI_FIXUP) --api=$(API) --metadata=$(METADATA)

$(GEN_SOURCES): $(API)
	$(GAPI_CODEGEN) --generate $(API)  $(GLIB_SHARP_CFLAGS) $(GTK_SHARP_CFLAGS) $(GIO_SHARP_CFLAGS) $(GDK_SHARP_CFLAGS) \
		--outdir=$(srcdir)/generated \
		--glue-filename=$(GLUEDIR)/generated.c --gluelib-name=libwebkit2sharpglue-2.10.9.so \
		--glue-includes=webkit2/webkit2.h, --gapidir=$(gapidir) \
	 	--assembly-name=$(ASSEMBLY_NAME) && \
	find $(srcdir)/generated/ -type f -name "*.cs" > $(GEN_SOURCES)

$(SRC_FILES_XML): $(GEN_SOURCES) $(CUSTOM_SOURCES)
	echo "<SourceFiles>" > $(SRC_FILES_XML)
	echo $(build_sources) | tr '[:blank:]' '\n' | cat - $(GEN_SOURCES) | \
		grep -Ev '^[[:blank:]]*$$' | sed 's|\/|\\|g' | sed 's|^\.\\||g' | \
		sed 's|\(.*\)|  \<Compile Include="\1" \/\>|g' >> $(SRC_FILES_XML)
	echo "</SourceFiles>" >> $(SRC_FILES_XML)

$(srcdir)/$(ASSEMBLY_NAME)-%.csproj: $(SRC_FILES_XML) $(CSPROJ_XSLT)
	cp $@ $@.tmp
	$(XSLTPROC) --stringparam srcListPath $(SRC_FILES_XML) $(CSPROJ_XSLT) $@.tmp | \
		$(UNIX2DOS) > $@
	$(MDTOOL) project-export -f:'MSBuild (Visual Studio 2012)' $@
	rm $@.tmp

.PHONY: csproj
csproj: $(CS_PROJECTS)

$(OUTDIR)/%/$(ASSEMBLY_DLL_NAME): $(build_sources) $(GEN_SOURCES)
	mkdir -p $(@D)
	$(CSC) -nowarn:169 -unsafe -target:library  $(GLIB_SHARP_LIBS) $(GTK_SHARP_LIBS) $(GIO_SHARP_LIBS) $(GDK_SHARP_LIBS) \
		$(build_sources) -out:$@ -d:$($*_DEFINES) @$(GEN_SOURCES)

$(OUTDIR)/%/$(ASSEMBLY_CONFIG_NAME): $(ASSEMBLY_CONFIG)
	mkdir -p $(@D)
	cp $(ASSEMBLY_CONFIG) $@

clean-local:
	test -e $(GEN_SOURCES) && xargs rm -f < $(GEN_SOURCES) || true
	rm -f $(GEN_SOURCES)

install-data-local: $(DLLS)
	@for p in $?; do \
		$(GACUTIL) /i $$p /f $(GACUTIL_FLAGS) || exit 1; \
	done;

uninstall-local:
	$(GACUTIL) /u $(ASSEMBLY_NAME) /f $(GACUTIL_FLAGS) || exit 1;

