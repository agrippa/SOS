# -*- autoconf -*-
#
# Copyright 2011 Sandia Corporation. Under the terms of Contract
# DE-AC04-94AL85000 with Sandia Corporation, the U.S.  Government
# retains certain rights in this software.
#
# Copyright (c) 2016 Intel Corporation. All rights reserved.
# This software is available to you under the BSD license.
#
# This file is part of the Sandia OpenSHMEM software package. For license
# information, see the LICENSE file in the top level directory of the
# distribution.

dnl Init Autoconf/Automake/Libtool

AC_INIT([Sandia OpenSHMEM], [1.3.1], [https://github.com/Sandia-OpenSHMEM/SOS])
AC_PREREQ([2.60])
AC_CONFIG_AUX_DIR([config])
AC_CONFIG_MACRO_DIR([config])

AM_INIT_AUTOMAKE([foreign check-news color-tests parallel-tests dist-bzip2 no-define subdir-objects tar-ustar 1.11 -Wall -Werror])

m4_ifndef([AS_VAR_COPY],
    [m4_define([AS_VAR_COPY],
       [AS_LITERAL_IF([$1[]$2], [$1=$$2], [eval $1=\$$2])])])

dnl Work around change in how the rules were implemented between
dnl 1.11.2 and 1.11.3.
AM_SILENT_RULES([yes])
AS_IF([test -z "$AM_V"],
  [AM_V='$(V)'
   AM_DEFAULT_V='$(AM_DEFAULT_VERBOSITY)'
   AC_SUBST([AM_V])
   AC_SUBST([AM_DEFAULT_V])])

dnl information on the package
AC_CONFIG_HEADERS([src/config.h])

AC_CANONICAL_HOST

AC_ARG_ENABLE([picky],
    [AC_HELP_STRING([--enable-picky],
                    [Enable developer-level compiler pickyness when building (default: disabled)])])

AC_ARG_ENABLE([debug],
    [AC_HELP_STRING([--enable-debug],
                    [Include debugging symbols in the build (default: disabled)])])

AC_ARG_ENABLE([error-checking],
    [AC_HELP_STRING([--enable-error-checking],
                    [Enable error checking for SHMEM calls (default:disabled)])])
AS_IF([test "$enable_error_checking" = "yes"], [AC_DEFINE([ENABLE_ERROR_CHECKING], [1], [Enable argument checking])])
AM_CONDITIONAL([ENABLE_ERROR_CHECKING], [test "$enable_error_checking" = "yes"])

AC_ARG_ENABLE([hard-polling],
    [AC_HELP_STRING([--enable-hard-polling],
                    [Enable hard polling of wait calls (default:disabled)])])
AS_IF([test "$enable_hard_polling" = "yes"], [AC_DEFINE([ENABLE_HARD_POLLING], [1], [Enable hard polling])])

AC_ARG_ENABLE([nonblocking-fence],
    [AC_HELP_STRING([--enable-nonblocking-fence],
                    [When total data ordering is not available, make shmem_fence a non-blocking operation.  shmem_fence will return immediately, but the next communication call will block until all previous communications finish. (default:disabled)])])
AS_IF([test "$enable_nb_fence" = "yes"], [AC_DEFINE([ENABLE_NONBLOCKING_FENCE], [1], [Enable non-blocking fence])])


AC_ARG_WITH([total-data-ordering],
    [AC_HELP_STRING([--with-total-data-ordering],
       [Configure handling of total data ordering option.  "no" or "never" to build with the assumption total data ordering will never be available.  "yes" or "always" to build with the assumption total data ordering will always be available (if not, applications will abort in start_pes()).  "check" to build with no assumptions, which may lead to a slight performance decrease on high performance networks.  (default: disabled)])])
AS_CASE([x$with_total_data_ordering],
  [xyes|xalways], [ordering=1; msg="assume always available"],
  [xno|xnever|x], [ordering=0; msg="assume never available"],
  [xcheck], [ordering=2; msg="runtime check"],
  [AC_MSG_ERROR([Unknown ordering requirement $with_total_data_ordering])])
AC_DEFINE_UNQUOTED([WANT_TOTAL_DATA_ORDERING], [$ordering], [0 if never check for total data ordering, 1 if always assume total data ordering, 2 to check])
AC_MSG_CHECKING([For total data ordering behavior])
AC_MSG_RESULT([$msg])

AC_ARG_ENABLE([threads],
    [AC_HELP_STRING([--enable-threads],
                    [Enable threading support (default:disabled)])])
AS_IF([test "$enable_threads" = "yes"], [
       OPAL_CONFIG_POSIX_THREADS(HAVE_POSIX_THREADS=1, HAVE_POSIX_THREADS=0)
       AC_MSG_CHECKING([for working POSIX threads package])
       if test "$HAVE_POSIX_THREADS" = "1" ; then
           AC_MSG_RESULT([yes])
       else
           AC_MSG_RESULT([no])
       fi
       AC_DEFINE([ENABLE_THREADS], [1], [Enable threads])
       ])
AM_CONDITIONAL([HAVE_PTHREADS], [test "$HAVE_POSIX_THREADS" = "1"])

AC_ARG_ENABLE([pthread-mutexes],
    [AC_HELP_STRING([--enable-pthread-mutexes],
                    [Use pthread mutexes to support threading (used in conjunction with --enable-threads) (default:disabled)])])
AS_IF([test "$enable_pthread_mutexes" = "yes"], [AC_DEFINE([ENABLE_PTHREAD_MUTEX], [1], [Enable pthread mutexes])])

AC_ARG_ENABLE([remote-virtual-addressing],
    [AC_HELP_STRING([--enable-remote-virtual-addressing],
                    [Enable optimizations assuming the symmetric heap is always symmetric with regards to virtual address.  This may cause applications to abort during start_pes() if such a symmetric heap can not be created, but will reduce the instruction count for some operations.  This optimization also requires that the Portals 4 implementation support BIND_INACCESSIBLE on LEs. (default: disabled)])])
AS_IF([test "$enable_remote_virtual_addressing" = "yes"],
      [AC_DEFINE([ENABLE_REMOTE_VIRTUAL_ADDRESSING], [1], [If defined, the implementation will use one LE/PT to cover all of the symmetric data and heap, setup so that no offset transformation is needed on the target virtual address.])])

AC_ARG_ENABLE([lengthy-tests],
    [AC_HELP_STRING([--enable-lengthy-tests],
                    [Enable long running tests in the test suite (default: disabled)])])
AM_CONDITIONAL([ENABLE_LENGTHY_TESTS], [test "$enable_lengthy_tests" = "yes"])

AC_ARG_ENABLE([fortran],
    [AC_HELP_STRING([--disable-fortran],
                    [Disable building the Fortran bindings (default: enabled)])])

AC_ARG_ENABLE([cxx],
    [AC_HELP_STRING([--disable-cxx],
                    [Disable building the C++ bindings (default: enabled)])])

AC_ARG_ENABLE([libtool-wrapper],
  [AS_HELP_STRING([--disable-libtool-wrapper],
    [Disable use of libtool executable wrappers for tests (default: enabled)])])
AS_IF([test "$enable_libtool_wrapper" = "no"], [LIBTOOL_WRAPPER_LDFLAGS="-no-install"], [LIBTOOL_WRAPPER_LDFLAGS=])
AC_SUBST([LIBTOOL_WRAPPER_LDFLAGS])

AC_ARG_ENABLE([rpath],
  [AS_HELP_STRING([--disable-rpath],
    [Disable use of rpath in compiler wrappers and building tests (default: enabled)])])
AS_IF([test "$enable_rpath" = "no"], [RPATH_LDFLAGS=], [RPATH_LDFLAGS="-Wl,-rpath -Wl,$libdir"])


AC_ARG_ENABLE([profiling],
    [AC_HELP_STRING([--enable-profiling],
                    [Enable shmem call profiling (default:disabled)])])
AS_IF([test "$enable_profiling" = "yes"], [AC_DEFINE([ENABLE_PROFILING], [1], [Enable shmem call profiling])])

AC_ARG_ENABLE([long-fortran-header],
    [AC_HELP_STRING([--enable-long-fortran-header],
                    [Enable long Fortran header, including all function declarations (default:disabled)])])
AS_IF([test "$enable_long_fortran_header" = "yes"], [FORTRAN_LONG_HEADER=" "], [FORTRAN_LONG_HEADER="!"])
AC_SUBST([FORTRAN_LONG_HEADER])
AM_CONDITIONAL([HAVE_LONG_FORTRAN_HEADER], [test "$enable_long_fortran_header" = "yes"])

AC_ARG_ENABLE([mr-scalable],
    [AC_HELP_STRING([--disable-mr-scalable],
                    [Disable the use of scalable memory regions in the OFI transport.  Required by some providers. (default: enabled)])])
AS_IF([test "$enable_mr_scalable" != "no"],
      [AC_DEFINE([ENABLE_MR_SCALABLE], [1], [If defined, the OFI transport will use FI_MR_SCALABLE.  Otherwise, FI_MR_BASIC is used.])])

AC_ARG_ENABLE([av-map],
    [AC_HELP_STRING([--enable-av-map],
                    [enable av-map instead of av-table in the OFI transport. (default: disabled)])])
AS_IF([test "$enable_av_map" = "yes"],
      [AC_DEFINE([USE_AV_MAP], [1], [If defined, the OFI transport will use FI_AV_MAP.  Otherwise, FI_AV_TABLE is used.])])

dnl check for programs

AC_PROG_CC
AM_PROG_CC_C_O
AC_PROG_CC_C99
AC_C_INLINE
AC_PROG_CXX dnl required even with --disable-cxx due to automake conditionals
m4_ifdef([AM_PROG_AR],[AM_PROG_AR])
AC_PATH_PROG([PERL],[perl])

AC_PATH_PROG([M4], [m4], [false])
AS_IF([test "$M4" = "false"],
      [AC_MSG_ERROR([Could not find 'm4' macro processor])])

if test "$enable_fortran" != "no" ; then
  AC_PROG_FC
else
  FC=
fi
if test "$FC" != "" ; then
  AC_FC_WRAPPERS
fi

AX_GCC_FUNC_ATTRIBUTE([noreturn])
AX_GCC_BUILTIN([__builtin_trap])

if test "$enable_picky" = "yes" -a "$GCC" = "yes" ; then
  CFLAGS="$CFLAGS -Wall -Wno-long-long -Wmissing-prototypes -Wstrict-prototypes -Wcomment -pedantic"
else
  CFLAGS="$CFLAGS -Wall"
fi

if test "$enable_debug" = "yes" ; then
  CFLAGS="$CFLAGS -g"
fi

AC_CACHE_SAVE

AC_SEARCH_LIBS([clock_gettime], [rt],
               [AC_DEFINE([HAVE_CLOCK_GETTIME], [1], [define if clock_gettime is available]) ])

dnl check for libraries

OMPI_CHECK_PORTALS4([portals4],
    [AC_DEFINE([USE_PORTALS4], [1], [Define if Portals 4 transport active])
     transport="portals4"
     transport_portals4="yes"],
    [transport_portals4="no"])
AM_CONDITIONAL([USE_PORTALS4], [test "$transport_portals4" = "yes"])

OMPI_CHECK_OFI([ofi],
    [AC_DEFINE([USE_OFI], [1], [Define if OFI transport active])
     transport="ofi"
     transport_ofi="yes"],
    [transport_ofi="no"])
AM_CONDITIONAL([USE_OFI], [test "$transport_ofi" = "yes"])

SANDIA_CHECK_XPMEM(
    [AC_DEFINE([USE_XPMEM], [1], [Define if XPMEM transport is active])
     transport_xpmem="yes"],
    [transport_xpmem="no"])
AM_CONDITIONAL([USE_XPMEM], [test "$transport_xpmem" = "yes"])

CHECK_CMA(
   [AC_DEFINE([USE_CMA], [1], [Define if Cross Memory Attach transport is active])
    transport_cma="yes"],
   [transport_cma="no"])
AM_CONDITIONAL([USE_CMA], [test "$transport_cma" = "yes"])

AS_IF([test "$transport_xpmem" = "yes" -o "$transport_cma" = "yes"],
      [AC_DEFINE([USE_ON_NODE_COMMS], [1], [Define if any on-node comm transport is available])
       AC_DEFINE([ENABLE_HARD_POLLING], [1], [Enable hard polling])
      ])

AC_ARG_ENABLE([pmi-simple], [AC_HELP_STRING([--enable-pmi-simple],
			[build and run shmem-pmi support])])
AS_IF([test "$enable_pmi_simple" = "yes"],
     [TEST_RUNNER='$(abs_top_builddir)/src/oshrun -n $(NPROCS)'],
     [OPAL_CHECK_PMI(pmi_type)])
AM_CONDITIONAL([USE_SHMEM_PMI], [test "$enable_pmi_simple" = "yes"])
AM_CONDITIONAL([USE_PMI2], [test "$pmi_type" = "pmi2"])

dnl check for header files
AC_CHECK_HEADERS([fnmatch.h])

dnl check for types

SHMEM_FIND_INT_TYPE([char], [$transport])
SHMEM_FIND_INT_TYPE([short], [$transport])
SHMEM_FIND_INT_TYPE([int], [$transport])
SHMEM_FIND_INT_TYPE([long], [$transport])
SHMEM_FIND_INT_TYPE([long long], [$transport])

AC_CHECK_SIZEOF([float])
AC_CHECK_SIZEOF([double])
AC_CHECK_SIZEOF([long double])
AC_CHECK_SIZEOF([void*])

C_LOG_MAXPES=32
C_BCAST_SYNC_SIZE=1
C_REDUCE_SYNC_SIZE=`$PERL -e "print $C_BCAST_SYNC_SIZE + 2 + $C_LOG_MAXPES"`
C_BARRIER_SYNC_SIZE=`$PERL -e "use POSIX; print $ac_cv_sizeof_int * 8 * ($ac_cv_sizeof_int / $ac_cv_sizeof_long)"`
tree_sync_size=`$PERL -e "print $C_BCAST_SYNC_SIZE + 3"`
recdbl_sync_size=`$PERL -e "use POSIX; print $ac_cv_sizeof_int * 8 * ($ac_cv_sizeof_int / $ac_cv_sizeof_long)"`
AS_IF([test $tree_sync_size -lt $recdbl_sync_size],
      [C_COLLECT_SYNC_SIZE=$recdbl_sync_size], [C_COLLECT_SYNC_SIZE=$tree_sync_size])
C_ALLTOALL_SYNC_SIZE=1
C_ALLTOALLS_SYNC_SIZE=1
C_REDUCE_MIN_WRKDATA_SIZE=1
AC_SUBST(C_BCAST_SYNC_SIZE)
AC_SUBST(C_REDUCE_SYNC_SIZE)
AC_SUBST(C_BARRIER_SYNC_SIZE)
AC_SUBST(C_COLLECT_SYNC_SIZE)
AC_SUBST(C_ALLTOALL_SYNC_SIZE)
AC_SUBST(C_ALLTOALLS_SYNC_SIZE)
AC_SUBST(C_REDUCE_MIN_WRKDATA_SIZE)

AC_MSG_CHECKING([if long can hold lock_t])
AC_TRY_RUN([struct lock_s { int a; int b; }; main() { if (sizeof(long) == sizeof(struct lock_s)) return 0; else return 1; }],
               [AC_MSG_RESULT([yes])], [
                AC_MSG_RESULT([no])
                AC_MSG_ERROR([Lock API requires long can hold 'struct { int a; int b; }']) ])

if test "$FC" != "" ; then
  AC_DEFINE([ENABLE_FORTRAN],[1],[Fortran support enabled])
  # make sure Fortran supports ISO_C_BINDING (Fortran 2003)
  AC_CACHE_CHECK([if Fortran compiler supports ISO_C_BINDING],
    [shmem_cv_fortran_iso_c_binding],
    [AC_LANG_PUSH([Fortran])
     AC_COMPILE_IFELSE([AC_LANG_PROGRAM(, [[
        use, intrinsic :: ISO_C_BINDING
	integer(c_int) :: i
	i = 5
]])], [shmem_cv_fortran_iso_c_binding="yes"],
      [shmem_cv_fortran_iso_c_binding="no"])])
  AS_IF([test "$shmem_cv_fortran_iso_c_binding" = "no"],
    [AC_MSG_ERROR([Fortran compiler does not support ISO_C_BINDING, which is required for Sandia OpenSHMEM.])])

  # Get size of Fortran types
  OMPI_FORTRAN_GET_SIZEOF([], [LOGICAL])
  OMPI_FORTRAN_GET_SIZEOF([], [CHARACTER])
  OMPI_FORTRAN_GET_SIZEOF([], [INTEGER])
  OMPI_FORTRAN_GET_SIZEOF([], [REAL])
  OMPI_FORTRAN_GET_SIZEOF([], [DOUBLE PRECISION])
  OMPI_FORTRAN_GET_SIZEOF([], [COMPLEX])

  # find transport integer type corresponding to INTEGER
  SHMEM_FIND_FORTRAN_INT_TYPE([INTEGER], [$transport])

  # find C type corresponding to given Fortran types
  OMPI_FIND_TYPE([INTEGER], [char,     short, int, long, long long])
  OMPI_FIND_TYPE([DOUBLE PRECISION], [float, double, long double])

  # Find kind values for 4 & 8 byte integer types in Fortran (used in
  # shmem.fh)
  OMPI_FORTRAN_GET_KIND_VALUE([C_INT32_T], [FORTRAN_INT4_KIND])
  OMPI_FORTRAN_GET_KIND_VALUE([C_INT64_T], [FORTRAN_INT8_KIND])
  OMPI_FORTRAN_GET_KIND_VALUE([C_FLOAT], [FORTRAN_REAL4_KIND])
  OMPI_FORTRAN_GET_KIND_VALUE([C_DOUBLE], [FORTRAN_REAL8_KIND])

  # The collectives are all implemented in C and require the psync to
  # hold a variable number of C longs.  Find how many INTEGERs it takes to
  # create a memory region as big as the required C region.
  FORTRAN_BCAST_SYNC_SIZE=`$PERL -e "use POSIX; print ceil($ac_cv_sizeof_long / $ompi_cv_fortran_sizeof_INTEGER * $C_BCAST_SYNC_SIZE)"`
  FORTRAN_REDUCE_SYNC_SIZE=`$PERL -e "use POSIX; print ceil($ac_cv_sizeof_long / $ompi_cv_fortran_sizeof_INTEGER * $C_REDUCE_SYNC_SIZE)"`
  FORTRAN_BARRIER_SYNC_SIZE=`$PERL -e "use POSIX; print ceil($ac_cv_sizeof_long / $ompi_cv_fortran_sizeof_INTEGER * $C_BARRIER_SYNC_SIZE)"`
  FORTRAN_COLLECT_SYNC_SIZE=`$PERL -e "use POSIX; print ceil($ac_cv_sizeof_long / $ompi_cv_fortran_sizeof_INTEGER * $C_COLLECT_SYNC_SIZE)"`
  FORTRAN_ALLTOALL_SYNC_SIZE=`$PERL -e "use POSIX; print ceil($ac_cv_sizeof_long / $ompi_cv_fortran_sizeof_INTEGER * $C_ALLTOALL_SYNC_SIZE)"`
  FORTRAN_ALLTOALLS_SYNC_SIZE=`$PERL -e "use POSIX; print ceil($ac_cv_sizeof_long / $ompi_cv_fortran_sizeof_INTEGER * $C_ALLTOALLS_SYNC_SIZE)"`
  FORTRAN_REDUCE_MIN_WRKDATA_SIZE=`$PERL -e "use POSIX; print ceil($ac_cv_sizeof_long / $ompi_cv_fortran_sizeof_INTEGER * $C_REDUCE_MIN_WRKDATA_SIZE)"`
else
  FORTRAN_BCAST_SYNC_SIZE=0
  FORTRAN_REDUCE_SYNC_SIZE=0
  FORTRAN_BARRIER_SYNC_SIZE=0
  FORTRAN_COLLECT_SYNC_SIZE=0
  FORTRAN_ALLTOALL_SYNC_SIZE=0
  FORTRAN_ALLTOALLS_SYNC_SIZE=0
  FORTRAN_REDUCE_MIN_WRKDATA_SIZE=0
  FORTRAN_INT4_KIND=0
  FORTRAN_INT8_KIND=0
  FORTRAN_REAL4_KIND=0
  FORTRAN_REAL8_KIND=0
  AC_DEFINE([DTYPE_FORTRAN_INTEGER],[-1],[Fortran integer C type (disabled)])
fi
if test "$ac_cv_sizeof_voidp" = "4" ; then
  FORTRAN_PTR_KIND=$FORTRAN_INT4_KIND
else
  FORTRAN_PTR_KIND=$FORTRAN_INT8_KIND
fi
AC_SUBST(FORTRAN_BCAST_SYNC_SIZE)
AC_SUBST(FORTRAN_REDUCE_SYNC_SIZE)
AC_SUBST(FORTRAN_BARRIER_SYNC_SIZE)
AC_SUBST(FORTRAN_COLLECT_SYNC_SIZE)
AC_SUBST(FORTRAN_ALLTOALL_SYNC_SIZE)
AC_SUBST(FORTRAN_ALLTOALLS_SYNC_SIZE)
AC_SUBST(FORTRAN_REDUCE_MIN_WRKDATA_SIZE)
AC_SUBST(FORTRAN_INT4_KIND)
AC_SUBST(FORTRAN_INT8_KIND)
AC_SUBST(FORTRAN_REAL4_KIND)
AC_SUBST(FORTRAN_REAL8_KIND)
AC_SUBST(FORTRAN_PTR_KIND)

AM_CONDITIONAL([HAVE_FORTRAN], [test "$FC" != ""])

AM_CONDITIONAL([HAVE_CXX], [test "$enable_cxx" != "no" ])

AM_CONDITIONAL([ENABLE_PROFILING], [test "$enable_profiling" = "yes" ])

dnl check for structures

dnl check for compiler characteristics

# disable aslr, if possible at link time
aslr_LDFLAGS=
case $host_os in
darwin*)
    aslr_LDFLAGS="-Wl,-no_pie"
    ;;
esac
AC_SUBST(aslr_LDFLAGS)

dnl check for library functions

dnl final output

LT_INIT

OPAL_CASE_SENSITIVE_FS_SETUP

DISTCHECK_CONFIGURE_FLAGS=
if test -n "$with_portals4" ; then
  DISTCHECK_CONFIGURE_FLAGS="$DISTCHECK_CONFIGURE_FLAGS --with-portals4=${with_portals4}"
fi
if test -n "$with_ofi" ; then
  DISTCHECK_CONFIGURE_FLAGS="$DISTCHECK_CONFIGURE_FLAGS --with-ofi=${with_ofi}"
fi
if test -n "$with_xpmem" ; then
  DISTCHECK_CONFIGURE_FLAGS="$DISTCHECK_CONFIGURE_FLAGS --with-xpmem=${with_xpmem}"
fi
if test -n "$with_pmi" ; then
  DISTCHECK_CONFIGURE_FLAGS="$DISTCHECK_CONFIGURE_FLAGS --with-pmi=${with_pmi}"
fi
if test -n "$FCFLAGS" ; then
  DISTCHECK_CONFIGURE_FLAGS="$DISTCHECK_CONFIGURE_FLAGS FCFLAGS=\"$FCFLAGS\""
fi
AC_SUBST(DISTCHECK_CONFIGURE_FLAGS)

dnl Cause Automake to delete emacs turds
CLEANFILES="*~"
AC_SUBST(CLEANFILES)

# last minute updates of the flags so that tests don't fail oddly if
# portals libdir isn't in LD_LIBRARY_PATH...
CPPFLAGS="$CPPFLAGS $portals4_CPPFLAGS $ofi_CPPFLAGS $XPMEM_CPPFLAGS $pmi_CPPFLAGS"
LDFLAGS="$LDFLAGS $portals4_LDFLAGS $ofi_LDFLAGS $XPMEM_LDFLAGS $pmi_LDFLAGS $aslr_LDFLAGS $RPATH_LDFLAGS"
LIBS="$LIBS $portals4_LIBS $ofi_LIBS $XPMEM_LIBS $pmi_LIBS"

# wrapper compiler gorp
WRAPPER_COMPILER_EXTRA_LDFLAGS="$portals4_LDFLAGS $ofi_LDFLAGS $XPMEM_LDFLAGS $pmi_LDFLAGS $aslr_LDFLAGS $RPATH_LDFLAGS"
WRAPPER_COMPILER_EXTRA_LIBS="$portals4_LIBS $ofi_LIBS $XPMEM_LIBS $pmi_LIBS"
AC_SUBST(WRAPPER_COMPILER_EXTRA_LDFLAGS)
AC_SUBST(WRAPPER_COMPILER_EXTRA_LIBS)

AC_CONFIG_FILES([Makefile
  sandia-openshmem.spec
  mpp/Makefile
  mpp/shmem.fh
  mpp/shmemx.fh
  mpp/shmem.h4
  mpp/shmemx.h4
  src/Makefile
  test/Makefile
  test/unit/Makefile
  test/performance/Makefile
  test/performance/shmem_perf_suite/Makefile
  test/performance/tests/Makefile
  test/apps/Makefile])

if test "$enable_profiling" = "yes"; then
    AC_CONFIG_FILES([mpp/pshmem.h4])
fi

AS_IF([test "$enable_pmi_simple" = "yes"],
	[AC_CONFIG_SUBDIRS([shmem_pmi])])

AC_OUTPUT

AS_IF([test "$enable_pmi_simple" != "yes" -a "$ompi_check_pmi_happy" != "yes" -a -z "$pmi_type"],
         [AC_MSG_WARN([No PMI found, if build fails consider --enable-pmi-simple or --with-pmi])])

AS_IF([test "$transport_portals4" != "yes" -a "$transport_xpmem" != "yes" -a "$transport_cma" != "yes" -a "$transport_ofi" != "yes"],
      [AC_MSG_WARN([No transport found])])


FORT="$FC"
if test "$FORT" = "" ; then
  FORT="none"
fi

if test "$enable_cxx" == "no"; then
  CXX="none"
fi

echo ""
echo "Compilers:"
echo "  C:              $CC"
echo "  C++:            $CXX"
echo "  Fortran:        $FORT"
echo ""
echo "Transports:"
echo "  Portals 4:      $transport_portals4"
echo "  OFI:            $transport_ofi"
echo "  XPMEM:          $transport_xpmem"
echo "  CMA:            $transport_cma"
echo ""
