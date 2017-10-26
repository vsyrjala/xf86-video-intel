project('xf86-video-intel', 'c',
	version : '2.99.917',
	default_options: [
	  'warning_level=2',
	  'c_std=gnu99',
	],
	license : 'MIT',
	meson_version : '>0.40.0')

config = configuration_data()

version = meson.project_version().split('.')
config.set('PACKAGE_VERSION_MAJOR', version[0])
config.set('PACKAGE_VERSION_MINOR', version[1])
config.set('PACKAGE_VERSION_PATCHLEVEL', version[2])

config.set_quoted('PREFIX_PATH', get_option('prefix'))
config.set_quoted('LIBEXEC_PATH', join_paths(get_option('prefix'),
					     get_option('libexecdir')))

xorg_moduledir = get_option('xorg-module-dir')
moduledir = ''
foreach dir : xorg_moduledir.split('/')
  if dir == '@libdir@'
    dir = get_option('libdir')
  endif
  moduledir = join_paths(moduledir, dir)
endforeach

cc = meson.get_compiler('c')

xorg = dependency('xorg-server', version : '>= 1.6', required : true)
pthreads = dependency('threads', required : true)
pciaccess = dependency('pciaccess', version : '>= 0.10', required : true)

x11 = dependency('x11', required : false)
xfixes = dependency('xfixes', required : false)
png = dependency('libpng', required : false)

math = cc.find_library('m', required : true)

clock = cc.find_library('rt', required : true)
if not cc.has_function('clock_gettime', args : '-lrt')
  error('clock_gettime() missing')
endif

if cc.has_function('getline')
  config.set('HAVE_GETLINE', 1)
endif

dependency('xproto', required : true)
dependency('fontsproto', required : true)
dependency('damageproto', required : true)

if cc.has_header_symbol('xorg-server.h', 'RANDR',
			dependencies : xorg)
  dependency('randrproto', required : true)
endif
if cc.has_header_symbol('xorg-server.h', 'RENDER',
			dependencies : xorg)
  dependency('renderproto', required : true)
endif
if cc.has_header_symbol('xorg-server.h', 'DPMSExtension',
			dependencies : xorg)
  dependency('xextproto', required : true)
endif

with_tools = get_option('tools')

if get_option('backlight')
  config.set('USE_BACKLIGHT', 1)
endif
with_backlight_helper = get_option('backlight-helper')
if with_backlight_helper
  config.set('USE_BACKLIGHT_HELPER', 1)
endif

config.set('USE_GIT_DESCRIBE', 1)
config.set('BUILDER_DESCRIPTION', 1)

atomic_primitives = 'none'

atomic_primitives_code = '''
int atomic_add(int i) {
    return __sync_fetch_and_add (&i, 1);
}
int atomic_cmpxchg(int i, int j, int k) {
    return __sync_val_compare_and_swap (&i, j, k);
}
int main(void) {
    return 0;
}'''
if cc.links(atomic_primitives_code, name : 'atomic primitives')
  atomic_primitives = 'intel'
  config.set('HAVE_ATOMIC_PRIMITIVES', 1)
endif

if atomic_primitives == 'none' and cc.has_header('atomic_ops.h')
  atomic_primitives = 'libatomic-ops'
  config.set('HAVE_LIB_ATOMIC_OPS', 1)
endif

if atomic_primitives == 'none'
  error('xf86-video-intel depends upon atomic operations, which were not found for your compiler/cpu. Try compiling with -march=native, or install the libatomics-op-dev package.')
endif

libudev = dependency('libudev', required : false)
if libudev.found()
  config.set('HAVE_UDEV', 1)
endif

cpuid_code = '''
#include <cpuid.h>
#include <stddef.h>
int main(void) {
    int eax, ebx, ecx, edx;
    if (__get_cpuid_max(0, NULL) < 4)
       return 0;
    __cpuid_count(4, 0, eax, ebx, ecx, edx);
    return 0;
}'''
if cc.links(cpuid_code, name : '__cpuid()')
  config.set('HAVE_CPUID_H', 1)
