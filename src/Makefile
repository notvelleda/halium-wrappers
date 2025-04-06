TRIPLET ?= $(shell gcc -dumpmachine)
DESTDIR ?= /

PACKAGES = libandroid-properties
GLESSHADERCACHE_PACKAGES = glesv2 gl

CFLAGS = -O2 $(shell pkg-config --cflags $(PACKAGES))
LIBS = $(shell pkg-config --libs $(PACKAGES))
GLESSHADERCACHE_LIBS = $(shell pkg-config --libs $(GLESSHADERCACHE_PACKAGES)) -lz

SYMLINKS = \
	android_bootctl \
	android_logcat \
	android_lshal \
	android_getprop \
	android_setprop \
	android_reboot \
	android_stop \
	android_start \
	android_kill \
	logcat \
	lshal

all: waitforservice libtls-padding.so

waitforservice: waitforservice.c
	# hybris-common lacks a proper pkg-config definition
	$(CC) $(CFLAGS) $< -o $@ $(LIBS) -lhybris-common

libtls-padding.so: libtls-padding.c
	$(CC) -shared $< -o $@

libgtk6216workaround.so: gtk6216workaround.c
	$(CC) -shared $< -o $@

libglesshadercache.so: glesshadercache.c
	$(CC) -fPIC -shared $< -o $@ $(GLESSHADERCACHE_LIBS)

install: waitforservice libtls-padding.so libglesshadercache.so libgtk6216workaround.so halium-lxc-exec.sh android-service.sh android-hwcomposer/run android-hwcomposer/finish
	install -d $(DESTDIR)/usr/bin
	install -d $(DESTDIR)/usr/lib/halium-wrappers
	install -d $(DESTDIR)/usr/lib/$(TRIPLET)
	install -d $(DESTDIR)/etc/sv/android-hwcomposer
	install -m 755 waitforservice $(DESTDIR)/usr/bin
	install -m 4644 libtls-padding.so $(DESTDIR)/usr/lib/$(TRIPLET)
	install -m 4644 libgtk6216workaround.so $(DESTDIR)/usr/lib/$(TRIPLET)
	install -m 4644 libglesshadercache.so $(DESTDIR)/usr/lib/$(TRIPLET)
	install -m 755 halium-lxc-exec.sh $(DESTDIR)/usr/lib/halium-wrappers
	install -m 755 android-service.sh $(DESTDIR)/usr/lib/halium-wrappers
	install -m 755 android-hwcomposer/run $(DESTDIR)/etc/sv/android-hwcomposer
	install -m 755 android-hwcomposer/finish $(DESTDIR)/etc/sv/android-hwcomposer

	for link in $(SYMLINKS); do \
		ln -s /usr/lib/halium-wrappers/halium-lxc-exec.sh $(DESTDIR)/usr/bin/$${link}; \
	done

.PHONY: all install
