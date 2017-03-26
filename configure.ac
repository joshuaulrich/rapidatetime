### configure.ac					-*- Autoconf -*-
###
### Process this file with autoconf to produce a configure script.
###
### Copyright (C) 1998-2017 R Core Team
###
### This file is part of R.
###
### R is free software; you can redistribute it and/or modify it under
### the terms of the GNU General Public License as published by the Free
### Software Foundation; either version 2 of the License, or (at your
### option) any later version.
###
### R is distributed in the hope that it will be useful, but WITHOUT ANY
### WARRANTY; without even the implied warranty of MERCHANTABILITY or
### FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public
### License for more details.
###
### You should have received a copy of the GNU General Public License
### along with R; if not, a copy is available at
### https://www.r-project.org/Licenses/

AC_PREREQ(2.62)

AC_INIT([RApiDatetime], [0.0.1])

### ** Optional features.

AC_ARG_WITH([internal-tzcode],
[AS_HELP_STRING([--with-internal-tzcode],[use internal time-zone code @<:@yes@:>@])],
[use_internal_tzcode=${withval}],
[use_internal_tzcode=default])

## R_MKTIME_ERRNO
## --------------
## Check whether mktime sets errno
AC_DEFUN([R_MKTIME_ERRNO],
[AC_CACHE_CHECK([whether mktime sets errno], [r_cv_mktime_errno],
[AC_RUN_IFELSE([AC_LANG_SOURCE([[
#include <stdlib.h>
#include <time.h>
#include <errno.h>

int main()
{
    struct tm tm;
    // Its hard to know what is an error, since mktime is allowed to
    // fix up times and there are 64-bit time_t about.
    // But this works for now (yes on Solaris, no on glibc).
    tm.tm_year = 3000; tm.tm_mon = 0; tm.tm_mday = 0;
    tm.tm_hour = 0; tm.tm_min = 0; tm.tm_sec = 0; tm.tm_isdst = -1;
    errno = 0;
    mktime(&tm);
    exit(errno == 0);
}
]])],
              [r_cv_mktime_errno=yes],
              [r_cv_mktime_errno=no],
              [r_cv_mktime_errno=no])])
if test "${r_cv_mktime_errno}" = yes; then
  AC_DEFINE(MKTIME_SETS_ERRNO,, [Define if mktime sets errno.])
fi
])# R_MKTIME_ERRNO

## R_FUNC_MKTIME
## ------------
AC_DEFUN([R_FUNC_MKTIME],
[AC_CACHE_CHECK([whether mktime works correctly outside 1902-2037],
                [r_cv_working_mktime],
[AC_RUN_IFELSE([AC_LANG_SOURCE([[
#include <stdlib.h>
#include <time.h>

int main() {
    if(sizeof(time_t) < 8) exit(1);

    struct tm tm;
    time_t res;
    putenv("TZ=Europe/London");
    tm.tm_sec = tm.tm_min = 0; tm.tm_hour = 12;
    tm.tm_mday = 1; tm.tm_mon = 0; tm.tm_year = 80; tm.tm_isdst = 0;
    res = mktime(&tm);
    if(res == (time_t)-1) exit(2);
    tm.tm_mday = 1; tm.tm_year = 01; tm.tm_isdst = 0;
    res = mktime(&tm);
    if(res == (time_t)-1) exit(3);
    tm.tm_year = 140;
    res = mktime(&tm);
    if(res != 2209032000L) exit(4);
    tm.tm_mon = 6; tm.tm_isdst = 1;
    res = mktime(&tm);
    if(res != 2224753200L) exit(5);

    exit(0);
}
]])],
              [r_cv_working_mktime=yes],
              [r_cv_working_mktime=no],
              [r_cv_working_mktime=no])])
if test "x${r_cv_working_mktime}" = xyes; then
  AC_DEFINE(HAVE_WORKING_64BIT_MKTIME, 1,
            [Define if your mktime works correctly outside 1902-2037.])
fi
])# R_FUNC_MKTIME

if test x${use_internal_tzcode} = xdefault; then
case "${host_os}" in
   darwin*)
     use_internal_tzcode=yes;
     ;; 
esac
fi

if test "${use_internal_tzcode}" != yes; then
  R_MKTIME_ERRNO
  R_FUNC_MKTIME
fi

if test "x${use_internal_tzcode}" = xyes; then
  AC_DEFINE(USE_INTERNAL_MKTIME, 1, [Define to use internal time-zone code])
fi

#AC_CONFIG_HEADERS([src/config.h])
AC_OUTPUT(src/Makevars)

### Local variables: ***
### mode: autoconf ***
### outline-regexp: "### [*]+" ***
### End: ***
