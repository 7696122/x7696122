+++
title = "Dot/Emacs org"
author = [7696122]
lastmod = 2022-01-19T11:16:58+09:00
tags = ["doom", "emacs", "org-mode"]
draft = false
+++

## ~/bin {#bin}


### /build-doomdir {#build-doomdir}

```sh
# !env bash
# https://code-maven.com/bash-absolute-path
# BASE=$(dirname $(realpath $0))
BASE=$(dirname $(readlink -f $0))
BASE=~/org
echo $BASE
# (defun where () (expand-file-name (mapconcat 'identity (org-get-outline-path t) "") (org-entry-get nil "dir" t)))
# Doom
emacs -q $BASE/roam/emacs.org --batch \
    --eval="(defun where () (expand-file-name (mapconcat 'identity (org-get-outline-path t) \"\") (org-entry-get nil \"dir\" t)))" \
    -f org-org-export-to-org
emacs -q $BASE/roam/emacs.org.org --batch \
    --eval="(defun where () (expand-file-name (mapconcat 'identity (org-get-outline-path t) \"\") (org-entry-get nil \"dir\" t)))" \
    -f org-babel-tangle
```


## ~/.emacs.el {#dot-emacs-dot-el}

```elisp
;; (featurep! :lang org)
(add-hook 'org-load-hook #'(lambda () (add-to-list 'org-export-backends 'org)))
(setq org-id-locations-file
      (convert-standard-filename
       (concat user-emacs-directory
               ".org-id-locations")))
(load "~/.emacs.d/init.el")
```


### org로 export 가능하게 하기 {#org로-export-가능하게-하기}

```elisp
;; (featurep! :lang org)
(add-hook 'org-load-hook #'(lambda () (add-to-list 'org-export-backends 'org)))
```


## ~/.doom.d/ {#dot-doom-dot-d}


### /init.el {#init-dot-el}

```elisp
;;; init.el -*- lexical-binding: t; -*-
<<doomdir_init_el>>
```


### /packages.el {#packages-dot-el}

```elisp
;; $DOOMDIR/packages.el -*- no-byte-compile: t; -*-
<<doomdir_packages_el>>
```


### /config.el {#config-dot-el}

```elisp
;;; $DOOMDIR/config.el -*- lexical-binding: t; -*-
(defun doom/tangle ()
  (interactive)
  (let* ((bin-dir (expand-file-name "bin" (getenv "HOME")))
         (bin     (expand-file-name "build-doomdir" bin-dir)))
    (async-shell-command (format "%S" bin) nil t)))
(define-key! help-map "rb" #'doom/tangle)

<<doomdir_config_el>>
```


## doom modules {#doom-modules}

```elisp
(doom!
 :input
 <<doom!_modules:input>>
 :completion
 <<doom!_modules:completion>>
 :ui
 <<doom!_modules:ui>>
 :editor
 <<doom!_modules:editor>>
 :emacs
 <<doom!_modules:emacs>>
 :term
 <<doom!_modules:term>>
 :checkers
 <<doom!_modules:checkers>>
 :tools
 <<doom!_modules:tools>>
 :os
 <<doom!_modules:os>>
 :lang
 <<doom!_modules:lang>>
 :email
 <<doom!_modules:email>>
 :app
 <<doom!_modules:app>>
 :config
 <<doom!_modules:config>>
 )
```


### :input {#input}

```elisp
:input
<<doom!_modules:input>>
```


### :completion {#completion}

```elisp
:completion
<<doom!_modules:completion>>
```


#### company ::the ultimate code completion backend {#company-the-ultimate-code-completion-backend}

```elisp
(company
 ;; +childframe
 ;; +tng
 )
```

```elisp
<<use-package::company>>
```

<!--list-separator-->

-  use-package

    <a id="code-snippet--use-package::company"></a>
    ```elisp
    (use-package! company
      :custom-face
      (company-tooltip ((t (:inherit (company-box-candidate mode-line)))))
      (company-tooltip-selection ((t (:inherit (company-tooltip-selection highlight) :extend t))))
      :config
      (setq company-minimun-prefix-length 2)
      (setq company-idle-delay .5)
      ;; (setq company-auto-complete t)
      ;; (setq company-auto-commit-chars '(?\  ?\) ?.))
      ;; (push '(company-rtags company-keywords) company-backends)
    
      <<C-s 저장으로>>
      <<pcomplete 설정>>
    
      <<company-dabbrev>>
    
      (add-hook 'after-init-hook 'global-company-mode))
    
    ;; (after! js2-mode
    ;;   (set-company-backend! 'js2-mode 'company-tide 'company-yasnippet))
    
    ;; (after! sh-script
    ;;   (set-company-backend! 'sh-mode
    ;;     '(company-shell :with company-yasnippet)))
    
    ;; (after! cc-mode
    ;;   (set-company-backend! 'c-mode
    ;;     '(:separate company-irony-c-headers company-irony)))
    ```

<!--list-separator-->

-  doom evil key 수정

    <a id="code-snippet--C-s 저장으로"></a>
    ```elisp
    (map!
     (:when (featurep! :completion company)
      (:prefix "C-x"
       :i "C-s"    nil ;; #'company-yasnippet
       )))
    ```

<!--list-separator-->

