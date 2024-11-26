`adb version`

<br>

`adb devices`

<br>

`adb shell pm uninstall -k --user 0 com.nearme.instant.platform`

`adb shell pm uninstall -k --user 0 com.heytap.htms`

`adb shell pm uninstall -k --user 0 com.opos.ads`

<br>

`adb shell pm disable-user com.nearme.instant.platform`

`adb shell pm disable-user com.heytap.htms`

`adb shell pm disable-user com.opos.ads`

<br>

`adb shell pm suspend com.nearme.instant.platform`

`adb shell pm suspend com.heytap.htms`

`adb shell pm suspend com.opos.ads`

<br>

`adb shell exit`

<br>

`adb kill-server`
