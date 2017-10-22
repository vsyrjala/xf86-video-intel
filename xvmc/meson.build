xvmc_sources = [
  'intel_xvmc.c',
  'intel_xvmc_dump.c',
  'i915_xvmc.c',
  'i965_xvmc.c',
  'xvmc_vld.c',
  'intel_batchbuffer.c',
]

xcb = dependency('x11-xcb', required : true)
xcb_aux = dependency('xcb-aux', required : true)
xcb_dri2 = dependency('xcb-dri2', required : true)
xvmc = dependency('xvmc', required : true)

shared_library('IntelXvMC',
	       soversion : '1',
	       version : '1.0.0',
	       sources : xvmc_sources,
	       dependencies : [ pthreads, x11, xvmc, xorg, xcb, xcb_aux, xcb_dri2, libdrm_intel, ],
	       c_args : [
		 '-DFALSE=0', '-DTRUE=1',
		 '-Wno-unused-parameter',
		 '-Wno-sign-compare',
	       ],
	       install : true)