-  pcomplete 설정

    [Company-mode completion for Org keywords - Emacs Stack Exchange](https://emacs.stackexchange.com/questions/21171/company-mode-completion-for-org-keywords)  
    
    <a id="code-snippet--pcomplete 설정"></a>
    ```elisp
    (defun add-pcomplete-to-capf ()
      (add-hook 'completion-at-point-functions
                'pcomplete-completions-at-point
                nil
                t))
    (add-hook 'org-mode-hook #'add-pcomplete-to-capf)
    
    (progn ;; pcomplete-completions-at-point with company
      ;; https://github.com/company-mode/company-mode/issues/804
      (advice-add
       'company-capf
       :around
       (lambda (fun &rest args)
         (let ((completion-at-point-functions
                (pcase major-mode
                  ('eshell-mode '(pcomplete-completions-at-point
                                  elisp-completion-at-point))
                  ('org-mode '(pcomplete-completions-at-point))
                  (_ completion-at-point-functions))))
           (apply fun args)))))
    ```

<!--list-separator-->

-  company-keywords

    ```elisp
    (setq company-keywords-alist
          ;; Please contribute corrections or additions.
          `((c++-mode
             ;; from https://en.cppreference.com/w/cpp/keyword
             "alignas" "alignof" "and" "and_eq" "asm" "atomic_cancel" "atomic_commit"
             "atomic_noexcept" "auto" "bitand" "bitor" "bool" "break" "case" "catch"
             "char" "char16_t" "char32_t" "char8_t" "class" "co_await" "co_return"
             "co_yield" "compl" "concept" "const" "const_cast" "consteval" "constexpr"
             "constinit" "continue" "decltype" "default" "delete" "do" "double"
             "dynamic_cast" "else" "enum" "explicit" "export" "extern" "false" "final"
             "float" "for" "friend" "goto" "if" "import" "inline" "int" "long" "module"
             "mutable" "namespace" "new" "noexcept" "not" "not_eq" "nullptr" "operator"
             "or" "or_eq" "override" "private" "protected" "public" "reflexpr" "register"
             "reinterpret_cast" "requires" "return" "short" "signed" "sizeof" "static"
             "static_assert" "static_cast" "struct" "switch" "synchronized" "template"
             "this" "thread_local" "throw" "true" "try" "typedef" "typeid" "typename"
             "union" "unsigned" "using" "virtual" "void" "volatile" "wchar_t" "while"
             "xor" "xor_eq")
            (c-mode
             ;; from https://en.cppreference.com/w/c/keyword
             "_Alignas" "_Alignof" "_Atomic" "_Bool" "_Complex" "_Generic" "_Imaginary"
             "_Noreturn" "_Static_assert" "_Thread_local"
             "auto" "break" "case" "char" "const" "continue" "default" "do"
             "double" "else" "enum" "extern" "float" "for" "goto" "if" "inline"
             "int" "long" "register" "restrict" "return" "short" "signed" "sizeof"
             "static" "struct" "switch" "typedef" "union" "unsigned" "void" "volatile"
             "while")
            (csharp-mode
             "abstract" "add" "alias" "as" "base" "bool" "break" "byte" "case"
             "catch" "char" "checked" "class" "const" "continue" "decimal" "default"
             "delegate" "do" "double" "else" "enum" "event" "explicit" "extern"
             "false" "finally" "fixed" "float" "for" "foreach" "get" "global" "goto"
             "if" "implicit" "in" "int" "interface" "internal" "is" "lock" "long"
             "namespace" "new" "null" "object" "operator" "out" "override" "params"
             "partial" "private" "protected" "public" "readonly" "ref" "remove"
             "return" "sbyte" "sealed" "set" "short" "sizeof" "stackalloc" "static"
             "string" "struct" "switch" "this" "throw" "true" "try" "typeof" "uint"
             "ulong" "unchecked" "unsafe" "ushort" "using" "value" "var" "virtual"
             "void" "volatile" "where" "while" "yield")
            (d-mode
             ;; from http://www.digitalmars.com/d/2.0/lex.html
             "abstract" "alias" "align" "asm"
             "assert" "auto" "body" "bool" "break" "byte" "case" "cast" "catch"
             "cdouble" "cent" "cfloat" "char" "class" "const" "continue" "creal"
             "dchar" "debug" "default" "delegate" "delete" "deprecated" "do"
             "double" "else" "enum" "export" "extern" "false" "final" "finally"
             "float" "for" "foreach" "foreach_reverse" "function" "goto" "idouble"
             "if" "ifloat" "import" "in" "inout" "int" "interface" "invariant"
             "ireal" "is" "lazy" "long" "macro" "mixin" "module" "new" "nothrow"
             "null" "out" "override" "package" "pragma" "private" "protected"
             "public" "pure" "real" "ref" "return" "scope" "short" "static" "struct"
             "super" "switch" "synchronized" "template" "this" "throw" "true" "try"
             "typedef" "typeid" "typeof" "ubyte" "ucent" "uint" "ulong" "union"
             "unittest" "ushort" "version" "void" "volatile" "wchar" "while" "with")
            (f90-mode .
                      ;; from f90.el
                      ;; ".AND." ".GE." ".GT." ".LT." ".LE." ".NE." ".OR." ".TRUE." ".FALSE."
                      ,(company-keywords-upper-lower
                        "abs" "abstract" "achar" "acos" "adjustl" "adjustr" "aimag" "aint"
                        "align" "all" "all_prefix" "all_scatter" "all_suffix" "allocatable"
                        "allocate" "allocated" "and" "anint" "any" "any_prefix" "any_scatter"
                        "any_suffix" "asin" "assign" "assignment" "associate" "associated"
                        "asynchronous" "atan" "atan2" "backspace" "bind" "bit_size" "block"
                        "btest" "c_alert" "c_associated" "c_backspace" "c_bool"
                        "c_carriage_return" "c_char" "c_double" "c_double_complex" "c_f_pointer"
                        "c_f_procpointer" "c_float" "c_float_complex" "c_form_feed" "c_funloc"
                        "c_funptr" "c_horizontal_tab" "c_int" "c_int16_t" "c_int32_t" "c_int64_t"
                        "c_int8_t" "c_int_fast16_t" "c_int_fast32_t" "c_int_fast64_t"
                        "c_int_fast8_t" "c_int_least16_t" "c_int_least32_t" "c_int_least64_t"
                        "c_int_least8_t" "c_intmax_t" "c_intptr_t" "c_loc" "c_long"
                        "c_long_double" "c_long_double_complex" "c_long_long" "c_new_line"
                        "c_null_char" "c_null_funptr" "c_null_ptr" "c_ptr" "c_short"
                        "c_signed_char" "c_size_t" "c_vertical_tab" "call" "case" "ceiling"
                        "char" "character" "character_storage_size" "class" "close" "cmplx"
                        "command_argument_count" "common" "complex" "conjg" "contains" "continue"
                        "copy_prefix" "copy_scatter" "copy_suffix" "cos" "cosh" "count"
                        "count_prefix" "count_scatter" "count_suffix" "cpu_time" "cshift"
                        "cycle" "cyclic" "data" "date_and_time" "dble" "deallocate" "deferred"
                        "digits" "dim" "dimension" "distribute" "do" "dot_product" "double"
                        "dprod" "dynamic" "elemental" "else" "elseif" "elsewhere" "end" "enddo"
                        "endfile" "endif" "entry" "enum" "enumerator" "eoshift" "epsilon" "eq"
                        "equivalence" "eqv" "error_unit" "exit" "exp" "exponent" "extends"
                        "extends_type_of" "external" "extrinsic" "false" "file_storage_size"
                        "final" "floor" "flush" "forall" "format" "fraction" "function" "ge"
                        "generic" "get_command" "get_command_argument" "get_environment_variable"
                        "goto" "grade_down" "grade_up" "gt" "hpf_alignment" "hpf_distribution"
                        "hpf_template" "huge" "iachar" "iall" "iall_prefix" "iall_scatter"
                        "iall_suffix" "iand" "iany" "iany_prefix" "iany_scatter" "iany_suffix"
                        "ibclr" "ibits" "ibset" "ichar" "ieee_arithmetic" "ieee_exceptions"
                        "ieee_features" "ieee_get_underflow_mode" "ieee_set_underflow_mode"
                        "ieee_support_underflow_control" "ieor" "if" "ilen" "implicit"
                        "import" "include" "independent" "index" "inherit" "input_unit"
                        "inquire" "int" "integer" "intent" "interface" "intrinsic" "ior"
                        "iostat_end" "iostat_eor" "iparity" "iparity_prefix" "iparity_scatter"
                        "iparity_suffix" "ishft" "ishftc" "iso_c_binding" "iso_fortran_env"
                        "kind" "lbound" "le" "leadz" "len" "len_trim" "lge" "lgt" "lle" "llt"
                        "log" "log10" "logical" "lt" "matmul" "max" "maxexponent" "maxloc"
                        "maxval" "maxval_prefix" "maxval_scatter" "maxval_suffix" "merge"
                        "min" "minexponent" "minloc" "minval" "minval_prefix" "minval_scatter"
                        "minval_suffix" "mod" "module" "modulo" "move_alloc" "mvbits" "namelist"
                        "ne" "nearest" "neqv" "new" "new_line" "nint" "non_intrinsic"
                        "non_overridable" "none" "nopass" "not" "null" "nullify"
                        "number_of_processors" "numeric_storage_size" "only" "onto" "open"
                        "operator" "optional" "or" "output_unit" "pack" "parameter" "parity"
                        "parity_prefix" "parity_scatter" "parity_suffix" "pass" "pause"
                        "pointer" "popcnt" "poppar" "precision" "present" "print" "private"
                        "procedure" "processors" "processors_shape" "product" "product_prefix"
                        "product_scatter" "product_suffix" "program" "protected" "public"
                        "pure" "radix" "random_number" "random_seed" "range" "read" "real"
                        "realign" "recursive" "redistribute" "repeat" "reshape" "result"
                        "return" "rewind" "rrspacing" "same_type_as" "save" "scale" "scan"
                        "select" "selected_char_kind" "selected_int_kind" "selected_real_kind"
                        "sequence" "set_exponent" "shape" "sign" "sin" "sinh" "size" "spacing"
                        "spread" "sqrt" "stop" "subroutine" "sum" "sum_prefix" "sum_scatter"
                        "sum_suffix" "system_clock" "tan" "tanh" "target" "template" "then"
                        "tiny" "transfer" "transpose" "trim" "true" "type" "ubound" "unpack"
                        "use" "value" "verify" "volatile" "wait" "where" "while" "with" "write"))
            (go-mode
             ;; 1. Keywords ref: https://golang.org/ref/spec#Keywords
             ;; 2. Builtin functions and types ref: https://golang.org/pkg/builtin/
             "append" "bool" "break" "byte" "cap" "case" "chan" "close" "complex" "complex128"
             "complex64" "const" "continue" "copy" "default" "defer" "delete" "else" "error"
             "fallthrough" "false" "float32" "float64" "for" "func" "go" "goto" "if" "imag"
             "import" "int" "int16" "int32" "int64" "int8" "interface" "len" "make"
             "map" "new" "nil" "package" "panic" "print" "println" "range" "real" "recover"
             "return" "rune" "select" "string" "struct" "switch" "true" "type" "uint" "uint16"
             "uint32" "uint64" "uint8" "uintptr" "var")
            (java-mode
             "abstract" "assert" "boolean" "break" "byte" "case" "catch" "char" "class"
             "continue" "default" "do" "double" "else" "enum" "extends" "final"
             "finally" "float" "for" "if" "implements" "import" "instanceof" "int"
             "interface" "long" "native" "new" "package" "private" "protected" "public"
             "return" "short" "static" "strictfp" "super" "switch" "synchronized"
             "this" "throw" "throws" "transient" "try" "void" "volatile" "while")
            (javascript-mode
             ;; https://tc39.github.io/ecma262/ + async, static and undefined
             "async" "await" "break" "case" "catch" "class" "const" "continue"
             "debugger" "default" "delete" "do" "else" "enum" "export" "extends" "false"
             "finally" "for" "function" "if" "import" "in" "instanceof" "let" "new"
             "null" "return" "static" "super" "switch" "this" "throw" "true" "try"
             "typeof" "undefined" "var" "void" "while" "with" "yield"
             "spyOn")
            (kotlin-mode
             "abstract" "annotation" "as" "break" "by" "catch" "class" "companion"
             "const" "constructor" "continue" "data" "do" "else" "enum" "false" "final"
             "finally" "for" "fun" "if" "import" "in" "init" "inner" "interface"
             "internal" "is" "lateinit" "nested" "null" "object" "open" "out" "override"
             "package" "private" "protected" "public" "return" "super" "this" "throw"
             "trait" "true" "try" "typealias" "val" "var" "when" "while")
            (lua-mode
             ;; https://www.lua.org/manual/5.3/manual.html
             "and" "break" "do" "else" "elseif" "end" "false" "for" "function" "goto" "if"
             "in" "local" "nil" "not" "or" "repeat" "return" "then" "true" "until" "while")
            (objc-mode
             "@catch" "@class" "@encode" "@end" "@finally" "@implementation"
             "@interface" "@private" "@protected" "@protocol" "@public"
             "@selector" "@synchronized" "@throw" "@try" "alloc" "autorelease"
             "bycopy" "byref" "in" "inout" "oneway" "out" "release" "retain")
            (perl-mode
             ;; from cperl.el
             "AUTOLOAD" "BEGIN" "CHECK" "CORE" "DESTROY" "END" "INIT" "__END__"
             "__FILE__" "__LINE__" "abs" "accept" "alarm" "and" "atan2" "bind"
             "binmode" "bless" "caller" "chdir" "chmod" "chomp" "chop" "chown" "chr"
             "chroot" "close" "closedir" "cmp" "connect" "continue" "cos"
             "crypt" "dbmclose" "dbmopen" "defined" "delete" "die" "do" "dump" "each"
             "else" "elsif" "endgrent" "endhostent" "endnetent" "endprotoent"
             "endpwent" "endservent" "eof" "eq" "eval" "exec" "exists" "exit" "exp"
             "fcntl" "fileno" "flock" "for" "foreach" "fork" "format" "formline"
             "ge" "getc" "getgrent" "getgrgid" "getgrnam" "gethostbyaddr"
             "gethostbyname" "gethostent" "getlogin" "getnetbyaddr" "getnetbyname"
             "getnetent" "getpeername" "getpgrp" "getppid" "getpriority"
             "getprotobyname" "getprotobynumber" "getprotoent" "getpwent" "getpwnam"
             "getpwuid" "getservbyname" "getservbyport" "getservent" "getsockname"
             "getsockopt" "glob" "gmtime" "goto" "grep" "gt" "hex" "if" "index" "int"
             "ioctl" "join" "keys" "kill" "last" "lc" "lcfirst" "le" "length"
             "link" "listen" "local" "localtime" "lock" "log" "lstat" "lt" "map"
             "mkdir" "msgctl" "msgget" "msgrcv" "msgsnd" "my" "ne" "next" "no"
             "not" "oct" "open" "opendir" "or" "ord" "our" "pack" "package" "pipe"
             "pop" "pos" "print" "printf" "push" "q" "qq" "quotemeta" "qw" "qx"
             "rand" "read" "readdir" "readline" "readlink" "readpipe" "recv" "redo"
             "ref" "rename" "require" "reset" "return" "reverse" "rewinddir" "rindex"
             "rmdir" "scalar" "seek" "seekdir" "select" "semctl" "semget" "semop"
             "send" "setgrent" "sethostent" "setnetent" "setpgrp" "setpriority"
             "setprotoent" "setpwent" "setservent" "setsockopt" "shift" "shmctl"
             "shmget" "shmread" "shmwrite" "shutdown" "sin" "sleep" "socket"
             "socketpair" "sort" "splice" "split" "sprintf" "sqrt" "srand" "stat"
             "study" "sub" "substr" "symlink" "syscall" "sysopen" "sysread" "system"
             "syswrite" "tell" "telldir" "tie" "time" "times" "tr" "truncate" "uc"
             "ucfirst" "umask" "undef" "unless" "unlink" "unpack" "unshift" "untie"
             "until" "use" "utime" "values" "vec" "wait" "waitpid"
             "wantarray" "warn" "while" "write" "x" "xor" "y")
            (php-mode
             "__CLASS__" "__DIR__" "__FILE__" "__FUNCTION__" "__LINE__" "__METHOD__"
             "__NAMESPACE__" "_once" "abstract" "and" "array" "as" "break" "case"
             "catch" "cfunction" "class" "clone" "const" "continue" "declare"
             "default" "die" "do" "echo" "else" "elseif" "empty" "enddeclare"
             "endfor" "endforeach" "endif" "endswitch" "endwhile" "eval" "exception"
             "exit" "extends" "final" "for" "foreach" "function" "global"
             "goto" "if" "implements" "include" "instanceof" "interface"
             "isset" "list" "namespace" "new" "old_function" "or" "php_user_filter"
             "print" "private" "protected" "public" "require" "require_once" "return"
             "static" "switch" "this" "throw" "try" "unset" "use" "var" "while" "xor")
            (python-mode
             ;; https://docs.python.org/3/reference/lexical_analysis.html#keywords
             "False" "None" "True" "and" "as" "assert" "break" "class" "continue" "def"
             "del" "elif" "else" "except" "exec" "finally" "for" "from" "global" "if"
             "import" "in" "is" "lambda" "nonlocal" "not" "or" "pass" "print" "raise"
             "return" "try" "while" "with" "yield")
            (ruby-mode
             "BEGIN" "END" "alias" "and"  "begin" "break" "case" "class" "def" "defined?"
             "do" "else" "elsif"  "end" "ensure" "false" "for" "if" "in" "module"
             "next" "nil" "not" "or" "redo" "rescue" "retry" "return" "self" "super"
             "then" "true" "undef" "unless" "until" "when" "while" "yield")
            ;; From https://doc.rust-lang.org/grammar.html#keywords
            ;; but excluding unused reserved words: https://www.reddit.com/r/rust/comments/34fq0k/is_there_a_good_list_of_rusts_keywords/cqucvnj
            (rust-mode
             "Self"
             "as" "box" "break" "const" "continue" "crate" "else" "enum" "extern"
             "false" "fn" "for" "if" "impl" "in" "let" "loop" "macro" "match" "mod"
             "move" "mut" "pub" "ref" "return" "self" "static" "struct" "super"
             "trait" "true" "type" "unsafe" "use" "where" "while")
            (scala-mode
             "abstract" "case" "catch" "class" "def" "do" "else" "extends" "false"
             "final" "finally" "for" "forSome" "if" "implicit" "import" "lazy" "match"
             "new" "null" "object" "override" "package" "private" "protected"
             "return" "sealed" "super" "this" "throw" "trait" "true" "try" "type" "val"
             "var" "while" "with" "yield")
            (swift-mode
             "Protocol" "Self" "Type" "and" "as" "assignment" "associatedtype"
             "associativity" "available" "break" "case" "catch" "class" "column" "continue"
             "convenience" "default" "defer" "deinit" "didSet" "do" "dynamic" "dynamicType"
             "else" "elseif" "endif" "enum" "extension" "fallthrough" "false" "file"
             "fileprivate" "final" "for" "func" "function" "get" "guard" "higherThan" "if"
             "import" "in" "indirect" "infix" "init" "inout" "internal" "is" "lazy" "left"
             "let" "line" "lowerThan" "mutating" "nil" "none" "nonmutating" "open"
             "operator" "optional" "override" "postfix" "precedence" "precedencegroup"
             "prefix" "private" "protocol" "public" "repeat" "required" "rethrows" "return"
             "right" "selector" "self" "set" "static" "struct" "subscript" "super" "switch"
             "throw" "throws" "true" "try" "typealias" "unowned" "var" "weak" "where"
             "while" "willSet")
            (julia-mode
             "abstract" "break" "case" "catch" "const" "continue" "do" "else" "elseif"
             "end" "eval" "export" "false" "finally" "for" "function" "global" "if"
             "ifelse" "immutable" "import" "importall" "in" "let" "macro" "module"
             "otherwise" "quote" "return" "switch" "throw" "true" "try" "type"
             "typealias" "using" "while"
             )
            ;; From https://github.com/apache/thrift/blob/master/contrib/thrift.el
            (thrift-mode
             "binary" "bool" "byte" "const" "double" "enum" "exception" "extends"
             "i16" "i32" "i64" "include" "list" "map" "oneway" "optional" "required"
             "service" "set" "string" "struct" "throws" "typedef" "void"
             )
            ;; aliases
            (js2-mode . javascript-mode)
            (js2-jsx-mode . javascript-mode)
            (espresso-mode . javascript-mode)
            (js-mode . javascript-mode)
            (js-jsx-mode . javascript-mode)
            (rjsx-mode . javascript-mode)
            (cperl-mode . perl-mode)
            (jde-mode . java-mode)
            (ess-julia-mode . julia-mode)
            (enh-ruby-mode . ruby-mode))
          )
    ```

<!--list-separator-->

-  company-dabbrev

    <a id="code-snippet--company-dabbrev"></a>
    ```elisp
    (add-to-list 'company-backends 'company-dabbrev-code)
    (setq company-dabbrev-minimum-length 1)
    (setq company-dabbrev-other-buffers t)
    ```


#### ivy ::a search engine for love and life {#ivy-a-search-engine-for-love-and-life}

```elisp
(ivy
 ;; +fuzzy ;; Enables fuzzy completion for Ivy searches.
 ;; +prescient ;; Enables prescient filtering and sorting for Ivy searches.
 ;; +childframe ;; Causes Ivy to display in a floating child frame, above Emacs.
 ;; +icons ;; Enables file icons for switch-{buffer,project}/find-file counsel commands.
 )
```


#### vertico ::the search engine of the future {#vertico-the-search-engine-of-the-future}

```elisp
(vertico
 ;; +icons
 )
