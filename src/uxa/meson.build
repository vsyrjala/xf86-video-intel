uxa_sources = [
  'intel_batchbuffer.c',
  'intel_display.c',
  'intel_driver.c',
  'intel_memory.c',
  'intel_uxa.c',
  'intel_video.c',
  'intel_video_overlay.c',
  'intel_uxa_video.c',
  'i830_3d.c',
  'i830_render.c',
  'i915_3d.c',
  'i915_render.c',
  'i915_video.c',
  'i965_3d.c',
  'i965_video.c',
  'i965_render.c',
  'uxa.c',
  'uxa-accel.c',
  'uxa-glyphs.c',
  'uxa-render.c',
  'uxa-unaccel.c',
]

uxa_deps = [ xorg, libudev, libdrm_intel, libdrm, ]

if with_dri2
  uxa_sources += 'intel_dri.c'
  uxa_deps += dri2
endif

if with_dri3
  uxa_sources += [
    'intel_dri3.c',
    'intel_sync.c',
  ]
endif

if has_present
  uxa_sources += 'intel_present.c'
endif

if with_xvmc
  uxa_sources += 'intel_hwmc.c'
endif

uxa = static_library('uxa',
		     sources : uxa_sources,
		     dependencies : uxa_deps,
		     include_directories : inc,
		     c_args : [
		       '-Wno-unused-parameter',
		       '-Wno-sign-compare',
		     ],
		     install : false)
