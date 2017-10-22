fb_deps = [
  xorg,
  pixman,
]

fb_sources = [
  'fbarc.c',
  'fbbitmap.c',
  'fbblt.c',
  'fbbltone.c',
  'fbclip.c',
  'fbcopy.c',
  'fbfill.c',
  'fbgc.c',
  'fbglyph.c',
  'fbimage.c',
  'fbline.c',
  'fbpict.c',
  'fbpoint.c',
  'fbpush.c',
  'fbseg.c',
  'fbspan.c',
  'fbstipple.c',
  'fbtile.c',
  'fbutil.c',
]

fb = static_library('fb',
		    sources : fb_sources,
		    dependencies : fb_deps,
		    c_args : [
		      '-Wno-unused-parameter',
		      '-Wno-sign-compare',
		    ],
		    install : false)
