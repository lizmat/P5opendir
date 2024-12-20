[![Actions Status](https://github.com/lizmat/P5opendir/actions/workflows/linux.yml/badge.svg)](https://github.com/lizmat/P5opendir/actions) [![Actions Status](https://github.com/lizmat/P5opendir/actions/workflows/macos.yml/badge.svg)](https://github.com/lizmat/P5opendir/actions) [![Actions Status](https://github.com/lizmat/P5opendir/actions/workflows/windows.yml/badge.svg)](https://github.com/lizmat/P5opendir/actions)

NAME
====

Raku port of Perl's opendir() and related built-ins

SYNOPSIS
========

    # exports opendir, readdir, telldir, seekdir, rewinddir, closedir
    use P5opendir;

    opendir(my $dh, $some_dir) || die "can't opendir $some_dir: $!";
    my @dots = grep { .starts-with('.') && "$some_dir/$_".IO.f }, readdir($dh);
    closedir $dh;

DESCRIPTION
===========

This module tries to mimic the behaviour of Perl's `opendir`, `readdir`, `telldir`, `seekdir`, `rewinddir` and `closedir` built-ins as closely as possible in the Raku Programming Language.

ORIGINAL PERL 5 DOCUMENTATION
=============================

    opendir DIRHANDLE,EXPR
            Opens a directory named EXPR for processing by "readdir",
            "telldir", "seekdir", "rewinddir", and "closedir". Returns true if
            successful. DIRHANDLE may be an expression whose value can be used
            as an indirect dirhandle, usually the real dirhandle name. If
            DIRHANDLE is an undefined scalar variable (or array or hash
            element), the variable is assigned a reference to a new anonymous
            dirhandle; that is, it's autovivified. DIRHANDLEs have their own
            namespace separate from FILEHANDLEs.

    readdir DIRHANDLE
            Returns the next directory entry for a directory opened by
            "opendir". If used in list context, returns all the rest of the
            entries in the directory. If there are no more entries, returns
            the undefined value in scalar context and the empty list in list
            context.

            If you're planning to filetest the return values out of a
            "readdir", you'd better prepend the directory in question.
            Otherwise, because we didn't "chdir" there, it would have been
            testing the wrong file.

                opendir(my $dh, $some_dir) || die "can't opendir $some_dir: $!";
                @dots = grep { /^\./ && -f "$some_dir/$_" } readdir($dh);
                closedir $dh;

            As of Perl 5.12 you can use a bare "readdir" in a "while" loop,
            which will set $_ on every iteration.

                opendir(my $dh, $some_dir) || die;
                while(readdir $dh) {
                    print "$some_dir/$_\n";
                }
                closedir $dh;

            To avoid confusing would-be users of your code who are running
            earlier versions of Perl with mysterious failures, put this sort
            of thing at the top of your file to signal that your code will
            work monly on Perls of a recent vintage:

                use 5.012; # so readdir assigns to $_ in a lone while test

    telldir DIRHANDLE
            Returns the current position of the "readdir" routines on
            DIRHANDLE. Value may be given to "seekdir" to access a particular
            location in a directory. "telldir" has the same caveats about
            possible directory compaction as the corresponding system library
            routine.

    seekdir DIRHANDLE,POS
            Sets the current position for the "readdir" routine on DIRHANDLE.
            POS must be a value returned by "telldir". "seekdir" also has the
            same caveats about possible directory compaction as the
            corresponding system library routine.

    closedir DIRHANDLE
            Closes a directory opened by "opendir" and returns the success of
            that system call.

PORTING CAVEATS
===============

The `readdir` function has three modes:

list mode
---------

By default, `readdir` returns a list with all directory entries found.

    my @entries = readdir($dh);

scalar context
--------------

In scalar context, `readdir` returns one directory entry at a time. Add `Scalar` as the first positional variable to mimic this behaviour:

    while readdir(Scalar, $dh, :scalar) -> $entry {
        say "found $entry";
    }

void context
------------

In void context, `readdir` stores one directory entry at a time in `$_`. Add `Mu` as the first positional variable to mimic this behaviour:

    .say while readdir(Mu, $dh, :void);

$_ no longer accessible from caller's scope
-------------------------------------------

In future language versions of Raku, it will become impossible to access the `$_` variable of the caller's scope, because it will not have been marked as a dynamic variable. So please consider changing:

    readdir;

to either:

    readdir($_);

or, using the subroutine as a method syntax, with the prefix `.` shortcut to use that scope's `$_` as the invocant:

    .&readdir;

AUTHOR
======

Elizabeth Mattijsen <liz@raku.rocks>

If you like this module, or what I’m doing more generally, committing to a [small sponsorship](https://github.com/sponsors/lizmat/) would mean a great deal to me!

Source can be located at: https://github.com/lizmat/P5opendir . Comments and Pull Requests are welcome.

COPYRIGHT AND LICENSE
=====================

Copyright 2018, 2019, 2020, 2021, 2023, 2024 Elizabeth Mattijsen

Re-imagined from Perl as part of the CPAN Butterfly Plan.

This library is free software; you can redistribute it and/or modify it under the Artistic License 2.0.