endif

has_shm = (cc.has_header('sys/ipc.h') and
	   cc.has_header('X11/extensions/XShm.h') and
	   cc.has_header('X11/extensions/shmproto.h') and
	   cc.has_header('X11/extensions/shmstr.h'))
if has_shm
  config.set('HAVE_MIT_SHM', 1)
  config.set('HAVE_X11_EXTENSIONS_SHMPROTO_H', 1)
  config.set('HAVE_X11_EXTENSIONS_SHMSTR_H', 1)
endif

shdt_code = '''
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/shm.h>
int main(void){
    char *shmaddr;
    int id = shmget (IPC_PRIVATE, 4, IPC_CREAT | 0600);
    if (id == -1) return 2;
    shmaddr = shmat (id, 0, 0);
    shmctl (id, IPC_RMID, 0);
    if ((char*) shmat (id, 0, 0) == (char*) -1) {
       shmdt (shmaddr);
       return 1;
    }
    shmdt (shmaddr);
    shmdt (shmaddr);
    return 0;
}'''
if has_shm and cc.links(shdt_code, name : 'Deferred shm release')
  config.set('IPC_RMID_DEFERRED_RELEASE', 1)
endif

present = dependency('presentproto', required : false)
has_present = (present.found() and
	       cc.has_header('present.h', dependencies : xorg))
if has_present
  config.set('HAVE_PRESENT', 1)
endif

libdrm = dependency('libdrm', version : '>= 2.4.20', required : true)
pixman = dependency('pixman-1', version : '>= 0.16.0', required : true)

if pixman.version() >= '0.24.0'
  config.set('HAS_PIXMAN_TRIANGLES', 1)
endif
if pixman.version() >= '0.27.1'
  config.set('HAS_PIXMAN_GLYPHS', 1)
endif

default_accel = get_option('default-accel')

with_sna = get_option('sna')
if with_sna
  message('Checking SNA support')
  config.set('USE_SNA', 1)

  has_totalram = cc.has_member('struct sysinfo', 'totalram',
			       prefix : '#include <sys/sysinfo.h>')
  if has_totalram
    config.set('HAVE_STRUCT_SYSINFO_TOTALRAM', 1)
  endif
elif default_accel == 'sna'
  error('SNA not available, so can\'t selected as the default acceleration method')
endif

with_uxa = get_option('uxa')
if with_uxa
  message('Checking UXA support')
  libdrm_intel = dependency('libdrm_intel', version : '>= 2.4.52',
			    required : true)
  uxa_pixman = dependency('pixman-1', version : '>= 0.24.0',
			  required : true)

  config.set('USE_UXA', 1)
elif default_accel == 'uxa'
  error('UXA not available, so can\'t selected as the default acceleration method')
endif

if default_accel == 'sna'
  config.set('DEFAULT_ACCEL_METHOD', 'SNA')
elif default_accel == 'uxa'
  config.set('DEFAULT_ACCEL_METHOD', 'UXA')
else
  config.set('DEFAULT_ACCEL_METHOD', 'NOACCEL')
endif

with_kms = get_option('kms')
if with_kms
  message('Checking KMS support')
  config.set('KMS', 1)
endif

with_ums = get_option('ums')
if with_ums
  message('Checking UMS support')
  has_ums = cc.has_header('vgaHW.h',
			  dependencies : xorg)

  # Currently 'required' doesn't work for cc.has_header() & co.
  if not has_ums
    error('UMS dependencies not met')
  endif

  config.set('UMS', 1)

  has_xaa = cc.has_header('xaa.h', dependencies : xorg)
  if has_xaa
    config.set('XAA', 1)
  endif

  has_dga = cc.has_header('dgaproc.h', dependencies : xorg)
  if has_xaa
    config.set('DGA', 1)
  endif
