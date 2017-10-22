i810_sources = [
  'i810_accel.c',
  'i810_cursor.c',
  'i810_driver.c',
  'i810_memory.c',
  'i810_video.c',
  'i810_wmark.c',
]

i810_deps = [ xorg, libdrm, pciaccess, ]

if has_xaa
  i810_sources += 'i810_xaa.c'
endif

if has_dga
  i810_sources += 'i810_dga.c'
endif

if with_dri1
  i810_sources += 'i810_dri.c'
  i810_deps += dri1

  if with_xvmc
    i810_sources += 'i810_hwmc.c'
  endif
endif

i810 = static_library('legacy_i810',
		      sources : i810_sources,
		      dependencies : i810_deps,
		      include_directories : inc,
		      c_args : [
			'-Wno-unused-parameter',
			'-Wno-sign-compare',
		      ],
		      install : false)

if with_xvmc
  subdir('xvmc')
endif
