## Makefile.am for Onigmo
encdir    = $(top_srcdir)/enc
sampledir = $(top_srcdir)/sample
libname = libonigmo.la

PYTHON = @python_prog@

ACLOCAL_AMFLAGS = -I m4
AM_CFLAGS = -Wall
AM_CPPFLAGS = -I$(top_srcdir) -I$(includedir) -I$(encdir)/unicode

SUBDIRS = . sample

include_HEADERS = onigmo.h onigmognu.h onigmoposix.h
lib_LTLIBRARIES = $(libname)

libonigmo_la_SOURCES = regint.h regparse.h regenc.h st.h \
	regerror.c regparse.c regext.c regcomp.c regexec.c reggnu.c \
	regenc.c regsyntax.c regtrav.c regversion.c st.c \
	regposix.c regposerr.c \
	enc/unicode.c enc/ascii.c enc/utf_8.c \
	enc/utf_16be.c enc/utf_16le.c \
	enc/utf_32be.c enc/utf_32le.c \
	enc/unicode/casefold.h enc/unicode/name2ctype.h \
	enc/euc_jp.c enc/shift_jis.c enc/shift_jis.h \
	enc/windows_31j.c \
	enc/jis/props.h enc/jis/props.kwd \
	enc/iso_8859.h enc/iso_8859_1.c \
	enc/iso_8859_2.c  enc/iso_8859_3.c \
	enc/iso_8859_4.c  enc/iso_8859_5.c \
	enc/iso_8859_6.c  enc/iso_8859_7.c \
	enc/iso_8859_8.c  enc/iso_8859_9.c \
	enc/iso_8859_10.c enc/iso_8859_11.c \
	enc/iso_8859_13.c enc/iso_8859_14.c \
	enc/iso_8859_15.c enc/iso_8859_16.c \
	enc/euc_tw.c enc/euc_kr.c enc/big5.c \
	enc/gb18030.c enc/koi8_r.c enc/koi8_u.c \
	enc/windows_1250.c enc/windows_1251.c \
	enc/windows_1252.c enc/windows_1253.c \
	enc/windows_1254.c enc/windows_1257.c

libonigmo_la_LDFLAGS = -version-info $(LTVERSION) -no-undefined

EXTRA_DIST = .gitignore onigmo.pc.in HISTORY README.ja README.md \
	doc/API doc/API.ja doc/RE doc/RE.ja doc/FAQ doc/FAQ.ja \
	doc/UnicodeProps.txt \
	tool/.gitignore tool/Makefile tool/case-folding.rb \
	tool/convert-jis-props.sh \
	tool/enc-unicode.rb tool/download-ucd.sh tool/update-doc.py \
	win32/Makefile win32/Makefile.mingw win32/config.h win32/testc.c \
	win32/makedef.py win32/onigmo.rc \
	$(encdir)/mktable.c \
	test.rb testconv.rb testconvu.rb \
	onigmo.py testpy.py .editorconfig

bin_SCRIPTS = onigmo-config

onigmo-config: onigmo-config.in

do_subst = sed                                          \
      -e 's,[@]datadir[@],$(datadir),g'                 \
      -e 's,[@]datarootdir[@],$(datarootdir),g'         \
      -e 's,[@]PACKAGE_VERSION[@],$(PACKAGE_VERSION),g' \
      -e 's,[@]prefix[@],$(prefix),g'                   \
      -e 's,[@]exec_prefix[@],$(exec_prefix),g'         \
      -e 's,[@]libdir[@],$(libdir),g'                   \
      -e 's,[@]includedir[@],$(includedir),g'

onigmo.pc: $(srcdir)/onigmo.pc.in Makefile
	$(do_subst) < $(srcdir)/onigmo.pc.in > $(@)

pkgconfigdir   = $(libdir)/pkgconfig
pkgconfig_DATA = onigmo.pc
CLEANFILES = onigmo.pc

# character-types-table source generator
mktable: $(encdir)/mktable.c $(srcdir)/regenc.h
	$(CC) -I$(top_srcdir) -o mktable $(encdir)/mktable.c


# TEST
TESTS = testc testp testcu

check_PROGRAMS = testc testp testcu \
	test_enc_utf8

test: atest pytest
	$(MAKE) -C sample test

atest: testc$(EXEEXT) testp$(EXEEXT) testcu$(EXEEXT) \
    test_enc_utf8$(EXEEXT)
	@echo "[Onigmo API, ASCII/EUC-JP check]"
	@$(top_builddir)/testc  | grep RESULT
	@echo "[POSIX API, ASCII/EUC-JP check]"
	@$(top_builddir)/testp  | grep RESULT
	@echo "[Onigmo API, UTF-16 check]"
	@$(top_builddir)/testcu | grep RESULT
	@echo "[UTF-8 encoding check]"
	@$(top_builddir)/test_enc_utf8 | grep RESULT

testc_SOURCES = testc.c
testc_LDADD = libonigmo.la

testp_SOURCES = testc.c
testp_LDADD = libonigmo.la
testp_CFLAGS = -DPOSIX_TEST

testcu_SOURCES = testu.c
testcu_LDADD = libonigmo.la

test_enc_utf8_SOURCES = test_enc_utf8.c
test_enc_utf8_LDADD = libonigmo.la

#$(srcdir)/testc.c: $(srcdir)/test.rb $(srcdir)/testconv.rb
$(srcdir)/testc.c:
	ruby -Ke $(srcdir)/testconv.rb < $(srcdir)/test.rb > $@

#$(srcdir)/testu.c: $(srcdir)/test.rb $(srcdir)/testconvu.rb
$(srcdir)/testu.c:
	ruby -Ke $(srcdir)/testconvu.rb $(srcdir)/test.rb > $@

#$(srcdir)/win32/testc.c: $(srcdir)/test.rb $(srcdir)/testconv.rb
$(srcdir)/win32/testc.c:
	ruby -Ke $(srcdir)/testconv.rb -win < $(srcdir)/test.rb | iconv -f euc-jp -t cp932 | sed -e "s/$$/\r/" > $@

# Python TEST
pytest:
	LD_LIBRARY_PATH=.libs $(PYTHON) $(srcdir)/testpy.py EUC-JP
	LD_LIBRARY_PATH=.libs $(PYTHON) $(srcdir)/testpy.py SJIS
	LD_LIBRARY_PATH=.libs $(PYTHON) $(srcdir)/testpy.py UTF-8
	LD_LIBRARY_PATH=.libs $(PYTHON) $(srcdir)/testpy.py UTF-16LE
	LD_LIBRARY_PATH=.libs $(PYTHON) $(srcdir)/testpy.py UTF-16BE
	LD_LIBRARY_PATH=.libs $(PYTHON) $(srcdir)/testpy.py UTF-32LE
	LD_LIBRARY_PATH=.libs $(PYTHON) $(srcdir)/testpy.py UTF-32BE


# lcov

# Collect coverage data and write to coverage.info, then create HTML output
# in the coverage directory.
lcov:
	if [ ! -f $(builddir)/enc/jis/props.kwd ]; then mkdir -p $(builddir)/enc/jis; cp -p $(srcdir)/enc/jis/props.kwd $(builddir)/enc/jis/props.kwd; fi
	lcov -c -d .libs -d enc/.libs -o coverage.info
	genhtml -o coverage coverage.info

# Clear coverage data.
lcov-clear:
	lcov -d . -z

## END OF FILE