endif

with_dri1 = get_option('dri1')
if with_dri1
  message('Checking DRI1 support')
  dri1 = dependency('xf86driproto', required : true)
  has_dri1 = (cc.has_header('dri.h', dependencies : xorg) and
	      cc.has_header('sarea.h', dependencies : xorg) and
	      cc.has_header('dristruct.h', dependencies : xorg))

  # Currently 'required' doesn't work for cc.has_header() & co.
  if not has_dri1
    error('DRI1 dependencies not met')
  endif

  config.set('HAVE_DRI1', 1)
endif

with_dri2 = get_option('dri2')
if with_dri2
  message('Checking DRI2 support')
  dri2 = dependency('dri2proto', version : '>= 2.6', required : true)

  dri = dependency('dri', required : false)
  if dri.found()
    dridriverdir = dri.get_pkgconfig_variable('dridriverdir')
  else
    dridriverdir = join_paths(get_option('libdir'), 'dri')
  endif

  config.set('HAVE_DRI2', 1)
  config.set_quoted('DRI_DRIVER_PATH', dridriverdir)
endif

with_dri3 = get_option('dri3')
if with_dri3
  message('Checking DRI3 support')
  dri3 = dependency('dri3proto', required : true)
  has_dri3 = (cc.has_header_symbol('xorg-server.h', 'DRI3',
				   dependencies : xorg) and
	      cc.has_header('misyncstr.h',
			    dependencies : xorg) and
	      cc.has_header('misyncshm.h',
			    dependencies : xorg))

  # Currently 'required' doesn't work for cc.has_header() & co.
  if not has_dri3
    error('DRI3 dependencies not met')
  endif

  config.set('HAVE_DRI3', 1)
endif

default_dri = get_option('default-dri')
config.set('DEFAULT_DRI_LEVEL', default_dri)

with_xvmc = get_option('xvmc')
if with_xvmc
  dependency('xvmc', required : true)
  dependency('dri2proto', required : true)
  dependency('x11', required : true)
  dependency('x11-xcb', required : true)
  dependency('xcb-dri2', required : true)
  dependency('xcb-aux', required : true)
  dependency('libdrm_intel', required : true)

  config.set('ENABLE_XVMC', 1)
endif

if get_option('tearfree')
  config.set('TEARFREE', 1)
endif
if get_option('use-create2')
  config.set('USE_CREATE2', 1)
endif
if get_option('async-swap')
  config.set('USE_ASYNC_SWAP', 1)
endif

with_valgrind = get_option('valgrind')
if with_valgrind
  message('Checking Valgrind support')
  valgrind = dependency('valgrind', required : true)
  config.set('HAVE_VALGRIND', 1)
endif

debug = get_option('debug')
if debug == 'sync'
  config.set('DEBUG_SYNC', 1)
endif
if debug == 'memory' or debug == 'full'
  config.set('DEBUG_MEMORY', 1)
endif
if debug == 'pixmap' or debug == 'full'
  config.set('DEBUG_PIXMAP', 1)
endif
if debug == 'full'
  config.set('HAS_DEBUG_FULL', 1)
endif

inc = include_directories([ '.', 'src', 'xvmc', 'src/render_program', ])

add_project_arguments('-include', 'config.h', language : 'c')

man_config = configuration_data()
man_config.set('appmansuffix', '1')
man_config.set('filemansuffix', '5')
man_config.set('drivermansuffix', '4')
man_config.set('miscmansuffix', '7')
man_config.set('xservername', 'Xorg')
man_config.set('xconfigfile', 'xorg.conf')
man_config.set('vendorversion', '"@0@ @1@" "@2@"'.format(meson.project_name(),
							 meson.project_version(),
							 'X Version 11'))

subdir('src')
subdir('tools')

if with_xvmc
  subdir('xvmc')
endif

subdir('man')

configure_file(output: 'config.h', install: false, configuration: config)
