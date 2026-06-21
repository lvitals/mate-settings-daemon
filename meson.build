project('mate-settings-daemon', 'c',
  version: '1.29.0',
  meson_version: '>= 0.56.0',
  default_options: [
    'warning_level=1',
    'c_std=gnu99',
  ]
)

gnome = import('gnome')
i18n = import('i18n')

cc = meson.get_compiler('c')

# Global compiler flags
add_project_arguments('-DHAVE_CONFIG_H', language: 'c')

# Standard dependencies
glib_req = '>= 2.58.0'
gio_req = '>= 2.50.0'
gtk_req = '>= 3.22.0'
mate_desktop_req = '>= 1.28.0'
libmatekbd_req = '>= 1.17.0'
libnotify_req = '>= 0.7.0'
libatspi_req = '>= 2.0'
libmatemixer_req = '>= 1.10.0'

glib_dep = dependency('glib-2.0', version: glib_req)
gio_dep = dependency('gio-2.0', version: gio_req)
gio_unix_dep = dependency('gio-unix-2.0')
gtk_dep = dependency('gtk+-3.0', version: gtk_req)
gmodule_dep = dependency('gmodule-2.0')
gthread_dep = dependency('gthread-2.0')
dconf_dep = dependency('dconf', version: '>= 0.13.4')
mate_desktop_dep = dependency('mate-desktop-2.0', version: mate_desktop_req)

x11_dep = dependency('x11')
xinput_dep = dependency('xi')
fontconfig_dep = dependency('fontconfig')

libmatekbd_dep = dependency('libmatekbd', version: libmatekbd_req)
libmatekbdui_dep = dependency('libmatekbdui', version: libmatekbd_req)
libxklavier_dep = dependency('libxklavier', version: '>= 5.2')

# libnotify (optional)
libnotify_opt = get_option('libnotify')
have_libnotify = false
libnotify_dep = []
if not libnotify_opt.disabled()
  libnotify_dep = dependency('libnotify', version: libnotify_req, required: libnotify_opt.enabled())
  if libnotify_dep.found()
    have_libnotify = true
  endif
endif

# libatspi (optional)
libatspi_opt = get_option('libatspi')
have_libatspi = false
libatspi_dep = []
if not libatspi_opt.disabled()
  libatspi_dep = dependency('atspi-2', version: libatspi_req, required: libatspi_opt.enabled())
  if libatspi_dep.found()
    have_libatspi = true
  endif
endif

# pulse (optional)
pulse_opt = get_option('pulse')
have_pulse = false
pulse_dep = []
if not pulse_opt.disabled()
  pulse_dep = dependency('libpulse', version: '>= 0.9.15', required: pulse_opt.enabled())
  pulse_mainloop_dep = dependency('libpulse-mainloop-glib', required: pulse_opt.enabled())
  if pulse_dep.found() and pulse_mainloop_dep.found()
    have_pulse = true
    pulse_dep = [pulse_dep, pulse_mainloop_dep]
  endif
endif

# canberra (optional)
canberra_opt = get_option('canberra')
have_canberra = false
canberra_dep = []
if not canberra_opt.disabled()
  canberra_dep = dependency('libcanberra-gtk3', required: canberra_opt.enabled())
  if canberra_dep.found()
    have_canberra = true
  endif
endif

# smartcard / NSS (optional)
smartcard_opt = get_option('smartcard')
have_smartcard = false
nss_dep = []
if not smartcard_opt.disabled()
  nss_dep = dependency('nss', version: '>= 3.11.7', required: smartcard_opt.enabled())
  if nss_dep.found()
    have_smartcard = true
  endif
endif

# rfkill (optional)
rfkill_opt = get_option('rfkill')
have_rfkill = not rfkill_opt.disabled()

# libmatemixer (optional/auto)
libmatemixer_dep = dependency('libmatemixer', version: libmatemixer_req, required: false)
have_libmatemixer = libmatemixer_dep.found()

# Polkit (optional/auto)
polkit_dep = dependency('polkit-gobject-1', version: '>= 0.97', required: false)
have_polkit = polkit_dep.found()

# Math library
libm = cc.find_library('m', required: false)

# Headers and functions
conf = configuration_data()
conf.set_quoted('PACKAGE', meson.project_name())
conf.set_quoted('VERSION', meson.project_version())
conf.set_quoted('PACKAGE_NAME', meson.project_name())
conf.set_quoted('PACKAGE_VERSION', meson.project_version())
conf.set_quoted('PACKAGE_BUGREPORT', 'https://mate-desktop.org/')
conf.set_quoted('PACKAGE_STRING', '@0@ @1@'.format(meson.project_name(), meson.project_version()))
conf.set_quoted('GETTEXT_PACKAGE', meson.project_name())

if have_libnotify
  conf.set('HAVE_LIBNOTIFY', 1)
endif
if have_libatspi
  conf.set('HAVE_LIBATSPI', 1)
endif
if have_pulse
  conf.set('HAVE_PULSE', 1)
endif
if have_canberra
  conf.set('HAVE_LIBCANBERRA', 1)
endif
if have_smartcard
  conf.set('HAVE_SMARTCARD_SUPPORT', 1)
endif
if have_libmatemixer
  conf.set('HAVE_LIBMATEMIXER', 1)
endif
if cc.has_function('mate_rr_output_get_hotplug_mode_update', dependencies: mate_desktop_dep)
  conf.set('HAVE_MATE_RR_OUTPUT_GET_HOTPLUG_MODE_UPDATE', 1)
endif


# Configuration directories
prefix = get_option('prefix')
sysconfdir = join_paths(prefix, get_option('sysconfdir'))
datadir = join_paths(prefix, get_option('datadir'))
libdir = join_paths(prefix, get_option('libdir'))
libexecdir = join_paths(prefix, get_option('libexecdir'))
plugindir = join_paths(libdir, 'mate-settings-daemon')

add_project_arguments([
  '-DDATADIR="@0@"'.format(datadir),
  '-DSYSCONFDIR="@0@"'.format(sysconfdir),
  '-DGTKBUILDERDIR="@0@/mate-settings-daemon"'.format(datadir),
  '-DMATE_SETTINGS_LOCALEDIR="@0@"'.format(join_paths(datadir, 'locale')),
  '-DLIBEXECDIR="@0@"'.format(libexecdir),
  '-DMATE_SETTINGS_PLUGINDIR="@0@"'.format(plugindir),
  '-DBINDIR="@0@"'.format(join_paths(prefix, get_option('bindir')))
], language: 'c')

configure_file(
  output: 'config.h',
  configuration: conf
)

include_dirs = [
  include_directories('.'),
  include_directories('mate-settings-daemon'),
  include_directories('plugins/common'),
]

# Generate helper variables for GSettings schemas and gettext
glib_compile_resources = find_program('glib-compile-resources')
gdbus_codegen = find_program('gdbus-codegen')
glib_genmarshal = find_program('glib-genmarshal')
glib_mkenums = find_program('glib-mkenums')

subdir('po')
subdir('mate-settings-daemon')
subdir('plugins')
subdir('data')
