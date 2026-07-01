NULL =

enabled_plugins =	\
	a11y-settings	\
	background	\
	datetime	\
	dummy		\
	housekeeping	\
	mpris		\
	sound		\
	typing-break	\
	$(NULL)

disabled_plugins = $(NULL)

if BUILD_X11_PLUGINS
enabled_plugins +=	\
	a11y-keyboard	\
	clipboard	\
	keybindings	\
	keyboard	\
	media-keys	\
	mouse		\
	xrandr		\
	xrdb		\
	xsettings	\
	$(NULL)
else
disabled_plugins +=	\
	a11y-keyboard	\
	clipboard	\
	keybindings	\
	keyboard	\
	media-keys	\
	mouse		\
	xrandr		\
	xrdb		\
	xsettings	\
	$(NULL)
endif

if SMARTCARD_SUPPORT
enabled_plugins += smartcard
else
disabled_plugins += smartcard
endif

if BUILD_RFKILL
enabled_plugins += rfkill
else
disabled_plugins += rfkill
endif

SUBDIRS = common $(enabled_plugins)
DIST_SUBDIRS = $(SUBDIRS) $(disabled_plugins)

-include $(top_srcdir)/git.mk
