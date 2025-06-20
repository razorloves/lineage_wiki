{%- assign device = site.data.devices[page.device] -%}
{% if device.custom_recovery_codename %}
{% assign custom_recovery_codename = device.custom_recovery_codename %}
{% else %}
{% assign custom_recovery_codename = device.codename %}
{% endif %}

## Rooting your device

{% include alerts/important.html content="The device must be rooted before proceeding any further." %}

{% case device.root_method[0] %}
{% when 'custom' %}
1. Root your device by following [this]({{ device.root_method[1] }}) guide.
{% when 'kingroot' %}
1. Download KingRoot from [here](https://kingrootapp.net/).
   1. Install and run the apk to achieve root. Ensure you have a working Internet connection.
{% endcase %}

{% include snippets/before_recovery_install.md %}

{%- capture install_content %}
{%- if device.custom_recovery_link %}
{%- assign is_lineage_recovery = device.custom_lineage_recovery %}
1. Download a custom recovery - you can download one [here]({{ device.custom_recovery_link }}).
{%- elsif device.uses_twrp %}
1. Download a custom recovery - you can download [TWRP](https://dl.twrp.me/{{ custom_recovery_codename }}). Simply download the latest recovery file, named something like `twrp-x.x.x-x-{{ custom_recovery_codename }}.img`.
{%- elsif device.maintainers != empty %}
{%- assign is_lineage_recovery = true %}
1. Download [Lineage Recovery](https://download.lineageos.org/devices/{{ custom_recovery_codename }}). Simply download the latest recovery file, named `{{ device.recovery_partition_name }}.img`.
{%- else %}
1. [Build]({{ device | device_link: "/build" | relative_url }}) a LineageOS installation package. The recovery will be built as part of it!
{%- endif %}
2. Place the recovery image file on the root of `/sdcard`:
   * Using adb: `adb -d push <recovery_filename>.img /sdcard/<recovery_filename>.img`
    {% include alerts/tip.html content="The file may not be named identically to what stands in this command, so adjust accordingly." %}
   * You can use any method you are comfortable with. `adb` is universal across all devices, and works both in Android and recovery mode, providing USB debugging is enabled.
3. Now, open an `adb -d shell` from a command prompt (on Windows) or terminal (on Linux or macOS) window. In that shell, type the following commands:
{%- if device.is_ab_device %}
```
su
dd if=/sdcard/<recovery_filename>.img of={{ device.partition_path }}{{ device.recovery_partition_name }}_a
dd if=/sdcard/<recovery_filename>.img of={{ device.partition_path }}{{ device.recovery_partition_name }}_b
```
{%- else %}
```
su
dd if=/sdcard/<recovery_filename>.img of={{ device.partition_path }}{{ device.recovery_partition_name }}
```
{%- endif %}
4. Reboot into recovery.
    * From the same shell, type the following command:
```
reboot recovery
```
{%- endcapture %}

{%- if is_lineage_recovery %}
## Installing Lineage Recovery using `dd`
{%- else %}
## Installing a custom recovery using `dd`
{%- endif %}
{{ install_content }}
