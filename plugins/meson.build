libcommon = static_library('common',
  [
    'common/eggaccelerators.c',
    'common/msd-keygrab.c',
    'common/msd-input-helper.c',
    'common/msd-osd-window.c',
  ],
  include_directories: include_dirs,
  dependencies: [glib_dep, gio_dep, gtk_dep, x11_dep, xinput_dep]
)

plugins = {
  'a11y-keyboard': {
    'sources': ['a11y-keyboard/msd-a11y-keyboard-plugin.c', 'a11y-keyboard/msd-a11y-keyboard-manager.c', 'a11y-keyboard/msd-a11y-keyboard-atspi.c', 'a11y-keyboard/msd-a11y-preferences-dialog.c'],
    'deps': [glib_dep, gio_dep, gtk_dep, x11_dep]
  },
  'a11y-settings': {
    'sources': ['a11y-settings/msd-a11y-settings-plugin.c', 'a11y-settings/msd-a11y-settings-manager.c'],
    'deps': [glib_dep, gio_dep, gtk_dep]
  },
  'background': {
    'sources': ['background/msd-background-plugin.c', 'background/msd-background-manager.c'],
    'deps': [glib_dep, gio_dep, gtk_dep, mate_desktop_dep]
  },
  'clipboard': {
    'sources': ['clipboard/msd-clipboard-plugin.c', 'clipboard/msd-clipboard-manager.c', 'clipboard/list.c', 'clipboard/xutils.c'],
    'deps': [glib_dep, gio_dep, gtk_dep, x11_dep]
  },
  'housekeeping': {
    'sources': ['housekeeping/msd-housekeeping-plugin.c', 'housekeeping/msd-housekeeping-manager.c', 'housekeeping/msd-disk-space.c', 'housekeeping/msd-ldsm-dialog.c', 'housekeeping/msd-ldsm-trash-empty.c'],
    'deps': [glib_dep, gio_dep, gtk_dep]
  },
  'keybindings': {
    'sources': ['keybindings/msd-keybindings-plugin.c', 'keybindings/msd-keybindings-manager.c', 'keybindings/dconf-util.c'],
    'deps': [glib_dep, gio_dep, gtk_dep, mate_desktop_dep]
  },
  'keyboard': {
    'sources': ['keyboard/msd-keyboard-plugin.c', 'keyboard/msd-keyboard-manager.c', 'keyboard/msd-keyboard-xkb.c', 'keyboard/delayed-dialog.c'],
    'deps': [glib_dep, gio_dep, gtk_dep, libmatekbd_dep, libmatekbdui_dep, libxklavier_dep, mate_desktop_dep]
  },
  'mouse': {
    'sources': ['mouse/msd-mouse-plugin.c', 'mouse/msd-mouse-manager.c', 'mouse/msd-locate-pointer.c', 'mouse/msd-timeline.c'],
    'deps': [glib_dep, gio_dep, gtk_dep, x11_dep]
  },
  'mpris': {
    'sources': ['mpris/msd-mpris-plugin.c', 'mpris/msd-mpris-manager.c', 'mpris/bus-watch-namespace.c'],
    'deps': [glib_dep, gio_dep, gtk_dep]
  },
  'xrdb': {
    'sources': ['xrdb/msd-xrdb-plugin.c', 'xrdb/msd-xrdb-manager.c'],
    'deps': [glib_dep, gio_dep, gtk_dep, x11_dep]
  },
  'xsettings': {
    'sources': ['xsettings/msd-xsettings-plugin.c', 'xsettings/msd-xsettings-manager.c', 'xsettings/xsettings-common.c', 'xsettings/xsettings-manager.c', 'xsettings/fontconfig-monitor.c', 'xsettings/wm-common.c'],
    'deps': [glib_dep, gio_dep, gtk_dep, fontconfig_dep, x11_dep]
  },
  'xrandr': {
    'sources': ['xrandr/msd-xrandr-plugin.c', 'xrandr/msd-xrandr-manager.c'],
    'deps': [glib_dep, gio_dep, gtk_dep, mate_desktop_dep, x11_dep]
  },
  'typing-break': {
    'sources': ['typing-break/msd-typing-break-plugin.c', 'typing-break/msd-typing-break-manager.c'],
    'deps': [glib_dep, gio_dep, gtk_dep]
  }
}