```


### :ui {#ui}

```elisp
:ui
<<doom!_modules:ui>>
```


#### deft ::notational velocity for Emacs {#deft-notational-velocity-for-emacs}

```elisp
deft
```

<!--list-separator-->

-  [deft]({{<relref "20210309165015-deft.md#" >}})

    ```elisp
    (use-package! deft
      :after (org org-roam)
      :custom
      (deft-recursive t)
      (deft-use-filter-string-for-filename t)
      (deft-default-extension "org")
      (deft-extensions '("org" "md" "txt"))
      (deft-directory org-directory
        ;; org-roam-directory
        ;; (expand-file-name
        ;;  ;; "deft/"
        ;;  org-roam-directory)
        )
      (deft-strip-summary-regexp
        ;; "\\([\n	]\\|^#\\+[[:upper:]_]+:.*$\\)"
        ":PROPERTIES:\n\\(.+\n\\)+:END:\n"
        )
      (deft-use-filename-as-title t)
      (deft-new-file-format
        "%Y-%m-%dT%H%M"
        ;; "%<%Y-%m-%d>"
        )
      ;; (deft-extensions `("md"))
      ;; (deft-directory _zettelkasten-dir)
      ;; (deft-new-file-format _zettelkasten-format)
      :config
      (map! :map deft-mode-map
            :n "gr"  #'deft-refresh
            :n "C-s" #'deft-filter
            :i "C-n" #'next-line ;; #'deft-new-file
            :i "C-p" #'previous-line
            :i "C-m" #'deft-new-file-named
            :i "C-d" #'deft-delete-file
            :i "C-r" #'deft-rename-file
            :n "r"   #'deft-rename-file
            :n "a"   #'deft-new-file
            :n "A"   #'deft-new-file-named
            :n "d"   #'deft-delete-file
            :n "D"   #'deft-archive-file
            :n "q"   #'kill-current-buffer
            :localleader
            "RET" #'deft-new-file-named
            "a"   #'deft-archive-file
            "c"   #'deft-filter-clear
            "d"   #'deft-delete-file
            "f"   #'deft-find-file
            "g"   #'deft-refresh
            "l"   #'deft-filter
            "n"   #'zetteldeft-new-file ;; #'deft-new-file
            "r"   #'zetteldeft-rename-file
            "s"   #'deft-toggle-sort-method
            "t"   #'deft-toggle-incremental-search)
      ;; :config
      ;; (deft)
      ;; (deft-refresh)
      )
    ```


#### doom ::what makes DOOM look the way it does {#doom-what-makes-doom-look-the-way-it-does}

```elisp
doom
```


#### doom-dashboard ::a nifty splash screen for Emacs {#doom-dashboard-a-nifty-splash-screen-for-emacs}

```elisp
doom-dashboard
```


#### doom-quit ::DOOM quit-message prompts when you quit Emacs {#doom-quit-doom-quit-message-prompts-when-you-quit-emacs}

```elisp
doom-quit
```


#### emoji ; 🙂 {#emoji}

```elisp
(emoji
 +ascii
 +github
 +unicode
 )  ; 🙂
```

<!--list-separator-->

-  [emojify]({{<relref "20220118055248-emojify.md#" >}})

    ```elisp
    (use-package! emojify
      ;; :custom
      ;; (emojify-display-style
      ;;  ;; 'image
      ;;  ;; 'unicode
      ;;  'ascii
      ;;  )
      )
    ```

<!--list-separator-->

-  [emoji-fontset]({{<relref "20220118071913-emoji_fontset.md#" >}})

    ```elisp
    (package! emoji-fontset)
    ```
    
    ```elisp
    (use-package! emoji-fontset
      :config
      (emoji-fontset-enable))
    ```

<!--list-separator-->

-  [emoji-cheat-sheet-plus]({{<relref "20220118074031-emoji_cheat_sheet_plus.md#" >}})

    ```elisp
    (package! emoji-cheat-sheet-plus)
    ```
    
    ```elisp
    (use-package! emoji-cheat-sheet-plus)
    ```

<!--list-separator-->

-  [emoji-display]({{<relref "20220118074742-emoji_display.md#" >}})

    ```elisp
    (package! emoji-display)
    ```
    
    ```elisp
    (use-package! emoji-display
      :config
      (emoji-display-mode))
    ```


#### hl-todo ::highlight TODO/FIXME/NOTE/DEPRECATED/HACK/REVIEW {#hl-todo-highlight-todo-fixme-note-deprecated-hack-review}

```elisp
hl-todo
```


#### hydra {#hydra}

```elisp
hydra
```


#### indent-guides ::highlighted indent columns {#indent-guides-highlighted-indent-columns}

```elisp
indent-guides
```


#### ligatures ::ligatures and symbols to make your code pretty again {#ligatures-ligatures-and-symbols-to-make-your-code-pretty-again}

```elisp
ligatures
```


#### modeline ::snazzy, Atom-inspired modeline, plus API {#modeline-snazzy-atom-inspired-modeline-plus-api}

```elisp
(modeline
 ;; +light
 )
```

```elisp
(setq all-the-icons-scale-factor 1.2)
```


#### nav-flash ::blink cursor line after big motions {#nav-flash-blink-cursor-line-after-big-motions}

```elisp
nav-flash
```


#### ophints ::highlight the region an operation acts on {#ophints-highlight-the-region-an-operation-acts-on}

```elisp
ophints
```


#### popup ::tame sudden yet inevitable temporary windows {#popup-tame-sudden-yet-inevitable-temporary-windows}

```elisp
(popup +defaults)
```


#### treemacs ::a project drawer, like neotree but cooler {#treemacs-a-project-drawer-like-neotree-but-cooler}

```elisp
(treemacs
 (:unless (featurep! :tools lsp +eglot)
  +lsp)
 )
```

<!--list-separator-->

-  treemacs

    ```elisp
    (after! treemacs
      <<use-package_treemacs>>
      )
    ```
    
    <!--list-separator-->
    
    -  use-package
    
        <a id="code-snippet--use-package-treemacs"></a>
        ```elisp
        (use-package! treemacs
          :custom
          (treemacs-follow-mode t)
          (treemacs-tag-follow-mode nil)
          (treemacs-project-follow-mode t)
          ;; (treemacs-width-is-initially-locked nil)
          ;; (treemacs-show-cursor t)
          ;; (treemacs-icon-fallback t)
          ;; (treemacs-recenter-after-file-follow 'on-distance nil nil "Customized with use-package treemacs")
          ;; (treemacs-project-follow-cleanup t)
          ;; (lsp-treemacs-error-list-current-project-only t)
        
          ;; (treemacs-recenter-after-file-follow nil)
          ;; (treemacs-no-png-images (not (display-graphic-p)))
          ;; (treemacs-width 40)
          ;; (treemacs-resize-icons (/ 22 2))
          ;; (treemacs-indentation 1)
          ;; (treemacs-indentation-string
          ;;  (propertize "⫶" 'face 'font-lock-comment-face))
          :init
          <<nw에서 폴더표시 안되는 문제 해결>>
          ;; (setq-default treemacs--width-is-locked nil)
        
          :config
          (treemacs-load-theme doom-themes-treemacs-theme)
        
          (define-key treemacs-mode-map [mouse-1] #'treemacs-single-click-expand-action)
        
          ;; (treemacs-modify-theme "Default"
          ;;   :config
          ;;   (progn
          ;;     (treemacs-create-icon :icon "+" :extensions (dir-closed))
          ;;     (treemacs-create-icon :icon "-" :extensions (dir-open))))
        
          ;; (treemacs-fringe-indicator-mode 'always)
          (pcase (cons (not (null (executable-find "git")))
                       (not (null treemacs-python-executable)))
            (`(t . t)
             (treemacs-git-mode 'deferred))
            (`(t . _)
             (treemacs-git-mode 'simple)))
        
          ;; (progn ;; https://www.reddit.com/r/emacs/comments/jushaq/changing_treemacs_hlline_color_on_doom_emacs/
          ;;   (defun my-remap-hl-line ()
          ;;     "Remap hl-line face."
          ;;     (face-remap-add-relative
          ;;      'hl-line
          ;;      '(:background ,(face-background 'default)
          ;;        :foreground ,(face-foreground 'lazy-highlight))))
        
          ;;   (add-hook 'treemacs-mode-hook #'my-remap-hl-line))
          )
        ```
    
    <!--list-separator-->
    
    -  nw에서 폴더표시 안되는 문제 해결
    
        <a id="code-snippet--nw에서 폴더표시 안되는 문제 해결"></a>
        ```elisp
        ;; fixed: -nw에서 폴더표시 안됨
        (setq-default doom-themes-treemacs-theme
                      (if window-system
                          (progn
                            ;; "doom-colors"
                            "doom-atom"
                            )
                        ;; "doom-atom"
                        "Default"
                        ))
        ```


#### vc-gutter ::vcs diff in the fringe {#vc-gutter-vcs-diff-in-the-fringe}

```elisp
vc-gutter
```


#### vi-tilde-fringe ::fringe tildes to mark beyond EOB {#vi-tilde-fringe-fringe-tildes-to-mark-beyond-eob}

```elisp
vi-tilde-fringe
```


#### window-select ::visually switch windows {#window-select-visually-switch-windows}

```elisp
(window-select
 +switch-window
 ;; +numbers
 )
```


#### workspaces ::tab emulation, persistence & separate workspaces {#workspaces-tab-emulation-persistence-and-separate-workspaces}

```elisp
workspaces
```


#### zen ::distraction-free coding or writing {#zen-distraction-free-coding-or-writing}

```elisp
zen
```

<!--list-separator-->

-  writeroom-mode


### :editor {#editor}

```elisp
:editor
<<doom!_modules:editor>>
```


#### evil ::come to the dark side, we have cookies {#evil-come-to-the-dark-side-we-have-cookies}

```elisp
(evil
 +everywhere
 )
```

<!--list-separator-->

-  conf

    [evil]({{<relref "20210310152039-evil.md#" >}})  
    
    ```elisp
    (setq evil-motion-state-cursor 'box)  ; █
    (setq evil-visual-state-cursor 'box)  ; █
    (setq evil-normal-state-cursor 'box)  ; █
    (setq evil-insert-state-cursor 'bar)  ; ⎸
    (setq evil-emacs-state-cursor  'hbar) ; _
    ```
    
    ```elisp
    ;; Omni-completion
    (map!
     (:when (featurep! :completion company)
      (:prefix "C-x"
       :i "S"    #'company-yasnippet
       :i "C-s"    #'save-buffer))
     )
    ```
    
    ```elisp
    (map! ;; Wordaround config/default/+bindings.el
     (:when (featurep! :completion company)
      :i "C-@"    (cmds! (not (minibufferp)) nil ;; #'company-complete-common
                         )
      :i "C-SPC"  (cmds! (not (minibufferp)) nil ;; #'company-complete-common
                         )
      (:after company
       (:map company-active-map
        "C-SPC"  nil ;; #'company-complete-common
        ))
      )
    
     :m [C-i] nil ;; #'evil-jump-forward
     :m [tab] nil ;; #'evil-jump-item
     )
    ```
    
    ```elisp
    (map! ;; Wordaround config/default/+bindings.el
         :m [C-i] nil ;; #'evil-jump-forward
         :m [tab] nil ;; #'evil-jump-item
         ;; <leader> t --- toggle
         ;; (:prefix-map ("t" . "toggle")
         ;;  :desc "Truncate lines" "t" #'toggle-truncate-lines
         ;;  )
         )
    ```
    
    ```elisp
    ;; Use emacs key on insert mode of evil
    (setq evil-disable-insert-state-bindings t)
    ```


#### file-templates ::auto-snippets for empty files {#file-templates-auto-snippets-for-empty-files}

```elisp
file-templates
```


#### fold ::(nigh) universal code folding {#fold--nigh--universal-code-folding}

```elisp
fold
```


#### format ::automated prettiness {#format-automated-prettiness}

```elisp
(format
 +onsave
 )
```

```elisp
(setq +format-with-lsp nil)
;; (setq-hook! 'web-mode-hook +format-with :none)
```


#### lispy ::vim for lisp, for people who don't like vim {#lispy-vim-for-lisp-for-people-who-don-t-like-vim}

```elisp
lispy
```


#### multiple-cursors ::editing in many places at once {#multiple-cursors-editing-in-many-places-at-once}

```elisp
multiple-cursors
```


#### snippets ::my elves. They type so I don't have to {#snippets-my-elves-dot-they-type-so-i-don-t-have-to}

```elisp
snippets
```


#### word-wrap ::soft wrapping with language-aware indent {#word-wrap-soft-wrapping-with-language-aware-indent}

```elisp
word-wrap
```


### :emacs {#emacs}

```elisp
:emacs
<<doom!_modules:emacs>>
```


#### dired ::making dired pretty [functional] {#dired-making-dired-pretty-functional}

```elisp
(dired
 ;; +ranger
 ;; +icons
 )
```

```elisp
(unpin! dired-git-info)
(package! dired-git-info)
```


#### electric ::smarter, keyword-based electric-indent {#electric-smarter-keyword-based-electric-indent}

```elisp
electric
```


#### ibuffer ::interactive buffer management {#ibuffer-interactive-buffer-management}

```elisp
(ibuffer
 ;; +icons
 )
```


#### undo ::persistent, smarter undo for your inevitable mistakes {#undo-persistent-smarter-undo-for-your-inevitable-mistakes}

```elisp
(undo
 ;; +tree
 )
```


#### vc ::version-control and Emacs, sitting in a tree {#vc-version-control-and-emacs-sitting-in-a-tree}

```elisp
vc
```


### :term {#term}

```elisp
:term
<<doom!_modules:term>>
```


#### vterm ::the best terminal emulation in Emacs {#vterm-the-best-terminal-emulation-in-emacs}

```elisp
vterm
```


### :checkers {#checkers}

```elisp
:checkers
<<doom!_modules:checkers>>
```


#### syntax ::tasing you for every semicolon you forget {#syntax-tasing-you-for-every-semicolon-you-forget}

```elisp
(syntax
 ;; +childframe
 )
```


### :tools {#tools}

```elisp
:tools
<<doom!_modules:tools>>
```


#### debugger ::FIXME stepping through code, to help you add bugs {#debugger-fixme-stepping-through-code-to-help-you-add-bugs}

```elisp
(debugger
 +lsp
 )
```


#### direnv {#direnv}

```elisp
direnv
```


#### editorconfig ::let someone else argue about tabs vs spaces {#editorconfig-let-someone-else-argue-about-tabs-vs-spaces}

```elisp
editorconfig
```


#### eval ::run code, run (also, repls) {#eval-run-code-run--also-repls}

```elisp
(eval
 +overlay
 )
```


#### gist ::interacting with github gists {#gist-interacting-with-github-gists}

```elisp
gist
```


#### lookup ::navigate your code and its documentation {#lookup-navigate-your-code-and-its-documentation}

```elisp
(lookup
 +dictionary
 +offline
 +docsets
 )
```


#### lsp ::M-x vscode {#lsp-m-x-vscode}

```elisp
(lsp
 ;; +peek
 ;; +eglot
 )
```


#### magiti::a git porcelain for Emacs {#magiti-a-git-porcelain-for-emacs}

```elisp
magit
```

<!--list-separator-->

-  [magit]({{<relref "20210312142958-magit.md#" >}})

    ```elisp
    ;; (featurep! :tools magit)
    (after! magit
      <<use-package::magit>>
      )
    ```
    
    <!--list-separator-->
    
    -  use-package
    
        <a id="code-snippet--use-package::magit"></a>
        ```elisp
          (use-package magit
            :commands
            (magit magit-status magit-status-internal)
        
            ;; :custom
            ;; (magit-refresh-status-buffer nil)
            ;; (auto-revert-buffer-list-filter
            ;;  'magit-auto-revert-repository-buffer-p)
        
            ;; (vc-handled-backends nil)
            ;; (magit-display-buffer-function
            ;;  'magit-display-buffer-traditional)
            ;; (magit-revision-show-gravatars
            ;;  '("^Author:     " . "^Commit:     "))
            ;; (magit-diff-refine-hunk 'all)
            :bind
            <<마우스 사용하게>>
            :config
            <<log에 컬러 보기>>
        
            ;; (defun set-commit-fill-column () (setq fill-column 72))
            ;; (add-hook 'git-commit-mode-hook 'set-commit-fill-column)
            )
        ```
    
    <!--list-separator-->
    
    -  이름 변경
    
        <a id="code-snippet--이름 변경"></a>
        ```elisp
        ;; https://xenodium.com/magit-amend-commit-author/
        ;; https://gist.github.com/danielmartin/34bc36dafd8f900de483394087230f48
        (defun change-commit-author (arg)
          "Change the commit author during an interactive rebase in Magit.
        With a prefix argument, insert a new change commit author command
        even when there is already another rebase command on the current
        line.  With empty input, remove the change commit author action
        on the current line, if any."
          (interactive "P")
          (let ((author
                 (magit-transient-read-person
                  "Select a new author for this commit"
                  nil
                  nil)))
            (git-rebase-set-noncommit-action
             "exec"
             (lambda (_)
               (if author
                   (format
                    "git commit --amend --author='%s'" author) ""))
             arg)))
        
        ;; (define-key git-rebase-mode-map (kbd "h") #'change-commit-author)
        ```
    
    <!--list-separator-->
    
    -  Pretty Magit
    
        [Pretty Magit - Integrating commit leaders | Modern Emacs](http://www.modernemacs.com/post/pretty-magit/)  
        
        <a id="code-snippet--Pretty Magit"></a>
        ```elisp
        (require 'dash)
        
        (defmacro pretty-magit (WORD ICON PROPS &optional NO-PROMPT?)
          "Replace sanitized WORD with ICON, PROPS and by default add to prompts."
          `(prog1
               (add-to-list 'pretty-magit-alist
                            (list (rx bow (group ,WORD (eval (if ,NO-PROMPT? "" ":"))))
                                  ,ICON ',PROPS))
             (unless ,NO-PROMPT?
               (add-to-list 'pretty-magit-prompt (concat ,WORD ": ")))))
        
        (setq pretty-magit-alist nil)
        (setq pretty-magit-prompt nil)
        (pretty-magit "Feature" ? (:foreground "slate gray" :height 1.2))
        (pretty-magit "Add"     ? (:foreground "#375E97" :height 1.2))
        (pretty-magit "Fix"     ? (:foreground "#FB6542" :height 1.2))
        (pretty-magit "Clean"   ? (:foreground "#FFBB00" :height 1.2))
        (pretty-magit "Docs"    ? (:foreground "#3F681C" :height 1.2))
        (pretty-magit "master"  ? (:box t :height 1.2) t)
        (pretty-magit "origin"  ? (:box t :height 1.2) t)
        
        (defun add-magit-faces ()
          "Add face properties and compose symbols for buffer from pretty-magit."
          (interactive)
          (with-silent-modifications
            (--each pretty-magit-alist
              (-let (((rgx icon props) it))
                (save-excursion
                  (goto-char (point-min))
                  (while (search-forward-regexp rgx nil t)
                    (compose-region
                     (match-beginning 1) (match-end 1) icon)
                    (when props
                      (add-face-text-property
                       (match-beginning 1) (match-end 1) props))))))))
        
        (advice-add 'magit-status :after 'add-magit-faces)
        (advice-add 'magit-refresh-buffer :after 'add-magit-faces)
        ```
    
    <!--list-separator-->
    
    -  Log에 컬러 보기
    
        <a id="code-snippet--log에 컬러 보기"></a>
        ```elisp
        (setq transient-values
              ;; nil
              '((magit-log:magit-log-mode
                 "-n256"
                 "--graph"
                 "--color"
                 "--decorate"))
              )
        ```
    
    <!--list-separator-->
    
    -  log 설정
    
        <a id="code-snippet--log 설정"></a>
        ```elisp
        (let ((INIT t)
              (STYLE "%Y-%m-%d %H:%M ")
              (WIDTH 'magit-log-margin-width)
              (AUTHOR t)
              (AUTHOR-WIDTH 18))
        
          (setq magit-log-margin
                ;; '(INIT STYLE WIDTH AUTHOR AUTHOR-WIDTH)
                `(,INIT ,STYLE ,WIDTH ,AUTHOR ,AUTHOR-WIDTH)))
        ```
    
    <!--list-separator-->
    
    -  마우스 사용하게
    
        <a id="code-snippet--마우스 사용하게"></a>
        ```elisp
        ;; (define-key magit-status-mode-map [mouse-1] #'magit-section-toggle)
        ;; (define-key magit-hunk-section-map [mouse-1] #'magit-diff-visit-file)
        ;; (define-key magit-file-section-map [mouse-1] #'magit-diff-visit-file)
        ;; (define-key magit-commit-section-map [mouse-1] #'magit-diff-show-or-scroll-up)
        
        (:map magit-status-mode-map ([mouse-1] . #'magit-section-toggle))
        (:map magit-hunk-section-map ([mouse-1] . #'magit-diff-visit-file))
        (:map magit-file-section-map ([mouse-1] . #'magit-diff-visit-file))
        (:map magit-commit-section-map ([mouse-1] . #'magit-diff-show-or-scroll-up))
        ```


#### make ::run make tasks from Emacs {#make-run-make-tasks-from-emacs}

```elisp
make
```


#### pass ::password manager for nerds {#pass-password-manager-for-nerds}

```elisp
(pass
 +auth
 )
```


#### rgb ::creating color strings {#rgb-creating-color-strings}

```elisp
rgb
```


#### tmux ::an API for interacting with tmux {#tmux-an-api-for-interacting-with-tmux}

```elisp
tmux
```


#### upload ::map local to remote projects via ssh/ftp {#upload-map-local-to-remote-projects-via-ssh-ftp}

```elisp
upload
```


### :os {#os}

```elisp
:os
<<doom!_modules:os>>
```


#### macos ::improve compatibility with macOS {#macos-improve-compatibility-with-macos}

```elisp
(:if IS-MAC macos)
```


#### tty ::improve the terminal Emacs experience {#tty-improve-the-terminal-emacs-experience}

```elisp
(tty
 
 )
```


### :lang {#lang}

```elisp
:lang
<<doom!_modules:lang>>
```


#### clojure ::java with a lisp {#clojure-java-with-a-lisp}

```elisp
(clojure
 +lsp
 )
```


#### emacs-lisp ::drown in parentheses {#emacs-lisp-drown-in-parentheses}

```elisp
emacs-lisp
```


#### json ::At least it ain't XML {#json-at-least-it-ain-t-xml}

```elisp
json
```


#### java ::the poster child for carpal tunnel syndrome {#java-the-poster-child-for-carpal-tunnel-syndrome}

```elisp
(java
 +lsp
 ;; +meghanada
 )
```


#### javascript ::all(hope(abandon(ye(who(enter(here)))))) {#javascript-all--hopeabandonyewhoenterhere}

```elisp
(javascript
 +lsp
 )
```


#### latex ::writing papers in Emacs has never been so fun {#latex-writing-papers-in-emacs-has-never-been-so-fun}

```elisp
(latex
 +latexmk
 +cdlatex
 +lsp
 +fold
 )
```


#### markdown ::writing docs for people to ignore {#markdown-writing-docs-for-people-to-ignore}

```elisp
(markdown
 +grip)
```


#### nix ::I hereby declare "nix geht mehr!" {#nix-i-hereby-declare-nix-geht-mehr}

```elisp
nix
```


#### org ::organize your plain life in plain text {#org-organize-your-plain-life-in-plain-text}

```elisp
(org
 +brain
 +hugo
 +journal
 +noter
 +pandoc
 +pomodoro
 +present
 +pretty
 +roam2
 )
```

<!--list-separator-->

-  conf

    <!--list-separator-->
    
    -  [org]({{<relref "20220109191548-org.md#" >}})
    
        \#+BEGIN\_t  
        
        :ID:       5e65b19a-dc61-4093-9ac4-08d265a6108e  


## config {#config}

```elisp
(require 'org-attach-git)
(after! org
  (use-package! org
    :custom
    (org-open-directory-means-index-dot-org t)
    (org-indent-mode-turns-off-org-adapt-indentation t)
    (org-indent-mode-turns-on-hiding-stars nil)
    ))
```

\#+END\_t  

<!--list-separator-->

-  [org-habit]({{<relref "20211002175812-org_habit.md#" >}})

    ```elisp
    (require 'org-habit)
    (setq org-habit-show-habits-only-for-today t)
    (setq org-habit-show-all-today nil)
    ```

<!--list-separator-->

-  org export시 headline 무시

    ```elisp
    (require 'ox-extra)
    (ox-extras-activate '(ignore-headlines))
    ```

<!--list-separator-->

-  org에 indent 안되게

    ```elisp
    ;; #+STARTUP: indent
    ;; #+STARTUP: noindent"
    (setq org-startup-indented nil)
    ;; (setq org-adapt-indentation t)
    ```
    
    ```elisp
    (use-package! org-indent
      :config
      (setq org-indent-boundary-char 124)
      (setq org-indent-indentation-per-level 1))
    ```

<!--list-separator-->

-  [org-mouse]({{<relref "20210629075712-org_mouse.md#" >}})

    ```elisp
    (use-package! org-mouse
      :custom
      (org-mouse-features
       '(context-menu move-tree yank-link activate-stars activate-bullets activate-checkboxes)))
    ```

<!--list-separator-->

-  [ox]({{<relref "20210310114523-ox.md#" >}})

    ```elisp
    (use-package! ox
      :custom
      (org-export-preserve-breaks t) ;; #+OPTIONS: \n:t
      (org-export-with-properties nil) ;; #+OPTIONS: prop:t
      :config
      <<org-mode exports _ as subscripts and ^ as superscripts>>
      )
    ```
    
    <!--list-separator-->
    
    -  org-mode exports \_ as subscripts and ^ as superscripts
    
        [org-mode: How to export underscore as underscore instead of highlight in HTML...](https://emacs.stackexchange.com/questions/10549/org-mode-how-to-export-underscore-as-underscore-instead-of-highlight-in-html)  
        
        <a id="code-snippet--org export시 문제 해결"></a>
        ```elisp
        (setq org-export-with-sub-superscripts nil) ;; #+OPTIONS: ^:nil
        ```

<!--list-separator-->

-  flags

    <!--list-separator-->
    
    -  +brain ;; Enables [org-brain](https://github.com/Kungsgeten/org-brain) integration.
    
        ```elisp
        +brain
        ```
    
    <!--list-separator-->
    
    -  +hugo ;; Enables integration with [hugo](https://gohugo.io) to export from Emacs well-formed [blackfriday](https://github.com/russross/blackfriday)) markdown.
    
        ```elisp
        +hugo
        ```
        
        <!--list-separator-->
        
        -  [ox-hugo]({{<relref "20210731193747-ox_hugo.md#" >}})
        
            ```elisp
            (use-package! ox-hugo
              :after ox
              :custom
              (org-hugo-use-code-for-kbd t)
              (org-hugo-link-desc-insert-type t)
              (org-hugo-export-with-toc nil)
              (org-hugo-base-dir "~/public_html/")
              (org-hugo-section "braindump")
              (org-hugo-auto-set-lastmod t)
              (org-hugo-delete-trailing-ws t nil nil "Customized with use-package ox-hugo")
            
              :config
              <<hugo용 org-capture 설정>>
              <<braindump를 위한 설정>>
              )
            ```
            
            <!--list-separator-->
            
            -  braindump를 위한 설정
            
                <a id="code-snippet--braindump를 위한 설정"></a>
                ```elisp
                ;; roam 파일 저장시 export
                (defun org-hugo--org-roam-save-buffer (&optional no-trace-links)
                  "On save export to hugo"
                  (interactive)
                  (when (org-roam-file-p)
                    (org-hugo-export-wim-to-md)))
                
                (defun braindump-sync ()
                  "저장시 export 하기 켜기"
                  (interactive)
                  (add-hook 'after-save-hook #'org-hugo--org-roam-save-buffer))
                ;; (braindump-sync)
                
                (defun braindump-unsync ()
                  "저장시 export 하기 끄기"
                  (interactive)
                  (remove-hook 'after-save-hook #'org-hugo--org-roam-save-buffer))
                
                (require 'find-lisp)
                (defun hugo/publish (file)
                  (with-current-buffer (find-file-noselect file)
                    (setq org-hugo-base-dir "~/public_html")
                    (let ((org-id-extra-files
                           (find-lisp-find-files org-roam-directory "\.org$")))
                      (org-hugo-export-wim-to-md nil t t t))))
                
                (defun braindump-sync-all()
                  "모든 roam 파일 export 하기"
                  (interactive)
                  (setenv "EXPORT_HUGO_LOCALE" "kr")
                  (dolist (fil
                           (org-roam-list-files)
                           ;; (split-string
                           ;;  (string-trim
                           ;;   (shell-command-to-string
                           ;;    (concat "ls " org-roam-directory "*.org"))))
                           )
                    (hugo/publish fil)))
                ```
            
            <!--list-separator-->
            
            -  hugo용 org-capture 설정
            
                <a id="code-snippet--hugo용 org-capture 설정"></a>
                ```elisp
                ;; https://ox-hugo.scripter.co/doc/org-capture-setup/#an-org-capture-template
                ;; Populates only the EXPORT_FILE_NAME property in the inserted headline.
                (with-eval-after-load
                    'org-capture
                  (defun org-hugo-new-subtree-post-capture-template ()
                    "Returns `org-capture' template string for new Hugo post.
                See `org-capture-templates' for more information."
                    (let* ((title (read-from-minibuffer "Post Title: ")) ;Prompt to enter the post title
                           (fname (org-hugo-slug title)))
                      (mapconcat
                       #'identity
                       `(
                         ,(concat "* TODO " title)
                         ":PROPERTIES:"
                         ,(concat ":EXPORT_FILE_NAME: " fname)
                         ;; ,(concat ":CREATED: " datetimezone)
                         ":END:"
                         "%?\n%i\n%a"
                         )
                       ;;Place the cursor here finally
                       "\n")))
                
                  (add-to-list
                   'org-capture-templates
                   '("h" ;`org-capture' binding + h
                     "Hugo post"
                     entry
                     ;; It is assumed that below file is present in `org-directory'
                     ;; and that it has a "Blog Ideas" heading. It can even be a
                     ;; symlink pointing to the actual location of all-posts.org!
                     (file+olp "public/all-posts.org" "Posts/")
                     (function org-hugo-new-subtree-post-capture-template)
                     :prepend t
                     )))
                ```
    
    <!--list-separator-->
    
    -  +journal ;; Enables [org-journal](https://github.com/bastibe/org-journal) integration.
    
        ```elisp
        +journal
        ```
    
    <!--list-separator-->
    
    -  +noter ;; Enables org-noter integration. Keeps notes in sync with a document. equires [pdf-tools](https://github.com/politza/pdf-tools) (`:tools pdf`) or [DocView](https://www.gnu.org/software/emacs/manual/html%5Fnode/emacs/Document-View.html) or [nov.el](https://github.com/wasamasa/nov.el) to be enabled.
    
        ```elisp
        +noter
        ```
    
    <!--list-separator-->
    
    -  +pandoc ;; Enables pandoc integration into the Org exporter.
    
        ```elisp
        +pandoc
        ```
    
    <!--list-separator-->
    
    -  +pomodoro ;; Enables a pomodoro timer for clocking time on tasks.
    
        ```elisp
        +pomodoro
        ```
    
    <!--list-separator-->
    
    -  +present ;; Enables integration with reveal.js, beamer and org-tree-slide, so macs can be used for presentations. It automatically downloads [reveal.js](https://github.com/hakimel/reveal.js).
    
        ```elisp
        +present
        ```
    
    <!--list-separator-->
    
    -  +pretty ;; Enables pretty unicode symbols for bullets and priorities, and etter syntax highlighting for latex. Keep in mind: this can be expensive. If rg becomes too slow, it'd be wise to disable this flag.
    
        ```elisp
        +pretty
        ```
    
    <!--list-separator-->
    
    -  +roam2 ;; Enables integration with [org-roam v2](https://github.com/org-roam/org-roam). This requires <kbd>sqlite3</kbd> to be installed on your system. Incompatible with `+roam`.
    
        ```elisp
        +roam2
        ```
        
        ```elisp
        (setq +org-roam-open-buffer-on-find-file nil)
        ```
        
        <!--list-separator-->
        
        -  [org-roam]({{<relref "20210309161957-org_roam.md#" >}})
        
            ```elisp
            <<use-package/org-roam>>
            ```
            
            <!--list-separator-->
            
            -  use-package
            
                <a id="code-snippet--use-package-org-roam"></a>
                ```elisp
                ;; (use-package! emacsql-sqlite3)
                
                ;; (use-package! emacsql-sqlite
                ;;   :custom
                ;;   (emacsql-sqlite-executable-path
                ;;    (executable-find "sqlite3")))
                
                (use-package! org-roam
                  ;; :unless
                  ;; (or
                  ;;  (featurep! :lang org +roam)
                  ;;  (eq system-type 'windows-nt))
                
                  :custom
                  ;; (org-roam-directory (expand-file-name "roam/" org-directory))
                  (org-roam-file-extensions '("org" "md"))
                  (org-roam-file-exclude-regexp ".*\\.org\\.org")
                  (org-roam-database-connector
                   'sqlite
                   ;; 'sqlite3
                   ;; 'libsqlite3
                   )
                  ;; (org-roam-db-node-include-function
                  ;;  (lambda ()
                  ;;    (not (member "ATTACH" (org-get-tags)))))
                
                  (org-roam-db-update-on-save t)
                  (org-roam-mode-section-functions
                   '(org-roam-backlinks-section
                     org-roam-reflinks-section
                     ;; org-roam-unlinked-references-section
                     ))
                  (org-roam-completion-everywhere t)
                  ;; (org-roam-v2-ack t)
                  ;; (org-roam-verbose t)
                  :config
                  (setq org-roam-db-gc-threshold most-positive-fixnum)
                  (org-roam-db-autosync-mode)
                  )
                
                ;; Since the org module lazy loads org-protocol (waits until an org URL is
                ;; detected), we can safely chain `org-roam-protocol' to it.
                (use-package! org-roam-protocol
                  :after org-protocol)
                ```
        
        <!--list-separator-->
        
        -  [md-roam]({{<relref "20211231173710-md_roam.md#" >}})
        
            ```elisp
            (package! md-roam
              :recipe
              (:host github :repo "nobiot/md-roam"))
            ```
            
            ```elisp
            <<use-package/md-roam>>
            ```
            
            <!--list-separator-->
            
            -  use-package
            
                <a id="code-snippet--use-package-md-roam"></a>
                ```elisp
                (use-package! md-roam
                  :config
                  (md-roam-mode 1) ; md-roam-mode must be active before org-roam-db-sync
                  (setq md-roam-file-extension "md") ; default "md". Specify an extension such as "markdown"
                  (org-roam-db-autosync-mode 1) ; autosync-mode triggers db-sync. md-roam-mode must be already active
                  <<capture-template>>
                  ;; <<keymap>>
                
                  (with-eval-after-load 'markdown-mode
                    (advice-add #'markdown-indent-line :before-until #'completion-at-point))
                  )
                ```
                
                <a id="code-snippet--capture-template"></a>
                ```elisp
                (add-to-list 'org-roam-capture-templates
                               '("m" "Markdown" plain "" :target
                                 (file+head "%<%Y-%m-%dT%H%M%S>.md"
                                            "---\ntitle: ${title}\nid: %<%Y-%m-%dT%H%M%S>\ncategory: \n---\n")
                                 :unnarrowed t))
                ```
                
                <a id="code-snippet--keymap"></a>
                ```elisp
                ;;;; Org-roam
                (define-key global-map (kbd "C-c n f") #'org-roam-node-find)
                (define-key global-map (kbd "C-c n c") #'org-roam-capture)
                (define-key global-map (kbd "C-c n i") #'org-roam-node-insert)
                (define-key global-map (kbd "C-c n l") #'org-roam-buffer-toggle)
                ```
        
        <!--list-separator-->
        
        -  [org-roam-ui]({{<relref "20210805063403-org_roam_ui.md#" >}})
        
            ```elisp
            (package! websocket)
            (unpin! org-roam)
            (package! org-roam-ui
              ;; :recipe (:host github :repo "org-roam/org-roam-ui" :files ("*.el" "out"))
              )
            ```
            
            ```elisp
            (use-package! websocket
              :after org-roam)
            
            (use-package! org-roam-ui
              :after org-roam ;; or :after org
              ;;         normally we'd recommend hooking orui after org-roam, but since org-roam does not have
              ;;         a hookable mode anymore, you're advised to pick something yourself
              ;;         if you don't care about startup time, use
              ;; :hook (after-init . org-roam-ui-mode)
              :custom
              (org-roam-ui-sync-theme t)
              (org-roam-ui-follow t)
              (org-roam-ui-update-on-save t)
              (org-roam-ui-open-on-start t))
            ```
        
        <!--list-separator-->
        
        -  [gkroam]({{<relref "20210629041222-gkroam.md#" >}})
        
            ```elisp
            (package! gkroam)
            ```
            
            ```elisp
            (use-package gkroam
              :hook (after-init . gkroam-mode)
              :custom
              (gkroam-root-dir (expand-file-name "roam/" org-directory))
              (gkroam-prettify-page-p t)
              (gkroam-show-brackets-p nil)
              (gkroam-use-default-filename t)
              (gkroam-window-margin 4)
              :bind
              (:map gkroam-mode-map
                    (("C-c r I" . gkroam-index)
                     ("C-c r d" . gkroam-daily)
                     ("C-c r D" . gkroam-delete)
                     ("C-c r f" . gkroam-find)
                     ("C-c r i" . gkroam-insert)
                     ("C-c r n" . gkroam-dwim)
                     ("C-c r e" . gkroam-link-edit)
                     ("C-c r u" . gkroam-show-unlinked)
                     ("C-c r p" . gkroam-toggle-prettify)
                     ("C-c r t" . gkroam-toggle-brackets)
                     ("C-c r R" . gkroam-rebuild-caches)
                     ("C-c r g" . gkroam-update))))
            ```
        
        <!--list-separator-->
        
        -  [org-ref-prettify]({{<relref "20211228185048-org_ref_prettify.md#" >}})
        
            ```elisp
            (package! org-ref-prettify)
            ```
            
            ```elisp
            (use-package! org-ref-prettify
              :config
              (setq org-ref-prettify-bind-edit-keys nil)
              )
            ```


#### plantuml ::diagrams for confusing people more {#plantuml-diagrams-for-confusing-people-more}

```elisp
plantuml
```


#### rest ::Emacs as a REST client {#rest-emacs-as-a-rest-client}

```elisp
rest
```


#### rust ::Fe2O3.unwrap().unwrap().unwrap().unwrap() {#rust-fe2o3-dot-unwrap-dot-unwrap-dot-unwrap-dot-unwrap}

```elisp
(rust
 +lsp)
```


#### sh ::she sells {ba,z,fi}sh shells on the C xor {#sh-she-sells-ba-z-fi-sh-shells-on-the-c-xor}

```elisp
(sh
 +lsp
 +fish
 ;; +powershell
 )
```


#### web ::the tubes {#web-the-tubes}

```elisp
web
```

<!--list-separator-->

-  [web-mode]({{<relref "20210606111642-web_mode.md#" >}})

    ```elisp
    (use-package! web-mode
      ;; :after (css-mode sgml-mode)
      :custom
      (web-mode-enable-part-face t)
      (web-mode-indent-style 2)
      (web-mode-enable-current-column-highlight t)
      (web-mode-enable-current-element-highlight t)
      (web-mode-enable-css-colorization t)
      (web-mode-enable-comment-interpolation t)
      (web-mode-enable-auto-indentation t)
      (web-mode-enable-auto-closing t)
      (web-mode-enable-auto-pairing t)
      (web-mode-enable-auto-opening t)
      (web-mode-enable-auto-quoting t)
      (web-mode-enable-auto-expanding t)
      (web-mode-enable-curly-brace-indentation t)
      (web-mode-enable-control-block-indentation t)
      (web-mode-enable-html-entities-fontification t)
      (web-mode-enable-block-face t)
      (web-mode-enable-part-face t)
      (web-mode-enable-element-content-fontification t)
      (web-mode-enable-element-tag-fontification t)
      (web-mode-enable-front-matter-block t)
      (web-mode-enable-engine-detection t)
      (web-mode-enable-optional-tags t)
      (web-mode-comment-style 1)
      (web-mode-comment-formats
       '(("java" . "/*")
         ("javascript" . "//")
         ("typescript" . "//")
         ("php" . "/*")
         ("css" . "/*")
         ("tsx" . "//")
         ("jsx" . "//")))
    
      :config
      ;; ;; https://stackoverflow.com/questions/60200613/can-emacs-lisp-assign-a-lambda-form-to-a-variable-like-scheme
      (fset 'web-mode-code-indent-offset #'(lambda () standard-indent))
      ;; (setq web-mode-code-indent-offset (web-mode-code-indent-offset))
    
      ;; (setq web-mode-markup-indent-offset sgml-basic-offset)
      ;; (setq web-mode-css-indent-offset css-indent-offset)
    
      (setq-default web-mode-style-padding 2)
      (setq-default web-mode-script-padding 2)
    
      (add-to-list 'web-mode-comment-formats '("jsx" . "/*" ))
      (add-to-list 'web-mode-comment-formats '("tsx" . "/*" ))
      )
    ```


#### yaml ::JSON, but readable {#yaml-json-but-readable}

```elisp
yaml
```


### :email {#email}

```elisp
:email
<<doom!_modules:email>>
```


### :app {#app}

```elisp
:app
<<doom!_modules:app>>
```


#### calendar {#calendar}

```elisp
calendar
```

<!--list-separator-->

-  [calendar]({{<relref "20210324142557-calendar.md#" >}})

    ```elisp
    (use-package! calendar
      :custom
      (calendar-week-start-day 1) ;; monday: 1, sunday: 0
    
      :config
      ;; (setq holiday-other-holidays '((holiday-sexp 9 17 2021 "일괄연차")))
      (setq calendar-holidays
            (append
             ;; holiday-general-holidays
             holiday-local-holidays
             holiday-other-holidays
             ;; holiday-christian-holidays
             ;; holiday-hebrew-holidays
             ;; holiday-islamic-holidays
             ;; holiday-bahai-holidays
             ;; holiday-oriental-holidays
             ;; holiday-solar-holidays
             ))
      (progn
        ;; [[https://www.emacswiki.org/emacs/CalendarWeekNumbers][EmacsWiki: Calendar Week Numbers]]
        (copy-face font-lock-constant-face
                   'calendar-iso-week-face)
        ;; (set-face-attribute 'calendar-iso-week-face nil :height 0.7)
        (setq calendar-intermonth-text
              '(propertize
                (format
                 "%2d"
                 (ca
                  (calendar-iso-from-absolute
                   (calendar-absolute-from-gregorian
                    (list month day year)))))
                'font-lock-face
                'calendar-iso-week-face))))
    ```

<!--list-separator-->

-  [calfw]({{<relref "20210616075418-calfw.md#" >}})

    ```elisp
    (require 'calfw)
    (require 'calfw-cal)
    (require 'calfw-ical)
    (require 'calfw-org)
    
    (defun _달력 ()
      (interactive)
      (cfw:open-calendar-buffer
       :contents-sources
       (list
        (cfw:org-create-source "Green")  ; orgmode source
        ;; (cfw:cal-create-source "Orange") ; diary source
        (cfw:ical-create-source "Moon" "~/moon.ics" "Gray")  ; ICS source1
        (cfw:ical-create-source
         "대한민국 공휴일"
         "https://calendar.google.com/calendar/ical/ko.south_korea%23holiday%40group.v.calendar.google.com/public/basic.ics"
         "IndianRed"
         ))))
    ```


### :config {#config}

```elisp
:config
<<doom!_modules:config>>
```


#### default {#default}

```elisp
(default
  +bindings
  +smartparens
  )
```


## Default {#default}


### [expand-region]({{<relref "20210420034003-expand_region.md#" >}}) {#expand-region--20210420034003-expand-region-dot-md}

```elisp
(use-package! expand-region
  ;; :bind
  ;; ("" . er/expand-region)
  :config
  ;; vib
  (map! :nv "C-=" #'er/contract-region
        :nv "C-+" #'er/expand-region))
```


### [org-id]({{<relref "20211228082946-org_id.md#" >}}) {#org-id--20211228082946-org-id-dot-md}

```elisp
(use-package! org-id
  :after org-roam
  :config
  ;; (require 'find-lisp)
  ;; (mapcar (lambda (elt) (add-to-list 'org-agenda-text-search-extra-files elt))
  ;;         (find-lisp-find-files default-directory "\\.org"))

  (setq org-id-link-to-org-use-id t)
  ;; (setq org-id-extra-files 'org-agenda-text-search-extra-files)
  (setq org-id-extra-files (org-roam--list-files org-roam-directory))
  ;; (org-id-update-id-locations)
  )
```


### [warnings]({{<relref "20210706061815-warnings.md#" >}}) {#warnings--20210706061815-warnings-dot-md}

```elisp
(setq warning-minimum-level :error)
;; (setq warning-suppress-types '((mouse-leave-buffer-hook)))
```


### [server]({{<relref "20220117175111-server.md#" >}}) {#server--20220117175111-server-dot-md}

```elisp
(unless (server-running-p) (server-start))
```


## External Packages {#external-packages}


### [apheleia]({{<relref "20210502034311-apheleia.md#" >}}) {#apheleia--20210502034311-apheleia-dot-md}

```elisp
(package! apheleia)
```

```elisp
(use-package! apheleia
  :config
  (apheleia-global-mode +1))
```


### [git-auto-commit-mode]({{<relref "20210312140854-git_auto_commit_mode.md#" >}}) {#git-auto-commit-mode--20210312140854-git-auto-commit-mode-dot-md}

```elisp
(package! git-auto-commit-mode)
```

```elisp
;; (custom-set-variables
;;  ;; custom-set-variables was added by Custom.
;;  ;; If you edit it by hand, you could mess it up, so be careful.
;;  ;; Your init file should contain only one such instance.
;;  ;; If there is more than one, they won't work right.
;;  '(safe-local-variable-values
;;    '((gac-ask-for-summary-p)
;;      (gac-ask-for-summary-p . t)
;;      (gac-silent-message-p . t)
;;      (gac-automatically-push-p . t)) nil nil "Customized with use-package files"))

(use-package! git-auto-commit-mode
  :custom
  (safe-local-variable-values
   '((gac-ask-for-summary-p)
     (gac-ask-for-summary-p . t)
     (gac-silent-message-p . t)
     (gac-automatically-push-p . t)))
  (gac-ask-for-summary-p nil)
  (gac-silent-message-p t)
  ;; (gac-automatically-push-p t)
  )
```


### [mpages]({{<relref "20210819072307-mpages.md#" >}}) {#mpages--20210819072307-mpages-dot-md}

```elisp
(package! mpages)
```

```elisp
(use-package! mpages
  ;; :after
  ;; (
  ;;  org-roam
  ;;  writeroom-mode
  ;;  )
  :config
  (setq mpages-content-directory (expand-file-name "daily/mpage" org-roam-directory))
  ;; (defun mpages-open-today ()
  ;;   "Open a Morning Pages file for today."
  ;;   (find-file (concat (file-name-as-directory mpages-content-directory) (format-time-string "%Y%m%d") ".txt"))
  ;;   (_writer-mode +1))
  )
```


### [easy-hugo]({{<relref "20210309170008-easy_hugo.md#" >}}) {#easy-hugo--20210309170008-easy-hugo-dot-md}

```elisp
(package! easy-hugo)
```

```elisp
<<use-package/easy-hugo>>
```


#### use-package {#use-package}

<a id="code-snippet--use-package-easy-hugo"></a>
```elisp
(use-package! easy-hugo
  :bind
  ("C-c h" . easy-hugo)
  :init
  (setq easy-hugo-basedir (file-name-as-directory (expand-file-name "public_html" (getenv "HOME"))))
  (setq easy-hugo-postdir "content/posts/")
  (if (file-exists-p easy-hugo-basedir)
      (let ((target (expand-file-name
                     easy-hugo-postdir
                     easy-hugo-basedir)))
        (unless (file-exists-p target)
          (make-directory target t))))

  :custom
  (easy-hugo-additional-help nil)
  (easy-hugo-default-ext ".org")
  (easy-hugo-org-header (equal easy-hugo-default-ext ".org"))

  (easy-hugo-additional-help t)

  :custom-face
  (easy-hugo-help-face ((t (:inherit package-help-section-name
                            :extend t
                            :weight bold))))

  :config
  (defun easy-hugo--org-headers (file)
    "Return a draft org mode header string for a new article as FILE."
    (let ((datetimezone
           (concat
            (format-time-string "%Y-%m-%dT%T")
            (easy-hugo--orgtime-format (format-time-string "%z")))))
      (concat
       ;; "#+TITLE: "
       file
       "\n#+DATE: " datetimezone
       "\n#+PUBLISHDATE: " datetimezone
       "\n#+DRAFT: t"
       "\n#+TAGS[]: nil"
       "\n\n")))
  (add-to-list 'evil-emacs-state-modes 'easy-hugo-mode)
  (let ((_port "8010"))
    (setq easy-hugo-server-flags "-p")
    (setq easy-hugo-server-value _port)
    (setq easy-hugo-server-flags2 "-D")
    (setq easy-hugo-preview-url (format "http://localhost:%s/" _port))
    ))
```


### [org-transclusion]({{<relref "20211104030500-org_transclusion.md#" >}}) {#org-transclusion--20211104030500-org-transclusion-dot-md}

```elisp
(package! org-transclusion
  ;; :recipe (:host github
  ;;          :repo "nobiot/org-transclusion"
  ;;          :branch "main"
  ;;          :files ("*.el"))
  )
```

```elisp
(use-package! org-transclusion
  :defer
  :after org
  :init
  (map!
   :map global-map "<f12>" #'org-transclusion-add
   :leader
   :prefix "n"
   :desc "Org Transclusion Mode" "t" #'org-transclusion-mode))
```


### [engine-mode]({{<relref "20210923081339-engine_mode.md#" >}}) {#engine-mode--20210923081339-engine-mode-dot-md}

```elisp
(package! engine-mode)
```

```elisp
(use-package! engine-mode
  ;; :custom
  ;; (engine/keybinding-prefix "C-c C-x /")
  :config
  (defengine google
    "https://www.google.com/search?q=%s"
    :keybinding "g"
    )
  (defengine github
    "https://github.com/search?ref=simplesearch&q=%s"
    :keybinding "c")
  (defengine duckduckgo
    "https://duckduckgo.com/?q=%s"
    :keybinding "k")
  (defengine endictnaver
    "https://en.dict.naver.com/#/search?query=%s"
    :keybinding "d")
;; https://map.naver.com/v5/directions/14137807.120189447,4512662.6768430285,서울특별시%20용산구%20보광로%2014(보광동%20448),,ADDRESS_POI/14143648.844239745,4515068.058803242,소문난성수감자탕,1169625359,PLACE_POI/-/transit?c=14139741.8529394,4515033.9426174,14,0,0,0,dh
  (defengine map
    "https://map.naver.com/v5/search/%s"
    :keybinding "m")
  (defengine lfjira
    "https://jira.lfcorp.com/browse/%s"
    :keybinding "j")
  (engine-mode +1))
```


### [dimmer]({{<relref "20210312175236-dimmer.md#" >}}) {#dimmer--20210312175236-dimmer-dot-md}

```elisp
(package! dimmer)
```

```elisp
(use-package! dimmer
  :custom
  (dimmer-fraction 0.2)
  (dimmer-adjustment-mode
   :both
   ;; :foreground
   )
  (dimmer-use-colorspace
   :cielab
   ;; :hsl
   ;; :rgb
   )
  ;; (dimmer-exclusion-predicates '(helm--alive-p window-minibuffer-p))
  ;; (dimmer-exclusion-regexp-list
  ;;  '("^\\*[h|H]elm.*\\*" "^\\*Minibuf-[0-9]+\\*"
  ;;    "^.\\*which-key\\*$" "^*Messages*" "*LV*"))
  :config
  (dimmer-configure-company-box)
  (dimmer-configure-posframe)
  (dimmer-configure-helm)
  (dimmer-configure-gnus)
  (dimmer-configure-hydra)
  (dimmer-configure-magit)
  (dimmer-configure-org)
  (dimmer-configure-which-key)
  (dimmer-config-change-handler)

  (dimmer-mode +1)

  ;; (add-hook 'focus-in-hook 'dimmer-config-change-hook)
  ;; (add-hook 'focus-out-hook 'dimmer-dim-all)
  )
```


### [tmux-pane]({{<relref "20210312174644-tmux_pane.md#" >}}) {#tmux-pane--20210312174644-tmux-pane-dot-md}

```elisp
(package! tmux-pane)
```

```elisp
(use-package! tmux-pane)
```


### [emamux]({{<relref "20210319214127-emamux.md#" >}}) {#emamux--20210319214127-emamux-dot-md}

```elisp
(package! emamux)
```

```elisp
(use-package! emamux)
```


### [nummm-mode]({{<relref "20210310151442-nummm_mode.md#" >}}) {#nummm-mode--20210310151442-nummm-mode-dot-md}

```elisp
(package! nummm-mode)
```

```elisp
;; I'm activating mini-modeline after smart-mode-line
(use-package! nummm-mode
  :commands (nummm-mode)
  :hook
  (after-init . (lambda ()
                  (nummm-mode +1)))
  :config
  (nummm-mode +1))
```


### [org-preview-html]({{<relref "20211106075420-org_preview_html.md#" >}}) {#org-preview-html--20211106075420-org-preview-html-dot-md}

```elisp
(package! org-preview-html)
```

```elisp
(use-package! org-preview-html)
```


### [paradox]({{<relref "20210320071358-paradox.md#" >}}) {#paradox--20210320071358-paradox-dot-md}

```elisp
(package! paradox)
```

```elisp
(use-package! paradox
  :custom
  (paradox-column-width-package
   ;; 18
   30
   )
  (paradox-column-width-version
   ;; 9
   13
   )
  ;; (paradox-display-buffer-name t)
  (paradox-display-download-count t)
  (paradox-display-star-count t)
  ;; (paradox-github-token "")
  :config
  (paradox-enable))
```


### [browse-url-dwim]({{<relref "20210318145909-browse_url_dwim.md#" >}}) {#browse-url-dwim--20210318145909-browse-url-dwim-dot-md}

```elisp
(package! browse-url-dwim)
```

```elisp
(use-package! browse-url-dwim
  :custom
  (browse-url-dwim-always-confirm-extraction nil)
  :config
  (browse-url-dwim-mode 1))
```


### [deadgrep]({{<relref "20211107205409-deadgrep.md#" >}}) {#deadgrep--20211107205409-deadgrep-dot-md}

```elisp
(package! deadgrep)
```

```elisp
(use-package! deadgrep
  :config
  (global-set-key (kbd "<f5>") #'deadgrep))
```


### [zetteldeft]({{<relref "20210629044325-zetteldeft.md#" >}}) {#zetteldeft--20210629044325-zetteldeft-dot-md}

```elisp
(package! zetteldeft)
```

```elisp
(use-package! zetteldeft
  :after deft
  :custom
  (zetteldeft-new-filename-to-kill-ring t)
  :config

  (zetteldeft-set-classic-keybindings)

  (setq zetteldeft-link-indicator "§"
        zetteldeft-id-format "%Y-%m-%d-%H%M"
        zetteldeft-id-regex "[0-9]\\{4\\}\\(-[0-9]\\{2,\\}\\)\\{3\\}"
        zetteldeft-tag-regex "[#@][a-z-]+")
  )
```


### 글쓰기/ {#글쓰기}


#### [olivetti]({{<relref "20211111211615-olivetti.md#" >}}) {#olivetti--20211111211615-olivetti-dot-md}

```elisp
(package! olivetti)
```

```elisp
(use-package! olivetti)
```


#### [focus]({{<relref "20210313032630-focus.md#" >}}) {#focus--20210313032630-focus-dot-md}

```elisp
(package! focus)
```

```elisp
(use-package! focus
  :custom-face
  (focus-unfocused ((t (:foreground "dimgray"))))
  ;; (focus-unfocused ((t (:inherit cfw:face-disable))))
  )
```


#### [perfect-margin]({{<relref "20211109064035-perfect_margin.md#" >}}) {#perfect-margin--20211109064035-perfect-margin-dot-md}

```elisp
(package! perfect-margin)
```

```elisp
(use-package! perfect-margin
  :config
  ;; (perfect-margin-mode 1)
  (setq perfect-margin-visible-width
        81
        ;; 121
        )

  (dolist (margin '("<left-margin> " "<right-margin> "))
    (global-set-key (kbd (concat margin "<mouse-1>")) 'ignore)
    (global-set-key (kbd (concat margin "<mouse-3>")) 'ignore)
    (dolist (multiple '("" "double-" "triple-"))
      (global-set-key (kbd (concat margin "<" multiple "wheel-up>")) 'mwheel-scroll)
      (global-set-key (kbd (concat margin "<" multiple "wheel-down>")) 'mwheel-scroll))))
```


#### [centered-cursor-mode]({{<relref "20210411074654-centered_cursor_mode.md#" >}}) {#centered-cursor-mode--20210411074654-centered-cursor-mode-dot-md}

```elisp
(package! centered-cursor-mode)
```

```elisp
(use-package! centered-cursor-mode
  :config
  ;; (setq ccm-vpos-init ;; '(round (* 21 (ccm-visible-text-lines)) 34)
  ;;       'center
  ;;       )
  (setq ccm-vpos-inverted -1)
  (setq ccm-recenter-at-end-of-file t)
  ;; (global-centered-cursor-mode -1)
  )
```


#### 글쓰기 모드 {#글쓰기-모드}

```elisp
(defun _집중모드 ()
  (interactive)
  ;; (olivetti-mode +1)
  (perfect-margin-mode +1)
  (centered-cursor-mode +1))

(defun _집중끄기 ()
  (interactive)
  ;; (olivetti-mode -1)
  (perfect-margin-mode -1)
  (centered-cursor-mode -1))

(defun _글쓰기모드 ()
  (interactive)
  (_집중모드)
  (hl-line-mode -1)
  (focus-mode +1))

(defun _글쓰기끄기 ()
  (interactive)
  (_집중끄기)
  (hl-line-mode +1)
  (focus-mode -1))
```


### [color-theme-approximate]({{<relref "20211111065401-color_theme_approximate.md#" >}}) {#color-theme-approximate--20211111065401-color-theme-approximate-dot-md}

```elisp
(package! color-theme-approximate)
```

```elisp
(use-package! color-theme-approximate
  :config
  (color-theme-approximate-on))
```


### [seethru]({{<relref "20211019041543-seethru.md#" >}}) :: 투명 {#seethru--20211019041543-seethru-dot-md--투명}

```elisp
(package! seethru)
```

```elisp
(use-package! seethru
  :if (display-graphic-p)
  :config
  (defun frame-alpha () (or (frame-parameter nil 'alpha) 100))
  (defun alphap () (< (frame-alpha) 100))

  (defun seethru-toggle ()
    (interactive)
    (if (alphap)
      (progn
        (seethru 100))
      (seethru 85)))

  (bind-key "s-u" 'seethru-toggle))
```


### 날씨/ {#날씨}


#### [wttrin]({{<relref "20210902070348-wttrin.md#" >}}) {#wttrin--20210902070348-wttrin-dot-md}

```elisp
(package! wttrin)
```

```elisp
(use-package! wttrin
  :config
  (setq wttrin-default-cities '("Seoul"))
  (setq wttrin-default-accept-language '("Accept-Language" . "en-US,en;q=0.8,ko-KR;q=0.7,zh-CN;q=0.6,zh;q=0.4"))
  ;; [[https://github.com/bcbcarl/emacs-wttrin/issues/16#issuecomment-626443963][bcbcarl/emacs-wttrin#16 Raw html instad of wttrin]]
  (defun wttrin-fetch-raw-string (query)
    "Get the weather information based on your QUERY."
    (let ((url-user-agent "curl")
          ;; (url-request-extra-headers '(("User-Agent" . "curl")))
          )
      (add-to-list 'url-request-extra-headers wttrin-default-accept-language)
      (with-current-buffer
          (url-retrieve-synchronously
           (concat "http://wttr.in/" query)
           (lambda (status) (switch-to-buffer (current-buffer))))
        (decode-coding-string (buffer-string) 'utf-8)))))
```


#### [forecast]({{<relref "20210902073449-forecast.md#" >}}) {#forecast--20210902073449-forecast-dot-md}

```elisp
(package! forecast)
```

```elisp
(use-package! forecast
  ;; :after (solar)
  ;; :custom
  ;; (forecast-api-key "")
  )
```


### [ox-leanpub]({{<relref "20211111214932-ox_leanpub.md#" >}}) {#ox-leanpub--20211111214932-ox-leanpub-dot-md}

```elisp
;; (use-package ox-gfm
;;   :after org)
(package! ox-leanpub)
```

```elisp
(use-package! ox-leanpub
  :after org
  :config
  (require 'ox-leanpub-markdown)
  (org-leanpub-book-setup-menu-markdown))
```


### [slack]({{<relref "20210929091638-slack.md#" >}}) {#slack--20210929091638-slack-dot-md}

```elisp
(package! slack
  ;; :recipe (:host github
  ;;          :repo "aculich/emacs-slack"
  ;;          :branch "cookie")
  )
```

```elisp
(use-package! slack
  :commands (slack-start)
  :init
  (setq slack-prefer-current-team t)
  :config
  (require 'auth-source)
  (slack-register-team
   :name "LF Corp/e-Biz"
   :token (auth-source-pick-first-password
           :host "lfebiz.slack.com"
           :user "ym.kim@lfcorp.com")
   :cookie (auth-source-pick-first-password
            :host "lfebiz.slack.com"
            :user "ym.kim@lfcorp.com_cookie")
   :default t
   :full-and-display-names t
   )
  )

(use-package alert
  :commands (alert)
  :init
  (setq alert-default-style 'notifier))
```


### [company-quickhelp-terminal]({{<relref "20210612185443-company_quickhelp_terminal.md#" >}}) {#company-quickhelp-terminal--20210612185443-company-quickhelp-terminal-dot-md}

```elisp
(package! company-quickhelp)
```

```elisp
(use-package! company-quickhelp
  :config
  (setq company-quickhelp-delay 0.1)
  ;; (setq company-quickhelp-use-propertized-text (display-graphic-p))
  (setq company-quickhelp-use-propertized-text t)
  (company-quickhelp-mode +1))
```

```elisp
(package! company-quickhelp-terminal)
```

```elisp
(use-package! company-quickhelp-terminal
  :config
  (with-eval-after-load 'company-quickhelp
    (company-quickhelp-terminal-mode +1)))
```


### [prettier]({{<relref "20210826153138-prettier.md#" >}}) {#prettier--20210826153138-prettier-dot-md}

```elisp
(package! prettier)
```

```elisp
(use-package! prettier
  :config
  (setq prettier-inline-errors-flag t)
  (global-prettier-mode -1))
```


### [zoom-window]({{<relref "20210319212501-zoom_window.md#" >}}) {#zoom-window--20210319212501-zoom-window-dot-md}

```elisp
(package! zoom-window)
```

```elisp
(use-package! zoom-window
  ;; :bind ("C-c z" . zoom-window-zoom)
  :config
  ;; window management (prefix "C-w")
  (map! :map evil-window-map "z" #'zoom-window-zoom))
```


### [multi-vterm]({{<relref "20210405043847-multi_vterm.md#" >}}) {#multi-vterm--20210405043847-multi-vterm-dot-md}

```elisp
(package! multi-vterm)
```

```elisp
(use-package! multi-vterm
  :after (vterm))
```


### [swagger-to-org]({{<relref "20211122081944-swagger_to_org.md#" >}}) {#swagger-to-org--20211122081944-swagger-to-org-dot-md}

```elisp
(package! swagger-to-org)
```

```elisp
(use-package! swagger-to-org)
```


### [flycheck-swagger-cli]({{<relref "20211122175646-flycheck_swagger_cli.md#" >}}) {#flycheck-swagger-cli--20211122175646-flycheck-swagger-cli-dot-md}

```elisp
(package! dash)
(package! flycheck-swagger-cli
  :recipe (:host github
            :repo "magoyette/flycheck-swagger-cli"))
```

```elisp
(use-package! flycheck-swagger-cli)
```


### [openapi-yaml-mode]({{<relref "20211122174325-openapi_yaml_mode.md#" >}}) {#openapi-yaml-mode--20211122174325-openapi-yaml-mode-dot-md}

```elisp
(package! openapi-yaml-mode
  :recipe (:host github
            :repo "magoyette/openapi-yaml-mode"))
```

```elisp
(use-package! openapi-yaml-mode
  :config
  (setq openapi-yaml-use-yaml-mode-syntax-highlight t))
```


### [swagger-mode]({{<relref "20211122180703-swagger_mode.md#" >}}) {#swagger-mode--20211122180703-swagger-mode-dot-md}

```elisp
(package! swagger-mode
  :recipe (:host github
            :repo "Nooby/swagger-mode"))
```

```elisp
;; 'generate -i /Users/lf/work/lf-ui/docs/openapi.yaml -g html2 -o /var/folders/kl/59pyj6f954n4gnjmlyx8_wfm0000gn/T/swaggerfV8eqU'
(use-package! swagger-mode
  :custom
  (swagger-codegen-cli "openapi-generator-cli")
  (swagger-command-template "generate -i %s -g %s -o %s")
  (swagger-compile-lang "html2")
  (swagger-preview-lang "html2"))
```


### [git-messenger]({{<relref "20211124140253-git_messenger.md#" >}}) {#git-messenger--20211124140253-git-messenger-dot-md}

```elisp
(package! git-messenger)
```

```elisp
(use-package! git-messenger
  :bind
  (:map vc-prefix-map
   ("p" . git-messenger:popup-message)
   :map git-messenger-map
   ("m" . git-messenger:copy-message))
  ;; :pretty-hydra
  ;; ((:title (pretty-hydra-title "Git Messenger" 'alltheicon "git")
  ;;   :color blue :quit-key "q")
  ;;  ("Actions"
  ;;   (("s" git-messenger:popup-show "Show")
  ;;    ;; ("S" git-messenger:popup-show-verbose "Show verbose")
  ;;    ("c" git-messenger:copy-commit-id "Copy hash")
  ;;    ;; ("d" git-messenger:popup-diff "Diff")
  ;;    ("m" git-messenger:copy-message "Copy message")
  ;;    ("," (catch 'git-messenger-loop (git-messenger:show-parent)) "Go Parent"))))
  :custom
  (git-messenger:show-detail t)
  (git-messenger:use-magit-popup t)
  )
```


### [git-msg-prefix]({{<relref "20210312141641-git_msg_prefix.md#" >}}) {#git-msg-prefix--20210312141641-git-msg-prefix-dot-md}

```elisp
(package! git-msg-prefix)
```

```elisp
(use-package! git-msg-prefix
  ;; :hook
  ;; (git-commit-mode . git-msg-prefix)
  :custom
  (git-msg-prefix-log-flags " --since='1 week ago' ")
  (git-msg-prefix-input-method 'helm-comp-read))
```


### [git-timemachine]({{<relref "20210629061235-git_timemachine.md#" >}}) {#git-timemachine--20210629061235-git-timemachine-dot-md}

```elisp
(use-package! git-timemachine
  :custom
  (git-timemachine-show-minibuffer-details t))
```


### [abridge-diff]({{<relref "20210623133924-abridge_diff.md#" >}}) {#abridge-diff--20210623133924-abridge-diff-dot-md}

```elisp
(package! abridge-diff)
```

```elisp

(use-package! abridge-diff
  :after magit ;; optional, if you'd like to use with magit
  :init (abridge-diff-mode 1))
```


### [projectile]({{<relref "20210313043332-projectile.md#" >}}) {#projectile--20210313043332-projectile-dot-md}

```elisp
(use-package! projectile
  :custom
  (projectile-auto-discover t)
  (projectile-compile-use-comint-mode t)
  (projectile-configure-use-comint-mode t)
  (projectile-dynamic-mode-line t)
  (projectile-find-dir-includes-top-level t)
  (projectile-install-use-comint-mode t)
  (projectile-package-use-comint-mode t)
  (projectile-per-project-compilation-buffer t)
  (projectile-require-project-root t)
  (projectile-run-use-comint-mode t)
  (projectile-test-use-comint-mode t)
  )
```


### [citre]({{<relref "20211128100522-citre.md#" >}}) {#citre--20211128100522-citre-dot-md}

```elisp
(package! citre)
```

```elisp
(use-package! citre
  :config
  (require 'citre-config))
```


### [symbol-overlay]({{<relref "20210705204846-symbol_overlay.md#" >}}) {#symbol-overlay--20210705204846-symbol-overlay-dot-md}

```elisp
(package! symbol-overlay)
```

```elisp
(use-package! symbol-overlay
  :custom
  (symbol-overlay-inhibit-map t)
  :bind
  ("M-i" . symbol-overlay-put)
  ("M-n" . symbol-overlay-switch-forward)
  ("M-p" . symbol-overlay-switch-backward)
  ("<f7>" . symbol-overlay-mode)
  ("<f8>" . symbol-overlay-remove-all)
  ;; :config
  ;; (define-key symbol-overlay-map (kbd "your-prefer-key") 'any-command)
  )
```


### [autorevert]({{<relref "20211130154648-autorevert.md#" >}}) {#autorevert--20211130154648-autorevert-dot-md}

```elisp
(use-package! autorevert
  :config
  (global-auto-revert-mode t))
```


### [emr]({{<relref "20211020155452-emr.md#" >}}) {#emr--20211020155452-emr-dot-md}

```elisp
(package! emr)
```

```elisp
(use-package! emr
  :config
  (define-key prog-mode-map (kbd "M-RET") 'emr-show-refactor-menu)
  (emr-initialize))
```


#### use-package {#use-package}

<a id="code-snippet--use-package::emr"></a>
```elisp
(use-package! emr
  :config
  (define-key prog-mode-map (kbd "M-RET") 'emr-show-refactor-menu)
  (emr-initialize))
```


### [page-break-lines]({{<relref "20210319061232-page_break_lines.md#" >}}) {#page-break-lines--20210319061232-page-break-lines-dot-md}

```elisp
(package! page-break-lines)
```

```elisp
(use-package! page-break-lines
  :hook
  (after-init . global-page-break-lines-mode))
```


### [expenses]({{<relref "20211212121600-expenses.md#" >}}) {#expenses--20211212121600-expenses-dot-md}

```elisp
(package! expenses)
```

```elisp
(use-package! expenses
  :custom
  (expenses-directory (expand-file-name "~/org/expenses/")))
```


### [blamer]({{<relref "20211212132346-blamer.md#" >}}) {#blamer--20211212132346-blamer-dot-md}

```elisp
(package! blamer)
```

```elisp
(use-package! blamer
  ;; :defer 20
  :custom
  (blamer-view
   ;; 'overlay
   'overlay-right
   )
  ;; (blamer-idle-time 0.5)
  ;; (blamer-min-offset 1)
  ;; (blamer-offset-per-symbol nil)
  :custom-face
  (blamer-face ((t :foreground "#7a88cf"
                   ;; :background nil
                   :height 140
                   :italic t)))
  :config
  (global-blamer-mode -1))
```


### [leetcode]({{<relref "20210922182803-leetcode.md#" >}}) {#leetcode--20210922182803-leetcode-dot-md}

```elisp
(package! leetcode
  ;; :recipe
  ;; (:local-repo "~/.doom.d/site-lisp/leetcode.el")
  )
;; (package! leetcode
;;   :recipe
;;   (:local-repo
;;    (lambda () (expand-file-name "site-lisp/leetcode.el" doom-private-dir))
;;    :files ("*.el" "src/lisp/*.el"))
;;   )
```

```elisp
(use-package! leetcode
  ;; :load-path (lambda () (expand-file-name "site-lisp/leetcode.el" doom-private-dir))
  :hook (leetcode--problem-description-mode . visual-line-mode)
  :config
  (setq leetcode-prefer-language
        ;; "c"
        ;; "cpp"
        ;; "csharp"
        ;; "golang"
        ;; "java"
        ;; "javascript"
        "typescript"
        ;; "kotlin"
        ;; "php"
        ;; "python"
        ;; "python3"
        ;; "ruby"
        ;; "rust"
        ;; "scala"
        ;; "swift"
        )
  (setq leetcode-prefer-sql "mysql")
  (setq leetcode-save-solutions t)
  (setq leetcode-directory "~/org/leetcode"))
```


### [cyberpunk-theme]({{<relref "20210617053729-cyberpunk_theme.md#" >}}) {#cyberpunk-theme--20210617053729-cyberpunk-theme-dot-md}

```elisp
(package! cyberpunk-theme)
```

```elisp
(use-package! cyberpunk-theme
  ;; :custom-face
  ;; (mode-line
  ;;  ((t (:background "black"))))
  )
```


### [highlight-parentheses]({{<relref "20210319211752-highlight_parentheses.md#" >}}) {#highlight-parentheses--20210319211752-highlight-parentheses-dot-md}

```elisp
(package! highlight-parentheses)
```

```elisp
(use-package! highlight-parentheses
  :custom
  (highlight-parentheses-colors `(nil))
  (highlight-parentheses-highlight-adjacent t)
  :custom-face
  (highlight-parentheses-highlight  ((t (:underline t :weight bold))))
  ;; :hook
  ;; (prog-mode . highlight-parentheses-mode)

  :config
  ;; (custom-set-faces '(highlight-parentheses-highlight
  ;;                     ((t (:underline t
  ;;                          :weight bold
  ;;                          :inverse-video nil
  ;;                          ))) t))

  (global-highlight-parentheses-mode +1))
```


### [one-themes]({{<relref "20210620154254-one_themes.md#" >}}) {#one-themes--20210620154254-one-themes-dot-md}

```elisp
(package! one-themes)
```

```elisp
(use-package! one-themes)
```


### [org-chef]({{<relref "20211219125938-org_chef.md#" >}}) {#org-chef--20211219125938-org-chef-dot-md}

```elisp
(package! org-chef)
```

```elisp
(use-package! org-chef)
```


### [ob-typescript]({{<relref "20211222051946-ob_typescript.md#" >}}) {#ob-typescript--20211222051946-ob-typescript-dot-md}

```elisp
(package! ob-typescript)
```

```elisp
(use-package! ob-typescript)
```


### [smudge]({{<relref "20211018200944-smudge.md#" >}}) {#smudge--20211018200944-smudge-dot-md}

```elisp
(package! smudge)
```

```elisp
(use-package! smudge
  :config
  ;; Settings
  (setq smudge-oauth2-client-id "")
  (setq smudge-oauth2-client-secret "")
  (define-key smudge-mode-map (kbd "C-c .") 'smudge-command-map)
  )
```


### [osx-plist]({{<relref "20210510122201-osx_plist.md#" >}}) {#osx-plist--20210510122201-osx-plist-dot-md}

```elisp
(package! osx-plist)
```

```elisp
(use-package! osx-plist
  :config
  ;; Allow editing of binary .plist files.
  (add-to-list 'jka-compr-compression-info-list
               [
                "\\.plist$"
                "converting text XML to binary plist"
                "plutil"
                ("-convert" "binary1" "-o" "-" "-")
                "converting binary plist to text XML"
                "plutil"
                ("-convert" "xml1" "-o" "-" "-")
                nil nil "bplist"])

  ;;It is necessary to perform an update!
  (jka-compr-update))
```


### [blox]({{<relref "20211017204729-blox.md#" >}}) {#blox--20211017204729-blox-dot-md}

```elisp
(package! blox)
```

```elisp
(use-package! blox
  :bind (:map lua-prefix-mode-map
         ("s" . blox-prompt-serve)
         ("b" . blox-prompt-build)
         ("t" . blox-test)))
```


### [indium]({{<relref "20210529024420-indium.md#" >}}) {#indium--20210529024420-indium-dot-md}

```elisp
(package! indium)
```

```elisp
(use-package! indium)
```


### [npm-mode]({{<relref "20211026131224-npm_mode.md#" >}}) {#npm-mode--20211026131224-npm-mode-dot-md}

```elisp
(use-package! npm-mode
  :config
  (npm-global-mode +1))
```


## scratch {#scratch}

```elisp
(menu-bar-mode -1)

;; (setenv "lc_all" "ko_kr.utf-8")
(display-time-mode +1)

;; (when (require 'org-id)
;;   (setq org-id-link-to-org-use-id 'create-if-interactive-and-no-custom-id))

(setq user-full-name "7696122"
      user-mail-address "7696122@gmail.com")

(setq doom-theme nil)
(setq display-line-numbers-type nil)
(remove-hook 'org-mode-hook #'org-superstar-mode)
```


## [ucs-normalize, 한글이 깨지는 문제 수정]({{<relref "20210929185141-ucs_normalize.md#" >}}) {#ucs-normalize-한글이-깨지는-문제-수정--20210929185141-ucs-normalize-dot-md}

```elisp
(require 'ucs-normalize)
(let ((_decoding (if (eq system-type 'darwin) 'utf-8-hfs 'utf-8))
       (_encoding 'utf-8-unix))
  (setq default-process-coding-system (cons _decoding _encoding))
  (add-hook! 'vterm-mode-hook #'(lambda () (set-buffer-process-coding-system 'utf-8-hfs-unix 'utf-8-unix)))
  (progn ;; https://stackoverflow.com/questions/6820051/unicode-characters-in-emacs-term-mode
    (defadvice ansi-term (after advise-ansi-term-coding-system)
      (set-buffer-process-coding-system 'utf-8-hfs 'utf-8-unix))
    (ad-activate 'ansi-term))

  (setq default-file-name-coding-system 'utf-8-hfs)
  ;; (setq default-terminal-coding-system _decoding)
  (set-file-name-coding-system 'utf-8-hfs)
  ;; (set-terminal-coding-system  _decoding)
  )

;; (leaf *mac-encoding
;;       :if (eq system-type 'darwin)
;;       (leaf ucs-normalize
;;             :require t
;;             :defvar (mac-pass-control-to-system ns-command-modifier ns-alternate-modifier)
;;             :config
;;             (set-file-name-coding-system 'utf-8-hfs)
;;             (setq locale-coding-system 'utf-8-hfs)
;;             (setq mac-pass-control-to-system t  ;; Ctrl を Mac から奪い取る
;;                   ns-command-modifier 'meta     ;; Cmd と Option を逆にする
;;                   ns-alternate-modifier 'super)
;;             (global-set-key [ns-drag-file] 'ns-find-file)
;;             )
;;       )
```


## 한글 {#한글}

```elisp
;; (set-language-environment "UTF-8")
(setenv "LANG"
        ;; "en_US.UTF-8"
        "ko_KR.UTF-8"
        )
(set-input-method 'korean-hangul)
```


## c-h를 backspace로 사용하기 {#c-h를-backspace로-사용하기}

```elisp
(normal-erase-is-backspace-mode -1)
(define-key key-translation-map [?\C-h] [?\C-?])
(global-set-key [(control ?h)] 'delete-backward-char)
;; (when (display-graphic-p)
;;   (define-key key-translation-map [?\C-h] [?\C-?])
;;   ;; (global-set-key [(control ?h)] 'delete-backward-char)
;;   )
```


## [which-key]({{<relref "20210321195628-which_key.md#" >}}) 설정 {#which-key--20210321195628-which-key-dot-md--설정}

```elisp
(setq which-key-enable-extended-define-key t)
;; (setq max-mini-window-height 0.95)

;; Allow C-h to trigger which-key before it is done automatically
(setq which-key-show-early-on-C-h t)
;; make sure which-key doesn't show normally but refreshes quickly after it is
;; triggered.
(setq which-key-idle-delay 0.5)
(setq which-key-idle-secondary-delay 0.05)
(setq which-key-popup-type 'side-window)
;; location of which-key window. valid values: top, bottom, left, right,
;; or a list of any of the two. If it's a list, which-key will always try
;; the first location first. It will go to the second location if there is
;; not enough room to display any keys in the first location
(setq which-key-side-window-location 'bottom)

;; max width of which-key window, when displayed at left or right.
;; valid values: number of columns (integer), or percentage out of current
;; frame's width (float larger than 0 and smaller than 1)
(setq which-key-side-window-max-width 0.33)

;; max height of which-key window, when displayed at top or bottom.
;; valid values: number of lines (integer), or percentage out of current
;; frame's height (float larger than 0 and smaller than 1)
(setq which-key-side-window-max-height 0.90)
```


## [fringe]({{<relref "20210507071553-fringe.md#" >}}) 설정 {#fringe--20210507071553-fringe-dot-md--설정}

```elisp
(when (display-graphic-p)
  (defun setup-fringe()
    (interactive)
    ;; (set-window-fringes nil fringe-mode fringe-mode)
    (fringe-mode '(14 . 8)))

  (add-hook! 'after-init-hook 'setup-fringe)
  (add-hook! 'doom-switch-buffer-hook #'setup-fringe))

;; (use-package! fringe
;;   :custom-face
;;   (fringe ((t (:inherit modeline))))
;;   :config
;;   (set-window-fringes nil fringe-mode fringe-mode)
;;   (fringe-mode '(14 . 4)))
```


## [font]({{<relref "20211123144500-font.md#" >}}) 설정 {#font--20211123144500-font-dot-md--설정}

```elisp
(setq doom-font-increment 1)
(setq doom-font (font-spec
                 :family "D2Coding"
                 :height 240
                 :foundry nil
                 :slant 'normal
                 :weight 'medium
                 :width 'normal
                 ))
```


## [theme]({{<relref "20211123144651-theme.md#" >}}) 설정 {#theme--20211123144651-theme-dot-md--설정}

```elisp
(defun setup-theme ()
  "테마를 로딩하고 백그라운드 픽스"
  (interactive)
  (setq doom-theme
        (cond
         ((eq frame-background-mode 'light)
          (if window-system 'doom-one-light 'tsdh-light))
         ((eq frame-background-mode 'dark)
          (if window-system 'doom-one 'tsdh-dark))
         (frame-background-mode nil)))
  ;; (load-theme doom-theme t)
  ;; (doom/reload-theme)
  )

(defun _update ()
  (interactive)

  (when (fboundp 'mini-modeline-mode)
    (after! mini-modeline
      (_update-mini-modeline)))

  ;; (doom/reload-theme)
  (let* ((transparency nil)
         (bg (if (and transparency
                      (not window-system)
                      (eq system-type 'darwin))
                 "ARGBBB000000"
               (cond ((eq frame-background-mode 'light)
                      (if (display-graphic-p) "white"
                        "brightwhite"
                        ;; "color-255"
                        ))
                     ((eq frame-background-mode 'dark)
                      "black")))))
    (set-background-color bg)
    (set-face-attribute 'default nil :background bg)
    ))

(defun _light ()
  (interactive)
  (setq frame-background-mode 'light)
  (if (display-graphic-p) "white" "brightwhite")
  (mapc 'frame-set-background-mode (frame-list))
  (setup-theme)
  (_update))

(defun _dark()
  (interactive)
  (setq frame-background-mode 'dark)
  (mapc 'frame-set-background-mode (frame-list))
  (setup-theme)
  (_update))
;; (_dark)
(_light)
```


## [mouse]({{<relref "20210422144350-mouse.md#" >}}) {#mouse--20210422144350-mouse-dot-md}

```elisp
;; <<use-package::mouse>>
(use-package! mouse
  :unless window-system
  :bind
  ("<mouse-4>" . scroll-down-line)
  ("<mouse-5>" . scroll-up-line)
  :config
  ;; (setq mouse-wheel-tilt-scroll t)
  (xterm-mouse-mode 1)
  (setq track-mouse nil)
  ;; (defun track-mouse (e))
  (setq mouse-sel-mode t)
  )
```


### use-package {#use-package}

<a id="code-snippet--use-package::mouse"></a>
```elisp
(use-package! mouse
  :unless window-system
  :bind
  ("<mouse-4>" . scroll-down-line)
  ("<mouse-5>" . scroll-up-line)
  :config
  ;; (setq mouse-wheel-tilt-scroll t)
  (xterm-mouse-mode 1)
  (setq track-mouse nil)
  ;; (defun track-mouse (e))
  (setq mouse-sel-mode t)
  )
```


## Relate {#relate}

[org-timeline]({{<relref "20211007212715-org_timeline.md#" >}})  
[yasnippet-snippets]({{<relref "20210704083041-yasnippet_snippets.md#" >}})  
[ox-pandoc]({{<relref "20211220053759-ox_pandoc.md#" >}})  
[js-react-redux-yasnippets]({{<relref "20210410182319-js_react_redux_yasnippets.md#" >}})  
[matrix-client]({{<relref "20211029164117-matrix_client.md#" >}})  
[mmm-mode]({{<relref "20210315112507-mmm_mode.md#" >}})  
[css-in-js]({{<relref "20210327103545-css_in_js.md#" >}})  
[org-tanglesync]({{<relref "20210318163828-org_tanglesync.md#" >}})  
[dap-mode]({{<relref "20210426115824-dap_mode.md#" >}})  
[feature-mode]({{<relref "20210311114005-feature_mode.md#" >}})  
[eyebrowse]({{<relref "20210706035412-eyebrowse.md#" >}})  


## Local Variables {#local-variables}
