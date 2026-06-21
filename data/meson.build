# GSettings Schemas
gsettings_schemas_in = [
  'org.mate.applications-at.gschema.xml.in',
  'org.mate.font-rendering.gschema.xml.in',
  'org.mate.peripherals-keyboard.gschema.xml.in',
  'org.mate.peripherals-mouse.gschema.xml.in',
  'org.mate.peripherals-smartcard.gschema.xml.in',
  'org.mate.peripherals-touchpad.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.a11y-keyboard.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.a11y-settings.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.background.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.clipboard.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.datetime.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.housekeeping.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.keybindings.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.keyboard.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.media-keys.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.mpris.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.mouse.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.rfkill.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.smartcard.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.sound.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.typing-break.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.xrandr.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.xrdb.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.xsettings.gschema.xml.in',
  'org.mate.SettingsDaemon.plugins.gschema.xml.in',
]

schema_conf = configuration_data()
schema_conf.set('GETTEXT_PACKAGE', 'mate-settings-daemon')

gsettings_schemas = []
foreach schema : gsettings_schemas_in
  # Output file name by stripping '.in'
  schema_out = schema.split('.in')[0]
  gsettings_schemas += configure_file(
    input: schema,
    output: schema_out,
    configuration: schema_conf,
    install: true,
    install_dir: join_paths(datadir, 'glib-2.0', 'schemas')
  )
endforeach

# Compile schemas during build
gnome.compile_schemas(
  depend_files: gsettings_schemas
)

# Desktop autostart file
desktop_conf = configuration_data()
desktop_conf.set('LIBEXECDIR', libexecdir)
desktop_in = configure_file(
  input: 'mate-settings-daemon.desktop.in.in',
  output: 'mate-settings-daemon.desktop.in',
  configuration: desktop_conf
)

i18n.merge_file(
  input: desktop_in,
  output: 'mate-settings-daemon.desktop',
  type: 'desktop',
  po_dir: join_paths(meson.project_source_root(), 'po'),
  install: true,
  install_dir: join_paths(get_option('sysconfdir'), 'xdg', 'autostart')
)

# DBus service file
service_conf = configuration_data()
service_conf.set('libexecdir', libexecdir)
configure_file(
  input: 'org.mate.SettingsDaemon.service.in',
  output: 'org.mate.SettingsDaemon.service',
  configuration: service_conf,
  install: true,
  install_dir: join_paths(datadir, 'dbus-1', 'services')
)

# Pkg-config file
pc_conf = configuration_data()
pc_conf.set('prefix', prefix)
pc_conf.set('exec_prefix', prefix)
pc_conf.set('libdir', libdir)
pc_conf.set('includedir', join_paths(prefix, get_option('includedir')))
pc_conf.set('libexecdir', libexecdir)
pc_conf.set('VERSION', meson.project_version())
configure_file(
  input: 'mate-settings-daemon.pc.in',
  output: 'mate-settings-daemon.pc',
  configuration: pc_conf,
  install: true,
  install_dir: join_paths(get_option('libdir'), 'pkgconfig')
)

# Man pages
install_man(
  'mate-settings-daemon.1',
  'msd-datetime-mechanism.1',
  'msd-locate-pointer.1'
)

# Accessibility XML (Keybindings)
i18n.merge_file(
  input: '50-accessibility.xml.in',
  output: '50-accessibility.xml',
  type: 'xml',
  po_dir: join_paths(meson.project_source_root(), 'po'),
  data_dirs: meson.project_source_root(),
  install: true,
  install_dir: join_paths(datadir, 'mate-control-center', 'keybindings')
)
