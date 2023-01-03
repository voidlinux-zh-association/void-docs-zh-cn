# 图标

## GTK

默认情况下，基于 GTK 的应用程序会尝试使用 Adwaita 图标主题作为应用程序的图标。因此，安装 `gtk+3` 软件包也将安装 `adwaita-icon-theme` 软件包。如果你想使用不同的主题，请安装相关的软件包，然后在 `/etc/gtk-3.0/settings.ini` 或 `~/.config/gtk-3.0/settings.ini` 中指定该主题。在[忽略](../../xbps/advanced-usage.md#ignoring-packages)软件包后，可以删除 `adwaita-icon-theme` 。

关于如何在中指定不同的 GTK 图标主题的信息 `settings.ini` ，请参考[GtkSettings 文档](https://developer.gnome.org/gtk3/stable/GtkSettings.html#GtkSettings.properties)，特别是 "[gtk-icon-theme-name](https://developer.gnome.org/gtk3/stable/GtkSettings.html#GtkSettings--gtk-icon-theme-name)" 属性。 
