##Instructions to enable systemtap on PHP-5.3.3

#####Step 1: Download PHP and checkout `php-5.3.3`

	$git clone git://github.com/php/php-src php-src
	$cd php-src/
	$git checkout -b php-5.3.3 php-5.3.3
	
#####Step 2: Apply the patch

	$patch -p1 < ../backport-patch-for-php-5.3.3-with-dtrace-enabled.patch
	
#####Step 3: Build `configure`, and compile with `--enable-dtrace` flag

	$./buildconf --force
	$./configure --enable-dtrace
	$make -j4
	
#####Step 4: Check whether dtrace has really been enabled in the compiled shared object

######Quick check
	$strings sapi/cli/php | grep "dtrace_execute"
	zend_dtrace_execute
	zend_dtrace_execute_internal

######Real check with systemtap 2.4 for the existence of probes in binary.

The following command shows which probes and corresponding parameters are available.

	$stap -L 'process("sapi/cli/php").provider("php").mark("*")'
	process("sapi/cli/php").provider("php").mark("compile__file__entry") $arg1:long $arg2:long
	process("sapi/cli/php").provider("php").mark("compile__file__return") $arg1:long $arg2:long
	process("sapi/cli/php").provider("php").mark("error") $arg1:long $arg2:long $arg3:long
	process("sapi/cli/php").provider("php").mark("exception__caught") $arg1:long
	process("sapi/cli/php").provider("php").mark("exception__thrown") $arg1:long
	process("sapi/cli/php").provider("php").mark("execute__entry") $arg1:long $arg2:long
	process("sapi/cli/php").provider("php").mark("execute__return") $arg1:long $arg2:long
	process("sapi/cli/php").provider("php").mark("function__entry") $arg1:long $arg2:long $arg3:long $arg4:long $arg5:long
	process("sapi/cli/php").provider("php").mark("function__return") $arg1:long $arg2:long $arg3:long $arg4:long $arg5:long
	process("sapi/cli/php").provider("php").mark("request__shutdown") $arg1:long $arg2:long $arg3:long
	process("sapi/cli/php").provider("php").mark("request__startup") $arg1:long $arg2:long $arg3:long