if have_smartcard
  plugins += {
    'smartcard': {
      'sources': ['smartcard/msd-smartcard-plugin.c', 'smartcard/msd-smartcard-manager.c', 'smartcard/msd-smartcard.c'],
      'deps': [glib_dep, gio_dep, gtk_dep, nss_dep]
    }
  }
endif

if have_rfkill
  plugins += {
    'rfkill': {
      'sources': ['rfkill/msd-rfkill-plugin.c', 'rfkill/msd-rfkill-manager.c', 'rfkill/rfkill-glib.c'],
      'deps': [glib_dep, gio_dep, gtk_dep]
    }
  }
endif

if have_libmatemixer
  plugins += {
    'sound': {
      'sources': ['sound/msd-sound-plugin.c', 'sound/msd-sound-manager.c'],
      'deps': [glib_dep, gio_dep, gtk_dep, libmatemixer_dep]
    }
  }
endif

# Media Keys plugin
media_keys_marshal = gnome.genmarshal('msd-marshal',
  sources: 'media-keys/msd-marshal.list',
  prefix: 'msd_marshal'
)
media_keys_sources = [
  'media-keys/msd-media-keys-plugin.c',
  'media-keys/msd-media-keys-manager.c',
  'media-keys/msd-media-keys-window.c',
  media_keys_marshal
]
media_keys_deps = [glib_dep, gio_dep, gtk_dep, libm]
if have_libmatemixer
  media_keys_deps += libmatemixer_dep
endif
if have_canberra
  media_keys_deps += canberra_dep
endif

shared_module('media-keys',
  media_keys_sources,
  include_directories: include_dirs + [include_directories('media-keys')],
  dependencies: media_keys_deps,
  link_with: libcommon,
  install: true,
  install_dir: plugindir
)

i18n.merge_file(
  input: 'media-keys/media-keys.mate-settings-plugin.desktop.in',
  output: 'media-keys.mate-settings-plugin',
  type: 'desktop',
  po_dir: '../po',
  install: true,
  install_dir: plugindir
)

# Datetime helper
if have_polkit
  datetime_codegen = gnome.gdbus_codegen('msd-datetime-generated',
    'datetime/msd-datetime-mechanism.xml',
    interface_prefix: 'org.mate.SettingsDaemon.',
    namespace: 'MateSettings'
  )
  
  executable('msd-datetime-mechanism',
    [
      'datetime/msd-datetime-mechanism.c',
      'datetime/msd-datetime-mechanism-main.c',
      'datetime/system-timezone.c',
      datetime_codegen
    ],
    include_directories: include_dirs,
    dependencies: [glib_dep, gio_dep, gtk_dep, polkit_dep],
    install: true,
    install_dir: libexecdir
  )
  
  # DBus service file
  datetime_service_conf = configuration_data()
  datetime_service_conf.set('LIBEXECDIR', libexecdir)
  configure_file(
    input: 'datetime/org.mate.SettingsDaemon.DateTimeMechanism.service.in',
    output: 'org.mate.SettingsDaemon.DateTimeMechanism.service',
    configuration: datetime_service_conf,
    install: true,
    install_dir: join_paths(get_option('datadir'), 'dbus-1/system-services')
  )
  
  # Polkit policy file
  i18n.merge_file(
    input: 'datetime/org.mate.settingsdaemon.datetimemechanism.policy.in',
    output: 'org.mate.settingsdaemon.datetimemechanism.policy',
    type: 'xml',
    po_dir: '../po',
    install: true,
    install_dir: join_paths(get_option('datadir'), 'polkit-1/actions')
  )
endif

# Iterate simple plugins
foreach name, info : plugins
  sources = []
  foreach src : info['sources']
    sources += files(src)
  endforeach
  
  shared_module(name,
    sources,
    include_directories: include_dirs + [include_directories(name)],
    dependencies: info['deps'],
    install: true,
    install_dir: plugindir
  )
  
  i18n.merge_file(
    input: '@0@/@0@.mate-settings-plugin.desktop.in'.format(name),
    output: '@0@.mate-settings-plugin'.format(name),
    type: 'desktop',
    po_dir: '../po',
    install: true,
    install_dir: plugindir
  )
endforeach
