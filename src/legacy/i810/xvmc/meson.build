xvmc = dependency('xvmc', required : true)

shared_library('I810XvMC',
	       soversion : '1',
	       version : '1.0.0',
	       sources : 'I810XvMC.c',
	       dependencies : [ x11, xvmc, xorg, libdrm, ],
	       c_args : [
		 '-Wno-unused-parameter',
	       ],
	       install : true)
