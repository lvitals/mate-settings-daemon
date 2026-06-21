libmsd_profile = static_library('msd-profile',
  [
    'mate-settings-profile.c',
    'mate-settings-profile.h'
  ],
  include_directories: include_dirs,
  dependencies: [glib_dep, gio_dep, gtk_dep]
)

msd_sources = [
  'main.c',
  'mate-settings-bus.c',
  'mate-settings-manager.c',
  'mate-settings-plugin.c',
  'mate-settings-plugin-info.c',
  'mate-settings-module.c'
]

msd_deps = [
  glib_dep, gio_dep, gio_unix_dep, gtk_dep, gmodule_dep, gthread_dep,
  dconf_dep, mate_desktop_dep, x11_dep
]

if have_libnotify
  msd_deps += libnotify_dep
endif

executable('mate-settings-daemon',
  msd_sources,
  include_directories: include_dirs,
  dependencies: msd_deps,
  link_with: libmsd_profile,
  export_dynamic: true,
  install: true,
  install_dir: libexecdir
)

install_headers(
  'mate-settings-plugin.h',
  subdir: 'mate-settings-daemon'
)
