if with_tools
  x11 = dependency('x11', required : true)
  xext = dependency('xext', required : true)
  xfixes = dependency('xfixes', required : true)
  xrender = dependency('xrender', required : true)
  xdamage = dependency('xdamage', required : true)
  xrandr = dependency('xrandr', required : true)
  xrender = dependency('xrender', required : true)
  xcursor = dependency('xcursor', required : true)
  xscrnsaver = dependency('xscrnsaver', required : true)
  xtst = dependency('xtst', required : true)

  executable('intel-virtual-output',
	     sources : 'virtual.c',
	     dependencies : [
	       x11,
	       xext,
	       xfixes,
	       xdamage,
	       xrandr,
	       xrender,
	       xcursor,
	       xscrnsaver,
	       xtst,
	       pixman,
	     ],
	     c_args : [
	       '-Wno-unused-parameter',
	       '-Wno-sign-compare',
	     ],
	     install : true)

  executable('cursor',
	     sources : 'cursor.c',
	     dependencies : [ x11, xfixes, png, ],
	     install : false)
endif

if with_tools and with_dri3
  xcb = dependency('x11-xcb', required : true)
  xcb_dri3 = dependency('xcb-dri3', required : true)
  xf86vm = dependency('xxf86vm', required : true)

  executable('dri3info',
	     sources : 'dri3info.c',
	     dependencies : [
	       xcb,
	       xcb_dri3,
	       x11,
	       xrandr,
	       xf86vm,
	       dri3,
	       dri,
	       libdrm,
	     ],
	     install : false)
endif

if with_backlight_helper
  executable('xf86-video-intel-backlight-helper',
	     sources : 'backlight_helper.c',
	     install_dir : get_option('libexecdir'),
	     install_mode : [ 'rws--x--x', 'root', 'root' ],
	     install : true)
endif
