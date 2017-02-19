AC_INIT(webkit2-sharp, [2.10.9])
AC_CONFIG_SRCDIR([README])
AC_CANONICAL_TARGET
AM_INIT_AUTOMAKE
AM_MAINTAINER_MODE
AC_PROG_INSTALL
LT_INIT

dnl Package settings
ASSEMBLY_COMPANY=""
ASSEMBLY_COPYRIGHT="2012 Andreia Gaita"
ASSEMBLY_DESCRIPTION="webkit2 bindings for Mono"
ASSEMBLY_NAME="webkit2-sharp"
ASSEMBLY_NAMESPACE="WebKit2"
ASSEMBLY_PC="webkit2-sharp-4.0"
ASSEMBLY_SHORTNAME="webkit2"
ASSEMBLY_TITLE="WebKit2#"
ASSEMBLY_VERSION="2.10.9"
AC_SUBST([ACLOCAL_AMFLAGS], ["-I m4 \${ACLOCAL_FLAGS}"])

AC_SUBST(ASSEMBLY_NAME)
AC_SUBST(ASSEMBLY_VERSION)
AC_SUBST(ASSEMBLY_NAMESPACE)
AC_SUBST(ASSEMBLY_SHORTNAME)
AC_SUBST(ASSEMBLY_TITLE)
AC_SUBST(ASSEMBLY_DESCRIPTION)
AC_SUBST(ASSEMBLY_COMPANY)
AC_SUBST(ASSEMBLY_COPYRIGHT)
AC_SUBST(ASSEMBLY_PC)

dnl Check for pkg-config
AC_PATH_PROG(PKG_CONFIG, pkg-config, no)
if test "x$PKG_CONFIG" = "xno"; then
	AC_MSG_ERROR(['pkg-config' is not in your PATH.])
fi

dnl Check for Mono
PKG_CHECK_MODULES(MONO_DEPENDENCY, mono >= 1.0, has_mono=true, has_mono=false)

if test "x$has_mono" = "xtrue"; then
	AC_PATH_PROG(RUNTIME, mono, no)
	AC_PATH_PROG(CSC, mcs, no)
	LIB_PREFIX=.so
	LIB_SUFFIX=
else
	AC_PATH_PROG(CSC, csc.exe, no)
	if test x$CSC = "xno"; then
		AC_MSG_ERROR([You need to install either mono or .Net])
	else
		RUNTIME=
		LIB_PREFIX=
		LIB_SUFFIX=.dylib
	fi
fi

CS="C#"
if test "x$CSC" = "xno" ; then
    AC_MSG_ERROR([No $CS compiler found])
fi

AC_PATH_PROG(GACUTIL, gacutil, no)
if test "x$GACUTIL" = "xno" ; then
	AC_MSG_ERROR([No gacutil tool found])
fi

GACUTIL_FLAGS="/package $ASSEMBLY_NAME /root "'$(DESTDIR)$(prefix)/lib'
                                     
AC_SUBST(RUNTIME)
AC_SUBST(CSC)
AC_SUBST(GACUTIL)
AC_SUBST(GACUTIL_FLAGS)
AC_SUBST(LIB_PREFIX)
AC_SUBST(LIB_SUFFIX)

dnl Check for gtk-sharp
PKG_CHECK_MODULES(GTK_SHARP, gtk-sharp-3.0)
AC_SUBST(GTK_SHARP_CFLAGS)
AC_SUBST(GTK_SHARP_LIBS)
gtksharp_prefix=`pkg-config --variable=prefix gtk-sharp-3.0`
GAPIXMLDIR=`pkg-config --variable=gapidir gtk-sharp-3.0`
AC_SUBST(gtksharp_prefix)
AC_SUBST(GAPIXMLDIR)

dnl Check for gapi
AC_PATH_PROG(GAPI_FIXUP, gapi3-fixup, no)
if test "x$GAPI_FIXUP" = "xno"; then
	AC_MSG_CHECKING(for gapi3-fixup.exe)
	GAPI_FIXUP=`which gapi3-fixup.exe || echo "no"`
	if test "x$GAPI_FIXUP" = "xno" ; then
		AC_MSG_ERROR(['gapi3_fixup'/'gapi3-fixup.exe' not found.])
	fi
	AC_MSG_RESULT($GAPI_FIXUP)
	GAPI_FIXUP="$MONO $GAPI_FIXUP"
fi
AC_SUBST(GAPI_FIXUP)

AC_PATH_PROG(GAPI_CODEGEN, gapi3-codegen, no)
if test "x$GAPI_CODEGEN" = "xno"; then
	AC_MSG_CHECKING(for gapi3_codegen.exe)
	GAPI_CODEGEN=`which gapi3_codegen.exe || echo "no"`
	if test "x$GAPI_CODEGEN" = "xno" ; then
		AC_MSG_ERROR(['gapi3-codegen'/'gapi3_codegen.exe' not found.])
	fi
	AC_MSG_RESULT([$GAPI_CODEGEN])
	GAPI_CODEGEN="$MONO $GAPI_CODEGEN"
fi
AC_SUBST(GAPI_CODEGEN)

AC_PATH_PROG(XSLTPROC, xsltproc, no)
if test "x$XSLTPROC" = "xno" ; then
	AC_MSG_ERROR([xsltproc not found])
fi
AC_SUBST(XSLTPROC)

AC_PATH_PROG(UNIX2DOS, unix2dos, [cat -])
if test "x$UNIX2DOS" = "xcat -" ; then
	AC_MSG_RESULT([unix2dos not found, using cat])
fi
AC_SUBST(UNIX2DOS)

AC_PATH_PROG(MDTOOL, mdtool, echo)
if test "x$MDTOOL" = "xecho" ; then
	AC_MSG_RESULT([mdtool not found, csproj conversion won't run])
fi
AC_SUBST(MDTOOL)

dnl Check for monodoc
AC_PATH_PROG(MDASSEMBLER, mdassembler, no)
AC_PATH_PROG(MONODOCER, monodocer, no)
if test "x$MONODOCER" = "xno" -o "x$MDASSEMBLER" = "xno"; then
	enable_monodoc=no
	doc_sources_dir=
else
	enable_monodoc=yes
	doc_sources_dir="`pkg-config --variable=sourcesdir monodoc`"
fi
AC_SUBST(MDASSEMBLER)
AC_SUBST(MONODOCER)

AM_CONDITIONAL(ENABLE_MONODOC, test "x$enable_monodoc" = "xyes")
dnl Check for WebKit2
PKG_CHECK_MODULES(WEBKIT2, webkit2gtk-4.0)
webkit2_prefix=/usr
AC_SUBST(webkit2_prefix)
AC_SUBST(WEBKIT2_LIBS)
AC_SUBST(WEBKIT2_CFLAGS)

dnl extra m4 calls
CHECK_GLIB_SHARP
CHECK_GIO_SHARP
CHECK_GDK_SHARP


AC_CONFIG_FILES([
	Makefile
	sources/Makefile
	sources/glue/Makefile
	sources/AssemblyInfo.cs
	webkit2-sharp-4.0.pc
	doc/Makefile
])
AC_CONFIG_COMMANDS([default],[[ echo timestamp > stamp-h ]],[[]])
AC_OUTPUT

echo ""
echo "Configuration summary"
echo "---------------------"
echo ""
echo "   * Installation prefix: $prefix"
echo "   * compiler: $CSC"
echo "   * Documentation:  ($MONODOC)"
echo ""
