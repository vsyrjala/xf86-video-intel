intel_drv_deps = [ pciaccess, xorg, ]
intel_drv_libs = []

if with_ums
  subdir('legacy/i810')
  intel_drv_libs += i810
endif

if with_sna
  subdir('sna')
  intel_drv_libs += sna
endif

if with_uxa
  subdir('uxa')
  intel_drv_libs += uxa
endif

if with_valgrind
  intel_drv_deps += valgrind
endif

intel_drv_sources = [
  'backlight.c',
  'fd.c',
  'intel_device.c',
  'intel_options.c',
  'intel_module.c',
]

shared_module('intel_drv',
	      sources : intel_drv_sources,
	      dependencies : intel_drv_deps,
	      link_with : intel_drv_libs,
	      c_args : [
		'-Wno-unused-parameter',
		'-Wno-sign-compare',
		'-Wno-missing-field-initializers',
	      ],
	      name_prefix : '',
	      install_dir : join_paths(moduledir, 'drivers'),
	      install : true)
